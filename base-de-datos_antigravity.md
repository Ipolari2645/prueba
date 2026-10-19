# Modelo de Base de Datos, Migraciones y Entidad-Relación

> **Ubicación:** `docs/webada/01-arquitectura/base-de-datos.md`  
> **Módulo:** Arquitectura de Datos  
> **Motor:** PostgreSQL 16+ / 18 (`DB_CONNECTION=pgsql`)  
> **Fuente de Verdad:** Migraciones en `database/migrations/` y 19 Modelos en `app/Models/`

---

## 1. Características Técnicas del Motor de Persistencia

La capa de datos está construida sobre **PostgreSQL**, utilizando características avanzadas del motor para garantizar integridad referencial, consistencia transaccional y cumplimiento legal:

1. **Extensión `citext`:**
   - Habilitada en `2026_10_06_100001_enable_citext_extension.php` mediante `CREATE EXTENSION IF NOT EXISTS citext`.
   - Modifica la columna `email` de la tabla `users` a tipo `citext` (`ALTER TABLE users ALTER COLUMN email TYPE citext`).
   - **Beneficio:** Garantiza que los correos sean únicos e insensibles a mayúsculas y minúsculas (`pedro@filam.pe` es idéntico a `PEDRO@FILAM.PE`) sin necesidad de llamar funciones `LOWER()` en cada consulta ni crear índices funcionales redundantes.
2. **Timestamps con Huso Horario (`timestamptz`):**
   - Todas las marcas temporales del sistema (`created_at`, `updated_at`, `deleted_at`, `fecha_emision`, `fecha_cambio`, `fecha_evento`, `aceptado_en`, etc.) utilizan `timestampTz()` / `timestamptz`.
   - **Beneficio:** Preserva el instante universal absoluto (UTC), permitiendo conversiones precisas a la zona horaria del negocio (`America/Lima`) sin desfases por horario de verano o diferencias de servidor.
3. **Borrado Lógico (*Soft Deletes* con Zona Horaria):**
   - Implementado mediante `softDeletesTz()` (`deleted_at timestamptz`) en las entidades críticas del catálogo y usuarios: `users`, `clientes`, `categorias` y `productos`.
4. **Restricciones CHECK Nativas:**
   - Restricciones declaradas a nivel de catálogo de PostgreSQL para impedir estados inválidos, incluso si se ejecutan consultas directas fuera del ORM:
     - `clientes`: `tipo_documento IN ('DNI', 'RUC')`.
     - `politicas_descuento`: `tipo_descuento IN ('porcentaje', 'monto_fijo')`.
     - `productos`: `stock_estado IN ('disponible', 'stock_bajo', 'agotado_por_consultar')` y `precio_base >= 0`.
     - `pedidos`: `estado_actual IN ('pendiente', 'en_revision', 'confirmado', 'atendido', 'cancelado')`.
     - `solicitudes_stock`: `estado IN ('pendiente', 'en_atencion', 'atendida')`.
     - `reclamaciones`: `tipo_documento IN ('DNI', 'CE', 'RUC')`, `tipo IN ('queja', 'reclamo')`, `estado IN ('registrado', 'en_atencion', 'respondido')`.
     - `consentimientos_datos`: `finalidad IN ('registro', 'contacto', 'solicitud', 'reclamo')`.
5. **Advisory Locks Transaccionales:**
   - La tabla `pedidos` genera correlativos continuos anuales `PED-AAAA-NNNNN` asistido por `SELECT pg_advisory_xact_lock(20261006)` en `OrderService`, asegurando que la lectura y asignación del correlativo sea estrictamente secuencial y atómica.

---

## 2. Diagrama Entidad-Relación Completo (`erDiagram`)

El siguiente diagrama modela las **20 entidades de negocio** más la tabla de autenticación biométrica WebAuthn (`passkeys`), mostrando sus atributos, tipos de datos PostgreSQL, claves primarias (`PK`), claves foráneas (`FK`) y cardinalidades exactas:

