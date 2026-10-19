# Servicios Core del Dominio (CartService, OrderService, AuditoriaService)

> **Ubicación:** `docs/webada/02-modulos/servicios-core.md`  
> **Módulo:** Lógica de Negocio y Dominio  
> **Fuente de Verdad:** `app/Services/CartService.php`, `app/Services/OrderService.php`, `app/Services/AuditoriaService.php`

---

## 1. Visión General de la Capa de Servicios

La capa de servicios encapsula las reglas de negocio más críticas de **Filam Centroplas**, desacoplando la persistencia en base de datos y la gestión de sesiones HTTP de los componentes visuales de Livewire y controladores. 

Los tres servicios nucleares son:
1. **`CartService`:** Orquesta el estado volátil del carrito en sesión, calcula subtotales matemáticos y aplica el motor de políticas de descuento mayoristas B2B.
2. **`OrderService`:** Garantiza la persistencia atómica transaccional de pedidos bajo condiciones de alta concurrencia mediante *advisory locks* de PostgreSQL, congela instantáneas comerciales (*price freezing*) y emite comprobantes internos de control correlacionados.
3. **`AuditoriaService`:** Proporciona trazabilidad inmutable y forense sobre eventos sensibles del sistema (cambios de estado, manipulaciones de catálogo y accesos).

---

## 2. `CartService` (`app/Services/CartService.php`)

### 2.1. Arquitectura de Almacenamiento en Sesión
El carrito se almacena de forma ligera en la sesión del cliente (`session()->get('cart', [])`) bajo la clave privada `private const SESSION_KEY = 'cart'`.

La estructura interna es un diccionario asociativo:
```php
/** @var array<int, int> $cart [id_producto => cantidad] */
[
    1 => 10,  // id_producto: 1, cantidad: 10
    4 => 2,   // id_producto: 4, cantidad: 2
]
```
Este diseño minimiza el tamaño de la cookie de sesión y el almacenamiento en Redis, delegando la hidratación de precios, nombres e imágenes a consultas puntuales contra PostgreSQL.

---

### 2.2. Diagrama de Flujo del Motor de Carrito y Descuentos

```mermaid
flowchart TD
    subgraph S1["1. ALMACENAMIENTO EN SESIÓN"]
        SES["session('cart', [])"]
        OPS["Estructura: [ id_producto => cantidad ]<br/>Operaciones: add(), update(), remove(), clear(), count()"]
        SES --> OPS
    end

    subgraph S2["2. HIDRATACIÓN DESDE BASE DE DATOS"]
        DBQ["Producto::query()->whereIn('id_producto', $ids)->get()"]
        MAP["Mapeo a colección de items:<br/>• producto (Modelo Eloquent)<br/>• cantidad (int)<br/>• subtotal_linea = round(precio_base * cantidad, 2)"]
        DBQ --> MAP
    end

    subgraph S3["3. CÁLCULO DE SUBTOTAL BRUTO"]
        SUB["subtotal() = round(∑ subtotal_lineas, 2)"]
    end

    subgraph S4["4. MOTOR DE DESCUENTOS B2B"]
        direction TB
        D1{"¿$cliente === null?"}
        D1_YES["Descuento = 0.00"]
        D2["Consultar $cliente->descuentosVigentes()->first()"]
        D3{"¿Existe política activa?"}
        D3_NO["Descuento = 0.00"]
        D4{"¿subtotal < monto_minimo?"}
        D4_YES["Descuento = 0.00 (No alcanza umbral)"]
        D5{"tipo_descuento"}
        F_PORC["porcentaje:<br/>subtotal * (valor / 100)"]
        F_FIJO["monto_fijo:<br/>(float) valor"]
        CAP["Techo máximo:<br/>round(min(descuento, subtotal), 2)"]

        D1 -->|Sí| D1_YES
        D1 -->|No| D2 --> D3
        D3 -->|No| D3_NO
        D3 -->|Sí| D4
        D4 -->|Sí| D4_YES
        D4 -->|No| D5
        D5 -->|porcentaje| F_PORC --> CAP
        D5 -->|monto_fijo| F_FIJO --> CAP
    end

    subgraph S5["5. TOTAL NETO DEFINITIVO"]
        TOT["total(?Cliente $cliente) = round(subtotal - descuento, 2)"]
    end

    OPS -->|"CartService::items()"| DBQ
    MAP --> SUB
    SUB --> D1
    CAP --> TOT
    D1_YES & D3_NO & D4_YES --> TOT
```

---

### 2.3. Catálogo de Métodos y Firmas

#### `raw(): array<int, int>`
- **Firma:** `public function raw(): array`
- **Descripción:** Retorna el array primitivo de sesión con los IDs de producto y sus cantidades.

#### `add(int $idProducto, int $cantidad = 1): void`
- **Firma:** `public function add(int $idProducto, int $cantidad = 1): void`
- **Comportamiento:** Si el producto ya existe en sesión, suma `$cantidad`; si no existe, lo inicializa. Aplica `max(1, ...)` para evitar cantidades negativas o cero.

#### `update(int $idProducto, int $cantidad): void`
- **Firma:** `public function update(int $idProducto, int $cantidad): void`
- **Comportamiento:** Si `$cantidad <= 0`, remueve la clave del array (`unset`); en caso contrario, fija el nuevo valor numérico.

#### `remove(int $idProducto): void`
- **Firma:** `public function remove(int $idProducto): void`
- **Comportamiento:** Elimina definitivamente el producto del carrito en sesión.

#### `clear(): void`
- **Firma:** `public function clear(): void`
- **Comportamiento:** Invoca `session()->forget(self::SESSION_KEY)`.

#### `count(): int`
- **Firma:** `public function count(): int`
- **Comportamiento:** Retorna la suma total de unidades físicas (`array_sum($this->raw())`).

#### `items(): Collection`
- **Firma:** `public function items(): Collection`
- **Retorno:** `Collection<int, array{producto: Producto, cantidad: int, subtotal: float}>`
- **Lógica de Hidratación:**
  1. Si el array de sesión está vacío, retorna `collect()`.
  2. Consulta la base de datos por los productos activos: `Producto::query()->whereIn('id_producto', array_keys($cart))->get()->keyBy('id_producto')`.
  3. Mapea cada elemento descartando productos eliminados o no encontrados (`filter()`).
  4. Calcula el subtotal de línea redondeado a 2 decimales: `round((float) $producto->precio_base * $cantidad, 2)`.

#### `subtotal(): float`
- **Firma:** `public function subtotal(): float`
- **Fórmula:** $\text{subtotal} = \text{round}\left(\sum_{i=1}^{n} \text{subtotal\_linea}_i, 2\right)$

#### `descuento(?Cliente $cliente): float`
- **Firma:** `public function descuento(?Cliente $cliente): float`
- **Motor de Descuentos Preferenciales B2B:**
  1. Si `$cliente` es `null`, retorna `0.0`.
  2. Consulta la asignación de descuento activa mediante el scope `$cliente->descuentosVigentes()->first()`.
  3. Si no existe asignación o la política vinculada está inactiva, retorna `0.0`.
  4. **Validación de Umbral Mínimo:** Si la política define `monto_minimo` y $\text{subtotal} < \text{monto\_minimo}$, el descuento se invalida y retorna `0.0`.
  5. **Aplicación de Fórmula según Tipo:**
     - Si `tipo_descuento === 'porcentaje'`:
       $$\text{descuento} = \text{subtotal} \times \left(\frac{\text{valor}}{100}\right)$$
     - Si `tipo_descuento === 'monto_fijo'`:
       $$\text{descuento} = \text{valor}$$
  6. **Salvaguarda de Techo:** El descuento nunca puede superar el subtotal:
     $$\text{descuento\_final} = \text{round}(\min(\text{descuento}, \text{subtotal}), 2)$$