```mermaid
erDiagram
    ROLES ||--o{ USERS : "asigna rol a"
    USERS ||--o| CLIENTES : "posee perfil comercial"
    USERS ||--o{ PASSKEYS : "registra llaves webauthn"
    USERS ||--o{ HISTORIAL_ESTADOS_PEDIDO : "registra operador"
    USERS ||--o{ REGISTRO_AUDITORIA : "origina eventos"
    USERS ||--o{ BANNERS : "publica banner"
    USERS ||--o{ CONTENIDO_INSTITUCIONAL : "edita contenido"
    USERS ||--o{ SOLICITUDES_STOCK : "asigna operador"
    USERS ||--o{ RECLAMACIONES : "atiende reclamo"

    CLIENTES ||--o{ PEDIDOS : "emite pedido"
    CLIENTES ||--o{ CLIENTE_DESCUENTO : "recibe descuento"
    CLIENTES ||--o{ SOLICITUDES_STOCK : "solicita opcional"
    CLIENTES ||--o{ CONSENTIMIENTOS_DATOS : "otorga consentimiento"

    POLITICAS_DESCUENTO ||--o{ CLIENTE_DESCUENTO : "establece regla"

    CATEGORIAS ||--o{ PRODUCTO_CATEGORIA : "clasifica"
    PRODUCTOS ||--o{ PRODUCTO_CATEGORIA : "pertenece a"
    PRODUCTOS ||--o{ IMAGENES_PRODUCTO : "posee galeria"
    PRODUCTOS ||--o{ DETALLE_PEDIDOS : "se congela en"
    PRODUCTOS ||--o{ SOLICITUDES_STOCK : "demanda stock"

    PEDIDOS ||--|{ DETALLE_PEDIDOS : "contiene items"
    PEDIDOS ||--|{ HISTORIAL_ESTADOS_PEDIDO : "registra ciclo"
    PEDIDOS ||--|| COMPROBANTES_INTERNOS : "emite 1 a 1"

    CONSENTIMIENTOS_DATOS ||--o| MENSAJES_CONTACTO : "autoriza datos"

    ROLES {
        bigint id_rol PK
        string nombre_rol
        string descripcion
        boolean estado
        timestamptz created_at
        timestamptz updated_at
    }

    USERS {
        bigint id PK
        string name
        citext email UK
        timestamptz email_verified_at
        string password
        text two_factor_secret
        text two_factor_recovery_codes
        timestamptz two_factor_confirmed_at
        bigint id_rol FK
        timestamptz last_login_at
        timestamptz created_at
        timestamptz updated_at
        timestamptz deleted_at
    }

    PASSKEYS {
        bigint id PK
        bigint user_id FK
        string name
        string credential_id UK
        json credential
        timestamptz last_used_at
        timestamptz created_at
        timestamptz updated_at
    }

    CLIENTES {
        bigint id_cliente PK
        bigint id_user FK
        string tipo_documento
        string numero_documento UK
        string razon_social
        string telefono
        string direccion
        boolean estado
        timestamptz created_at
        timestamptz updated_at
        timestamptz deleted_at
    }

    POLITICAS_DESCUENTO {
        bigint id_politica PK
        string nombre
        string tipo_descuento
        decimal valor
        decimal monto_minimo
        boolean activo
        timestamptz created_at
        timestamptz updated_at
    }

    CLIENTE_DESCUENTO {
        bigint id_cliente_descuento PK
        bigint id_cliente FK
        bigint id_politica FK
        date fecha_inicio
        date fecha_fin
        boolean activo
        timestamptz created_at
        timestamptz updated_at
    }

    CATEGORIAS {
        bigint id_categoria PK
        string nombre
        string slug UK
        text descripcion
        integer orden
        boolean activo
        timestamptz created_at
        timestamptz updated_at
        timestamptz deleted_at
    }

    PRODUCTOS {
        bigint id_producto PK
        string codigo_sku UK
        string nombre
        string slug UK
        string descripcion_corta
        text descripcion_larga
        decimal precio_base
        string stock_estado
        boolean destacado
        integer orden
        boolean activo
        timestamptz created_at
        timestamptz updated_at
        timestamptz deleted_at
    }

    PRODUCTO_CATEGORIA {
        bigint id_producto PK_FK
        bigint id_categoria PK_FK
    }

    IMAGENES_PRODUCTO {
        bigint id_imagen PK
        bigint id_producto FK
        string url_imagen
        integer orden
        boolean es_portada
        timestamptz created_at
        timestamptz updated_at
    }

    PEDIDOS {
        bigint id_pedido PK
        string codigo_correlativo UK
        bigint id_cliente FK
        decimal subtotal
        decimal descuento_total
        decimal total_neto
        string estado_actual
        timestamptz fecha_emision
        text observaciones_entrega
        timestamptz created_at
        timestamptz updated_at
    }

    DETALLE_PEDIDOS {
        bigint id_detalle PK
        bigint id_pedido FK
        bigint id_producto FK
        integer cantidad
        string nombre_comercial_congelado
        decimal precio_unitario_congelado
        decimal subtotal_linea
        timestamptz created_at
        timestamptz updated_at
    }

    HISTORIAL_ESTADOS_PEDIDO {
        bigint id_historial PK
        bigint id_pedido FK
        string estado
        bigint id_usuario_operador FK
        text notas_bitacora
        timestamptz fecha_cambio
    }

    COMPROBANTES_INTERNOS {
        bigint id_comprobante PK
        bigint id_pedido UK_FK
        string numero_comprobante UK
        timestamptz fecha_emision
        string leyenda_legal
    }

    SOLICITUDES_STOCK {
        bigint id_solicitud PK
        bigint id_producto FK
        bigint id_cliente FK
        string nombres_contacto
        string correo
        string telefono
        integer cantidad_requerida
        string estado
        bigint id_usuario_asignado FK
        text notas_seguimiento
        timestamptz created_at
        timestamptz updated_at
    }

    RECLAMACIONES {
        bigint id_reclamacion PK
        string codigo_correlativo UK
        string nombre_consumidor
        string tipo_documento
        string numero_documento
        string correo
        string telefono
        string domicilio
        string bien_contratado
        string tipo
        text detalle
        timestamptz fecha_registro
        date fecha_limite_respuesta
        string estado
        date fecha_respuesta
        bigint id_usuario_atendio FK
        timestamptz created_at
        timestamptz updated_at
    }

    CONSENTIMIENTOS_DATOS {
        bigint id_consentimiento PK
        bigint id_cliente FK
        string correo
        string finalidad
        string version_politica
        boolean aceptado
        timestamptz aceptado_en
        string direccion_ip
        string user_agent
        timestamptz created_at
        timestamptz updated_at
    }

    MENSAJES_CONTACTO {
        bigint id_mensaje PK
        string nombre
        string correo
        string telefono
        string asunto
        text mensaje
        bigint id_consentimiento FK
        timestamptz fecha_envio
        timestamptz created_at
        timestamptz updated_at
    }

    BANNERS {
        bigint id_banner PK
        string titulo
        string url_imagen
        string enlace_destino
        integer orden
        boolean activo
        bigint id_usuario FK
        timestamptz created_at
        timestamptz updated_at
    }

    CONTENIDO_INSTITUCIONAL {
        bigint id_contenido PK
        string clave UK
        string titulo
        text contenido
        bigint id_usuario FK
        timestamptz created_at
        timestamptz updated_at
    }

    REGISTRO_AUDITORIA {
        bigint id_evento PK
        bigint id_usuario FK
        string entidad
        bigint id_registro
        string accion
        text estado_previo
        string direccion_ip
        timestamptz fecha_evento
    }
```