#### `total(?Cliente $cliente): float`
- **Firma:** `public function total(?Cliente $cliente): float`
- **Fórmula:** $\text{total} = \text{round}(\text{subtotal}() - \text{descuento}(\$cliente), 2)$

---

## 3. `OrderService` (`app/Services/OrderService.php`)

### 3.1. Arquitectura Transaccional y Concurrencia
El proceso de checkout y confirmación de pedidos es una operación de alta criticidad financiera. `OrderService` resuelve dos desafíos arquitectónicos esenciales:
1. **Atomicidad ACID:** Si falla la inserción de las líneas congeladas o el comprobante interno, se ejecuta un rollback total mediante `DB::transaction`.
2. **Eliminación de Condiciones de Carrera (*Race Conditions*):** Para evitar que dos clientes concurrentes obtengan el mismo correlativo `PED-AAAA-NNNNN` en el mismo milisegundo, se utiliza un bloqueo consultivo exclusivo a nivel de transacción en PostgreSQL:
   ```php
   if (DB::connection()->getDriverName() === 'pgsql') {
       DB::select('SELECT pg_advisory_xact_lock(?)', [20261006]);
   }
   ```
   El identificador numérico `20261006` serializa las solicitudes de generación de correlativo sin bloquear lecturas generales en la tabla `pedidos`.

---

### 3.2. Diagrama de Flujo Transaccional

```mermaid
flowchart TD
    START(["Llamada a OrderService::crear($cliente, $items, $obs)"]) --> T1

    subgraph TX["Transacción ACID (DB::transaction)"]
        direction TB
        T1["1. Cálculo de Montos Financieros<br/>• $subtotal = $this->cart->subtotal()<br/>• $descuento = $this->cart->descuento($cliente)<br/>• $total = round($subtotal - $descuento, 2)"]
        
        T2["2. Bloqueo Consultivo PostgreSQL<br/>SELECT pg_advisory_xact_lock(20261006)<br/>Serializa asignación de correlativo sin colisiones"]
        
        T3["3. Inserción de Pedido Maestro<br/>• codigo_correlativo: PED-AAAA-NNNNN<br/>• estado_actual: 'pendiente'<br/>• fecha_emision: now()"]
        
        T4["4. Inserción de DetallePedido (Price Freezing)<br/>• nombre_comercial_congelado<br/>• sku_congelado<br/>• precio_unitario_congelado<br/>• subtotal_linea"]
        
        T5["5. Generación de ComprobanteInterno<br/>• serie: NP01<br/>• numero_comprobante: CI-AAAA-NNNNN<br/>• estado: 'emitido'<br/>• leyenda: NO VALIDO COMO COMPROBANTE TRIBUTARIO"]
        
        T6["6. Disparo de Eventos y Auditoría<br/>• Mailable PedidoCliente (al comprador)<br/>• Mailable PedidoOperativo (a ventas)<br/>• AuditoriaService::registrar('pedidos', ...)"]
        
        T7["7. Limpieza de Sesión<br/>CartService::clear()"]

        T1 --> T2 --> T3 --> T4 --> T5 --> T6 --> T7
    end

    T7 --> COMMIT(["Commit Transaccional y Retorno de Instancia Pedido"])
```

---

### 3.3. Catálogo de Métodos y Firmas

#### `crear(Cliente $cliente, array $items, ?string $observacionesEntrega = null): Pedido`
- **Firma:** `public function crear(Cliente $cliente, array $items, ?string $observacionesEntrega = null): Pedido`
- **Parámetros:**
  - `Cliente $cliente`: Instancia del cliente comercial titular.
  - `array $items`: Colección de items retornados por `CartService::items()`.
  - `?string $observacionesEntrega`: Indicaciones de logística y transporte en obra.