---

## 3. Catálogo Detallado de Tablas y Modelos Eloquent

A continuación se documenta cada una de las tablas del esquema, contrastada contra su modelo Eloquent en `app/Models/`.

---

### 3.1. Entidad: `Role`
- **Tabla:** `roles`
- **Clave Primaria:** `id_rol` (BIGINT identity)
- **Modelo:** `App\Models\Role`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_rol` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `nombre_rol` | `VARCHAR(50)` | No | - | E.g. `'Administrador'`, `'Operador'` |
  | `descripcion` | `VARCHAR(150)` | Sí | `NULL` | Detalle del alcance del rol |
  | `estado` | `BOOLEAN` | No | `true` | Estado activo/inactivo |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'estado' => 'boolean'`
- **Relaciones Eloquent:**
  - `users()`: `HasMany` hacia `User` (`foreignKey: 'id_rol'`, `localKey: 'id_rol'`).

---

### 3.2. Entidad: `User`
- **Tabla:** `users`
- **Clave Primaria:** `id` (BIGINT identity)
- **Modelo:** `App\Models\User` (extiende `Authenticatable`, usa `SoftDeletes`, `HasFactory`, `Notifiable`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `name` | `VARCHAR(255)` | No | - | Nombre del usuario |
  | `email` | `CITEXT` | No | - | **UNIQUE**, insensible a mayúsculas/minúsculas |
  | `email_verified_at` | `TIMESTAMPTZ` | Sí | `NULL` | Verificación de correo |
  | `password` | `VARCHAR(255)` | No | - | Hash Bcrypt (cost 12) |
  | `two_factor_secret` | `TEXT` | Sí | `NULL` | Secreto TOTP cifrado (Fortify 2FA) |
  | `two_factor_recovery_codes` | `TEXT` | Sí | `NULL` | Códigos de recuperación 2FA |
  | `two_factor_confirmed_at` | `TIMESTAMPTZ` | Sí | `NULL` | Confirmación de activación 2FA |
  | `remember_token` | `VARCHAR(100)` | Sí | `NULL` | Token de sesión recordada |
  | `id_rol` | `BIGINT` | Sí | `NULL` | **FK** -> `roles.id_rol` |
  | `last_login_at` | `TIMESTAMPTZ` | Sí | `NULL` | Registro de última conexión |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
  | `deleted_at` | `TIMESTAMPTZ` | Sí | `NULL` | **Soft Delete** |
- **Casts:** `'email_verified_at' => 'datetime'`, `'last_login_at' => 'datetime'`, `'password' => 'hashed'`
- **Relaciones Eloquent:**
  - `role()`: `BelongsTo` hacia `Role` (`foreignKey: 'id_rol'`, `ownerKey: 'id_rol'`).
  - `cliente()`: `HasOne` hacia `Cliente` (`foreignKey: 'id_user'`).
- **Métodos Helper:** `isAdministrador(): bool`, `isOperador(): bool`.

---

### 3.3. Entidad: `Cliente`
- **Tabla:** `clientes`
- **Clave Primaria:** `id_cliente` (BIGINT identity)
- **Modelo:** `App\Models\Cliente` (usa `SoftDeletes`, `HasFactory`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_cliente` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_user` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `tipo_documento` | `VARCHAR(10)` | No | - | **CHECK** (`tipo_documento IN ('DNI', 'RUC')`) |
  | `numero_documento` | `VARCHAR(20)` | No | - | **UNIQUE** (DNI 8 dígitos o RUC 11 dígitos) |
  | `razon_social` | `VARCHAR(150)` | No | - | Razón social o nombres y apellidos |
  | `telefono` | `VARCHAR(20)` | Sí | `NULL` | Teléfono / celular |
  | `direccion` | `VARCHAR(255)` | Sí | `NULL` | Dirección de entrega / fiscal |
  | `estado` | `BOOLEAN` | No | `true` | Estado activo/inactivo |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
  | `deleted_at` | `TIMESTAMPTZ` | Sí | `NULL` | **Soft Delete** |
- **Casts:** `'estado' => 'boolean'`
- **Relaciones Eloquent:**
  - `user()`: `BelongsTo` hacia `User` (`foreignKey: 'id_user'`).
  - `pedidos()`: `HasMany` hacia `Pedido` (`foreignKey: 'id_cliente'`, `localKey: 'id_cliente'`).
  - `descuentos()`: `HasMany` hacia `ClienteDescuento` (`foreignKey: 'id_cliente'`, `localKey: 'id_cliente'`).
  - `descuentosVigentes()`: Consulta especializada que filtra descuentos activos donde `fecha_inicio <= now() <= fecha_fin` y la política esté activa.

---

### 3.4. Entidad: `PoliticaDescuento`
- **Tabla:** `politicas_descuento`
- **Clave Primaria:** `id_politica` (BIGINT identity)
- **Modelo:** `App\Models\PoliticaDescuento`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_politica` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `nombre` | `VARCHAR(150)` | No | - | Nombre descriptivo de la política comercial |
  | `tipo_descuento` | `VARCHAR(20)` | No | - | **CHECK** (`tipo_descuento IN ('porcentaje', 'monto_fijo')`) |
  | `valor` | `NUMERIC(10,2)` | No | - | Magnitud (% o S/) |
  | `monto_minimo` | `NUMERIC(10,2)` | Sí | `NULL` | Subtotal mínimo para activación de regla |
  | `activo` | `BOOLEAN` | No | `true` | Flag de vigencia |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'valor' => 'decimal:2'`, `'monto_minimo' => 'decimal:2'`, `'activo' => 'boolean'`

---

### 3.5. Entidad: `ClienteDescuento`
- **Tabla:** `cliente_descuento`
- **Clave Primaria:** `id_cliente_descuento` (BIGINT identity)
- **Modelo:** `App\Models\ClienteDescuento`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_cliente_descuento` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_cliente` | `BIGINT` | No | - | **FK** -> `clientes.id_cliente` (`ON DELETE CASCADE`) |
  | `id_politica` | `BIGINT` | No | - | **FK** -> `politicas_descuento.id_politica` (`ON DELETE CASCADE`) |
  | `fecha_inicio` | `DATE` | Sí | `NULL` | Inicio de vigencia |
  | `fecha_fin` | `DATE` | Sí | `NULL` | Fin de vigencia |
  | `activo` | `BOOLEAN` | No | `true` | Flag de activación |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'fecha_inicio' => 'date'`, `'fecha_fin' => 'date'`, `'activo' => 'boolean'`
- **Relaciones Eloquent:**
  - `cliente()`: `BelongsTo` hacia `Cliente` (`foreignKey: 'id_cliente'`).
  - `politica()`: `BelongsTo` hacia `PoliticaDescuento` (`foreignKey: 'id_politica'`).

---

### 3.6. Entidad: `Categoria`
- **Tabla:** `categorias`
- **Clave Primaria:** `id_categoria` (BIGINT identity)
- **Modelo:** `App\Models\Categoria` (usa `SoftDeletes`, `HasFactory`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_categoria` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `nombre` | `VARCHAR(150)` | No | - | Nombre de la categoría (e.g. "Tuberías de PVC") |
  | `slug` | `VARCHAR(120)` | No | - | **UNIQUE** para navegación web |
  | `descripcion` | `TEXT` | Sí | `NULL` | Detalle técnico de la familia |
  | `orden` | `INTEGER` | No | `0` | Orden de aparición en menú |
  | `activo` | `BOOLEAN` | No | `true` | Visibilidad pública |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
  | `deleted_at` | `TIMESTAMPTZ` | Sí | `NULL` | **Soft Delete** |
- **Casts:** `'activo' => 'boolean'`
- **Relaciones Eloquent:**
  - `productos()`: `BelongsToMany` hacia `Producto` mediante la tabla pivot `producto_categoria` (`foreignPivotKey: 'id_categoria'`, `relatedPivotKey: 'id_producto'`).

---

### 3.7. Entidad: `Producto`
- **Tabla:** `productos`
- **Clave Primaria:** `id_producto` (BIGINT identity)
- **Modelo:** `App\Models\Producto` (usa `SoftDeletes`, `HasFactory`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_producto` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `codigo_sku` | `VARCHAR(35)` | No | - | **UNIQUE** (código industrial del producto) |
  | `nombre` | `VARCHAR(150)` | No | - | Nombre comercial |
  | `slug` | `VARCHAR(180)` | Sí | `NULL` | **UNIQUE** para URL canónica (autogenerado en `booted`) |
  | `descripcion_corta` | `VARCHAR(255)` | Sí | `NULL` | Resumen para tarjetas de catálogo |
  | `descripcion_larga` | `TEXT` | Sí | `NULL` | Especificaciones técnicas completas |
  | `precio_base` | `NUMERIC(10,2)` | No | `0.00` | **CHECK** (`precio_base >= 0`) |
  | `stock_estado` | `VARCHAR(30)` | No | `'disponible'` | **CHECK** (`stock_estado IN ('disponible', 'stock_bajo', 'agotado_por_consultar')`) |
  | `destacado` | `BOOLEAN` | No | `false` | Flag para portal de inicio |
  | `orden` | `INTEGER` | No | `0` | Posición en grillas |
  | `activo` | `BOOLEAN` | No | `true` | Visibilidad en catálogo |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
  | `deleted_at` | `TIMESTAMPTZ` | Sí | `NULL` | **Soft Delete** |
- **Casts:** `'precio_base' => 'decimal:2'`, `'activo' => 'boolean'`, `'destacado' => 'boolean'`
- **Comportamiento Automático (`booted`):** Genera slug único garantizado mediante `Str::slug($nombre . ' ' . $codigo_sku)` al guardar.
- **Enlace de Rutas:** `getRouteKeyName()` devuelve `'slug'`.
- **Relaciones Eloquent:**
  - `categorias()`: `BelongsToMany` hacia `Categoria` mediante `producto_categoria`.
  - `imagenes()`: `HasMany` hacia `ImagenProducto` (`foreignKey: 'id_producto'`, `localKey: 'id_producto'`).

---

### 3.8. Tabla Pivot: `producto_categoria`
- **Tabla:** `producto_categoria`
- **Clave Primaria Compuesta:** `['id_producto', 'id_categoria']`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Restricciones / Observaciones |
  |---|---|---|---|
  | `id_producto` | `BIGINT` | No | **PK**, **FK** -> `productos.id_producto` (`ON DELETE CASCADE`) |
  | `id_categoria` | `BIGINT` | No | **PK**, **FK** -> `categorias.id_categoria` (`ON DELETE CASCADE`) |

---

### 3.9. Entidad: `ImagenProducto`
- **Tabla:** `imagenes_producto`
- **Clave Primaria:** `id_imagen` (BIGINT identity)
- **Modelo:** `App\Models\ImagenProducto`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_imagen` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_producto` | `BIGINT` | No | - | **FK** -> `productos.id_producto` (`ON DELETE CASCADE`) |
  | `url_imagen` | `VARCHAR(255)` | No | - | Ruta pública (`/storage/productos/...`) |
  | `orden` | `INTEGER` | No | `0` | Secuencia en carrusel |
  | `es_portada` | `BOOLEAN` | No | `false` | Imagen principal de la tarjeta |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'es_portada' => 'boolean'`
- **Relaciones Eloquent:**
  - `producto()`: `BelongsTo` hacia `Producto` (`foreignKey: 'id_producto'`).

---

### 3.10. Entidad: `Pedido`
- **Tabla:** `pedidos`
- **Clave Primaria:** `id_pedido` (BIGINT identity)
- **Modelo:** `App\Models\Pedido`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_pedido` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `codigo_correlativo` | `VARCHAR(20)` | No | - | **UNIQUE** (`PED-AAAA-NNNNN`) |
  | `id_cliente` | `BIGINT` | No | - | **FK** -> `clientes.id_cliente` (`ON DELETE RESTRICT`) |
  | `subtotal` | `NUMERIC(10,2)` | No | `0.00` | Suma de subtotales de líneas |
  | `descuento_total` | `NUMERIC(10,2)` | No | `0.00` | Monto deducido por política B2B |
  | `total_neto` | `NUMERIC(10,2)` | No | `0.00` | Subtotal menos descuento |
  | `estado_actual` | `VARCHAR(20)` | No | `'pendiente'` | **CHECK** (`estado_actual IN ('pendiente', 'en_revision', 'confirmado', 'atendido', 'cancelado')`) |
  | `fecha_emision` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Fecha formal de solicitud |
  | `observaciones_entrega` | `TEXT` | Sí | `NULL` | Instrucciones especiales del cliente |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'subtotal' => 'decimal:2'`, `'descuento_total' => 'decimal:2'`, `'total_neto' => 'decimal:2'`, `'fecha_emision' => 'datetime'`
- **Relaciones Eloquent:**
  - `cliente()`: `BelongsTo` hacia `Cliente` (`foreignKey: 'id_cliente'`, `ownerKey: 'id_cliente'`).
  - `detalles()`: `HasMany` hacia `DetallePedido` (`foreignKey: 'id_pedido'`, `localKey: 'id_pedido'`).
  - `historial()`: `HasMany` hacia `HistorialEstadoPedido` (`foreignKey: 'id_pedido'`, `localKey: 'id_pedido'`).
  - `comprobante()`: `HasOne` hacia `ComprobanteInterno` (`foreignKey: 'id_pedido'`, `localKey: 'id_pedido'`).

---

### 3.11. Entidad: `DetallePedido`
- **Tabla:** `detalle_pedidos`
- **Clave Primaria:** `id_detalle` (BIGINT identity)
- **Modelo:** `App\Models\DetallePedido`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Restricciones / Observaciones |
  |---|---|---|---|
  | `id_detalle` | `BIGINT` | No | Clave primaria |
  | `id_pedido` | `BIGINT` | No | **FK** -> `pedidos.id_pedido` (`ON DELETE CASCADE`) |
  | `id_producto` | `BIGINT` | No | **FK** -> `productos.id_producto` (`ON DELETE RESTRICT`) |
  | `cantidad` | `INTEGER` | No | Unidades solicitadas |
  | `nombre_comercial_congelado` | `VARCHAR(150)` | No | Nombre del producto congelado al comprar |
  | `precio_unitario_congelado` | `NUMERIC(10,2)` | No | Precio base congelado al comprar |
  | `subtotal_linea` | `NUMERIC(10,2)` | No | `cantidad * precio_unitario_congelado` |
  | `created_at` | `TIMESTAMPTZ` | Sí | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | Actualización |
- **Índice de Unicidad:** `UNIQUE(['id_pedido', 'id_producto'])` para evitar líneas duplicadas de un mismo producto en un pedido.
- **Casts:** `'precio_unitario_congelado' => 'decimal:2'`, `'subtotal_linea' => 'decimal:2'`
- **Relaciones Eloquent:**
  - `pedido()`: `BelongsTo` hacia `Pedido` (`foreignKey: 'id_pedido'`).
  - `producto()`: `BelongsTo` hacia `Producto` (`foreignKey: 'id_producto'`).

---

### 3.12. Entidad: `HistorialEstadoPedido`
- **Tabla:** `historial_estados_pedido`
- **Clave Primaria:** `id_historial` (BIGINT identity)
- **Modelo:** `App\Models\HistorialEstadoPedido` (`public $timestamps = false`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_historial` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_pedido` | `BIGINT` | No | - | **FK** -> `pedidos.id_pedido` (`ON DELETE CASCADE`) |
  | `estado` | `VARCHAR(20)` | No | - | Estado transicionado |
  | `id_usuario_operador` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `notas_bitacora` | `TEXT` | Sí | `NULL` | Justificación técnica u operativa del cambio |
  | `fecha_cambio` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Instante de la transición |
- **Casts:** `'fecha_cambio' => 'datetime'`
- **Relaciones Eloquent:**
  - `pedido()`: `BelongsTo` hacia `Pedido` (`foreignKey: 'id_pedido'`).

---

### 3.13. Entidad: `ComprobanteInterno`
- **Tabla:** `comprobantes_internos`
- **Clave Primaria:** `id_comprobante` (BIGINT identity)
- **Modelo:** `App\Models\ComprobanteInterno` (`public $timestamps = false`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_comprobante` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_pedido` | `BIGINT` | No | - | **UNIQUE**, **FK** -> `pedidos.id_pedido` (`ON DELETE CASCADE`) |
  | `numero_comprobante` | `VARCHAR(30)` | No | - | **UNIQUE** (`CI-AAAA-NNNNN`) |
  | `fecha_emision` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Fecha de expedición |
  | `leyenda_legal` | `VARCHAR(255)` | No | `'DOCUMENTO DE CONTROL INTERNO - NO VALIDO PARA FINES TRIBUTARIOS'` | Leyenda legal de resguardo |
- **Casts:** `'fecha_emision' => 'datetime'`
- **Relaciones Eloquent:**
  - `pedido()`: `BelongsTo` hacia `Pedido` (`foreignKey: 'id_pedido'`).

---

### 3.14. Entidad: `SolicitudStock`
- **Tabla:** `solicitudes_stock`
- **Clave Primaria:** `id_solicitud` (BIGINT identity)
- **Modelo:** `App\Models\SolicitudStock`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_solicitud` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_producto` | `BIGINT` | No | - | **FK** -> `productos.id_producto` (`ON DELETE CASCADE`) |
  | `id_cliente` | `BIGINT` | Sí | `NULL` | **FK** -> `clientes.id_cliente` (`ON DELETE SET NULL`) |
  | `nombres_contacto` | `VARCHAR(150)` | No | - | Persona de contacto |
  | `correo` | `VARCHAR(150)` | No | - | Correo electrónico de respuesta |
  | `telefono` | `VARCHAR(20)` | Sí | `NULL` | Número de contacto |
  | `cantidad_requerida` | `INTEGER` | Sí | `NULL` | Metros o piezas requeridas |
  | `estado` | `VARCHAR(20)` | No | `'pendiente'` | **CHECK** (`estado IN ('pendiente', 'en_atencion', 'atendida')`) |
  | `id_usuario_asignado` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `notas_seguimiento` | `TEXT` | Sí | `NULL` | Notas de coordinación comercial |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Relaciones Eloquent:**
  - `producto()`: `BelongsTo` hacia `Producto` (`foreignKey: 'id_producto'`).

---

### 3.15. Entidad: `Reclamacion`
- **Tabla:** `reclamaciones`
- **Clave Primaria:** `id_reclamacion` (BIGINT identity)
- **Modelo:** `App\Models\Reclamacion`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_reclamacion` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `codigo_correlativo` | `VARCHAR(20)` | No | - | **UNIQUE** (`REC-AAAA-NNNNN`) |
  | `nombre_consumidor` | `VARCHAR(150)` | No | - | Nombre del titular del reclamo |
  | `tipo_documento` | `VARCHAR(10)` | No | - | **CHECK** (`tipo_documento IN ('DNI', 'CE', 'RUC')`) |
  | `numero_documento` | `VARCHAR(20)` | No | - | Número de identidad |
  | `correo` | `VARCHAR(150)` | No | - | Correo del reclamante |
  | `telefono` | `VARCHAR(20)` | Sí | `NULL` | Teléfono |
  | `domicilio` | `VARCHAR(255)` | Sí | `NULL` | Dirección del consumidor |
  | `bien_contratado` | `VARCHAR(255)` | Sí | `NULL` | Producto o servicio materia de reclamo |
  | `tipo` | `VARCHAR(10)` | No | - | **CHECK** (`tipo IN ('queja', 'reclamo')`) |
  | `detalle` | `TEXT` | No | - | Descripción de los hechos |
  | `fecha_registro` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Ingreso al sistema |
  | `fecha_limite_respuesta` | `DATE` | Sí | `NULL` | **15 días hábiles** (Ley 29571 / D.S. 101-2022-PCM) |
  | `estado` | `VARCHAR(20)` | No | `'registrado'` | **CHECK** (`estado IN ('registrado', 'en_atencion', 'respondido')`) |
  | `fecha_respuesta` | `DATE` | Sí | `NULL` | Fecha de contestación formal |
  | `id_usuario_atendio` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'fecha_registro' => 'datetime'`, `'fecha_limite_respuesta' => 'date'`, `'fecha_respuesta' => 'date'`

---

### 3.16. Entidad: `ConsentimientoDato`
- **Tabla:** `consentimientos_datos`
- **Clave Primaria:** `id_consentimiento` (BIGINT identity)
- **Modelo:** `App\Models\ConsentimientoDato`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_consentimiento` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_cliente` | `BIGINT` | Sí | `NULL` | **FK** -> `clientes.id_cliente` (`ON DELETE SET NULL`) |
  | `correo` | `VARCHAR(150)` | Sí | `NULL` | Correo del titular |
  | `finalidad` | `VARCHAR(20)` | No | - | **CHECK** (`finalidad IN ('registro', 'contacto', 'solicitud', 'reclamo')`) |
  | `version_politica` | `VARCHAR(20)` | Sí | `NULL` | Versión legal de la política de privacidad |
  | `aceptado` | `BOOLEAN` | No | `false` | Aceptación expresa (Ley 29733) |
  | `aceptado_en` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Fecha y hora exacta de consentimiento |
  | `direccion_ip` | `VARCHAR(45)` | Sí | `NULL` | IP de auditoría (IPv4/IPv6) |
  | `user_agent` | `VARCHAR(255)` | Sí | `NULL` | Agente de navegación del usuario |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'aceptado' => 'boolean'`, `'aceptado_en' => 'datetime'`

---

### 3.17. Entidad: `MensajeContacto`
- **Tabla:** `mensajes_contacto`
- **Clave Primaria:** `id_mensaje` (BIGINT identity)
- **Modelo:** `App\Models\MensajeContacto`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_mensaje` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `nombre` | `VARCHAR(150)` | No | - | Nombre del remitente |
  | `correo` | `VARCHAR(150)` | No | - | Correo de respuesta |
  | `telefono` | `VARCHAR(20)` | Sí | `NULL` | Teléfono |
  | `asunto` | `VARCHAR(150)` | Sí | `NULL` | Título del mensaje |
  | `mensaje` | `TEXT` | No | - | Cuerpo de la consulta |
  | `id_consentimiento` | `BIGINT` | Sí | `NULL` | **FK** -> `consentimientos_datos.id_consentimiento` (`ON DELETE SET NULL`) |
  | `fecha_envio` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Fecha de emisión |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'fecha_envio' => 'datetime'`

---

### 3.18. Entidad: `Banner`
- **Tabla:** `banners`
- **Clave Primaria:** `id_banner` (BIGINT identity)
- **Modelo:** `App\Models\Banner`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_banner` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `titulo` | `VARCHAR(255)` | Sí | `NULL` | Texto superpuesto |
  | `url_imagen` | `VARCHAR(255)` | No | - | Ruta en disco público |
  | `enlace_destino` | `VARCHAR(255)` | Sí | `NULL` | URL de destino al hacer clic |
  | `orden` | `INTEGER` | No | `0` | Orden en el slider |
  | `activo` | `BOOLEAN` | No | `true` | Visibilidad |
  | `id_usuario` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `created_at` | `TIMESTAMPTZ` | Sí | `NULL` | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | `NULL` | Actualización |
- **Casts:** `'activo' => 'boolean'`

---

### 3.19. Entidad: `ContenidoInstitucional`
- **Tabla:** `contenido_institucional`
- **Clave Primaria:** `id_contenido` (BIGINT identity)
- **Modelo:** `App\Models\ContenidoInstitucional`
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Restricciones / Observaciones |
  |---|---|---|---|
  | `id_contenido` | `BIGINT` | No | Clave primaria |
  | `clave` | `VARCHAR(50)` | No | **UNIQUE** (e.g. `'resena'`, `'mision'`, `'vision'`) |
  | `titulo` | `VARCHAR(150)` | Sí | Título visible |
  | `contenido` | `TEXT` | Sí | Párrafo o cuerpo HTML seguro |
  | `id_usuario` | `BIGINT` | Sí | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `created_at` | `TIMESTAMPTZ` | Sí | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | Actualización |

---

### 3.20. Entidad: `RegistroAuditoria`
- **Tabla:** `registro_auditoria`
- **Clave Primaria:** `id_evento` (BIGINT identity)
- **Modelo:** `App\Models\RegistroAuditoria` (`public $timestamps = false`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Valor por Defecto | Restricciones / Observaciones |
  |---|---|---|---|---|
  | `id_evento` | `BIGINT` | No | Auto-incremental | Clave primaria |
  | `id_usuario` | `BIGINT` | Sí | `NULL` | **FK** -> `users.id` (`ON DELETE SET NULL`) |
  | `entidad` | `VARCHAR(50)` | No | - | Tabla afectada (e.g. `'pedidos'`, `'productos'`) |
  | `id_registro` | `BIGINT` | Sí | `NULL` | ID del registro intervenido |
  | `accion` | `VARCHAR(50)` | No | - | Operación ejecutada (e.g. `'cambio_estado'`, `'guardar'`) |
  | `estado_previo` | `TEXT` | Sí | `NULL` | Snapshot o estado antes del cambio |
  | `direccion_ip` | `VARCHAR(45)` | Sí | `NULL` | IP de origen del cliente |
  | `fecha_evento` | `TIMESTAMPTZ` | No | `CURRENT_TIMESTAMP` | Instante inmutable del evento |
- **Casts:** `'fecha_evento' => 'datetime'`

---

### 3.21. Entidad de Infraestructura: `Passkey`
- **Tabla:** `passkeys`
- **Clave Primaria:** `id` (BIGINT identity)
- **Mapeo:** Laravel Passkeys (`Laravel\Passkeys\Passkeys::userModel()`)
- **Campos:**
  | Columna | Tipo PostgreSQL | Nulable | Restricciones / Observaciones |
  |---|---|---|---|
  | `id` | `BIGINT` | No | Clave primaria |
  | `user_id` | `BIGINT` | No | **FK** -> `users.id` (`ON DELETE CASCADE`), índice en `user_id` |
  | `name` | `VARCHAR(255)` | No | Nombre descriptivo (e.g. "Windows Hello", "Touch ID") |
  | `credential_id` | `VARCHAR(255)` | No | **UNIQUE** (identificador criptográfico público WebAuthn) |
  | `credential` | `JSON` | No | Estructura JSON con clave pública y parámetros de autenticación |
  | `last_used_at` | `TIMESTAMPTZ` | Sí | Último uso de la llave biométrica |
  | `created_at` | `TIMESTAMPTZ` | Sí | Creación |
  | `updated_at` | `TIMESTAMPTZ` | Sí | Actualización |