- **Retorno:** Instancia del modelo `Pedido` con relaciones cargadas (`loadMissing(['detalles', 'cliente', 'comprobante'])`).
- **Secuencia de Pasos Transaccionales:**
  1. Inicia `DB::transaction(...)`.
  2. Calcula montos: `$subtotal`, `$descuento = $this->cart->descuento($cliente)` y `$total = round($subtotal - $descuento, 2)`.
  3. Ejecuta `siguienteCorrelativo()` bajo advisory lock.
  4. Crea `Pedido::create([...])` con `estado_actual = 'pendiente'` y `fecha_emision = now()`.
  5. **Bucle de Congelamiento Comercial (*Price Freezing*):**
     Para cada item, inserta en `detalle_pedidos`:
     - `nombre_comercial_congelado = $item['producto']->nombre`
     - `precio_unitario_congelado = $item['producto']->precio_base`
     - `subtotal_linea = $item['subtotal']`
  6. Inserta en `historial_estados_pedido`:
     - `estado = 'pendiente'`, `notas_bitacora = 'Pedido registrado por el cliente.'`, `fecha_cambio = now()`.
  7. Emite 1 a 1 el registro en `comprobantes_internos`:
     - `numero_comprobante = str_replace('PED-', 'CI-', $pedido->codigo_correlativo)`
     - `fecha_emision = now()`
     - `leyenda_legal = 'DOCUMENTO DE CONTROL INTERNO - NO VALIDO PARA FINES TRIBUTARIOS'`
  8. Finaliza la transacción y retorna el pedido completo.

#### `siguienteCorrelativo(): string` (Privado)
- **Firma:** `private function siguienteCorrelativo(): string`
- **Lógica:**
  - Obtiene el año actual (`now()->year`).
  - Define el prefijo anual: `PED-AAAA-`.
  - Ejecuta advisory lock en PostgreSQL (`SELECT pg_advisory_xact_lock(20261006)`).
  - Consulta: `Pedido::query()->where('codigo_correlativo', 'like', $prefijo.'%')->max('codigo_correlativo')`.
  - Extrae los últimos 5 dígitos numéricos, suma 1 y formatea a 5 dígitos con ceros a la izquierda (`str_pad((string) $secuencia, 5, '0', STR_PAD_LEFT)`).
  - Retorno: E.g. `PED-2026-00001`.

---

## 4. `AuditoriaService` (`app/Services/AuditoriaService.php`)

### 4.1. Responsabilidad y Trazabilidad Forense
Centraliza el registro inmutable de acciones administrativas y transaccionales para auditoría de seguridad y resolución de controversias operativas.

### 4.2. Firma y Parámetros

```php
public function registrar(
    string $entidad,
    ?int $idRegistro,
    string $accion,
    ?string $estadoPrevio = null
): void
```

- **Parámetros:**
  - `string $entidad`: Nombre de la tabla afectada (e.g. `'pedidos'`, `'productos'`).
  - `?int $idRegistro`: Identificador del registro modificado.
  - `string $accion`: Verbo de la operación realizada (e.g. `'cambio_estado'`, `'guardar'`, `'eliminar'`).
  - `?string $estadoPrevio`: Snapshot o estado anterior del objeto antes de la modificación (e.g. `'pendiente'`).

### 4.3. Implementación Interna
```php
RegistroAuditoria::create([
    'id_usuario' => auth()->id(),
    'entidad' => $entidad,
    'id_registro' => $idRegistro,
    'accion' => $accion,
    'estado_previo' => $estadoPrevio,
    'direccion_ip' => request()->ip(),
    'fecha_evento' => now(),
]);
```
- La tabla `registro_auditoria` tiene `$timestamps = false` y persiste marcas `timestamptz` inmutables.
- Captura de forma transparente la dirección IP del operador (`request()->ip()`) y el ID del usuario en sesión (`auth()->id()`), soportando llamadas desatendidas (`null` para procesos de sistema).
