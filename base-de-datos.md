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

## 2. Diagramas Entidad-Relación por Subdominio

Para garantizar una lectura limpia, libre de cruces de líneas o marañas visuales, el modelo relacional de **20 entidades de negocio** más la tabla de autenticación WebAuthn (`passkeys`) se organiza en **3 subdominios funcionales desacoplados**:

---

### 2.1. Subdominio 1: Usuarios, Seguridad, Catálogo y Stock
Abarca la autenticación de usuarios (Laravel Fortify con 2FA y Passkeys), roles internos (`Administrador`, `Operador`), taxonomía de categorías, catálogo con imágenes y captura de solicitudes de stock agotado.

#### Diagrama Estructural de Relaciones (Subdominio 1)
```mermaid
erDiagram
    roles ||--o{ users : "asigna rol a"
    users ||--o{ passkeys : "autentica con"
    categorias ||--o{ producto_categoria : "clasifica"
    productos ||--o{ producto_categoria : "pertenece a"
    productos ||--o{ imagenes_producto : "tiene"
    productos ||--o{ solicitudes_stock : "recibe solicitudes"

    roles {
        int id_rol PK "Identificador único de rol"
        string nombre_rol UK "Administrador u Operador"
        boolean estado "Activo / Inactivo"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    users {
        int id PK "Identificador único de usuario"
        int id_rol FK "Llave foránea hacia roles"
        string name "Nombre visible"
        string email UK "Correo único insensible citext"
        timestamp email_verified_at "Fecha verificación"
        string password "Hash Bcrypt"
        text two_factor_secret "Clave secreta 2FA Fortify"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
        timestamp deleted_at "timestamptz SoftDelete"
    }

    passkeys {
        int id PK "Identificador de credencial"
        int user_id FK "Llave foránea hacia users"
        string name "Nombre amigable de passkey"
        string credential_id UK "ID WebAuthn único"
        text data "Metadatos y llave pública JSON"
        timestamp created_at "timestamptz registro"
        timestamp updated_at "timestamptz actualización"
    }

    categorias {
        int id_categoria PK "Identificador de categoría"
        string nombre "Nombre comercial familia"
        string slug UK "Slug único URL"
        text descripcion "Descripción técnica"
        int orden "Secuencia de visualización"
        boolean activo "Flag activo"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
        timestamp deleted_at "timestamptz SoftDelete"
    }

    productos {
        int id_producto PK "Identificador de producto"
        string codigo_sku UK "Código de inventario único"
        string nombre "Nombre oficial"
        string slug UK "Slug único URL"
        text descripcion "Ficha técnica extendida"
        decimal precio_base "Precio base en soles CHECK >= 0"
        string stock_estado "CHECK: disponible, stock_bajo, agotado"
        boolean activo "Flag visibilidad pública"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
        timestamp deleted_at "timestamptz SoftDelete"
    }

    producto_categoria {
        int id_producto PK, FK "Llave foránea hacia productos"
        int id_categoria PK, FK "Llave foránea hacia categorias"
    }

    imagenes_producto {
        int id_imagen PK "Identificador de imagen"
        int id_producto FK "Llave foránea hacia productos"
        string url_imagen "Ruta storage público"
        boolean es_portada "Indica imagen principal"
        int orden "Orden en carrusel"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    solicitudes_stock {
        int id_solicitud PK "Identificador de solicitud"
        int id_producto FK "Llave foránea hacia productos"
        string nombre_contacto "Nombre del interesado"
        string email "Correo de respuesta"
        string telefono "Teléfono de contacto"
        int cantidad_referencial "Cantidad solicitada"
        string estado "CHECK: pendiente, en_atencion, atendida"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }
```

---

### 2.2. Subdominio 2: Clientes, Descuentos B2B, Pedidos y Comprobantes
Abarca el núcleo comercial: perfiles de facturación DNI/RUC, motor de descuentos preferenciales mayoristas, pedidos con congelamiento de precios/nombres y comprobantes internos 1 a 1.

#### Diagrama Estructural de Relaciones (Subdominio 2)
```mermaid
erDiagram
    users ||--o| clientes : "asocia cuenta"
    clientes ||--o{ cliente_descuentos : "recibe asignación"
    politicas_descuento ||--o{ cliente_descuentos : "define regla"
    clientes ||--o{ pedidos : "genera"
    pedidos ||--o{ detalle_pedidos : "contiene líneas"
    productos ||--o{ detalle_pedidos : "referencia catálogo"
    pedidos ||--o{ historial_estados_pedido : "registra ciclo"
    users ||--o{ historial_estados_pedido : "operador asignado"
    pedidos ||--|| comprobantes_internos : "emite 1 a 1"

    politicas_descuento {
        int id_politica PK "Identificador de política"
        string nombre "Nombre de la regla comercial"
        string tipo_descuento "CHECK: porcentaje o monto_fijo"
        decimal valor "Magnitud del descuento"
        decimal monto_minimo "Umbral mínimo de subtotal"
        boolean activo "Flag de vigencia"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    clientes {
        int id_cliente PK "Identificador de cliente"
        int id_user FK "Llave foránea users opcional"
        string tipo_documento "CHECK: DNI o RUC"
        string numero_documento UK "Número de documento tributario"
        string razon_social "Razón social o nombres"
        string direccion "Dirección fiscal o entrega"
        string telefono "Teléfono de contacto"
        boolean activo "Flag cliente activo"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
        timestamp deleted_at "timestamptz SoftDelete"
    }

    cliente_descuentos {
        int id_cliente_descuento PK "Identificador asignación"
        int id_cliente FK "Llave foránea hacia clientes"
        int id_politica FK "Llave foránea hacia politicas_descuento"
        date fecha_inicio "Inicio vigencia"
        date fecha_fin "Fin vigencia opcional"
        boolean activo "Flag activo"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    pedidos {
        int id_pedido PK "Identificador de pedido"
        int id_cliente FK "Llave foránea hacia clientes"
        string codigo_correlativo UK "Formato PED-AAAA-NNNNN"
        string estado_actual "CHECK: pendiente, revision, etc"
        decimal subtotal "Suma de líneas redondeada"
        decimal descuento "Monto descontado aplicado"
        decimal total "Monto neto a pagar"
        text observaciones_entrega "Indicaciones de obra"
        timestamp fecha_emision "timestamptz emisión"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    detalle_pedidos {
        int id_detalle PK "Identificador de detalle"
        int id_pedido FK "Llave foránea hacia pedidos"
        int id_producto FK "Llave foránea hacia productos"
        string nombre_comercial_congelado "Nombre inmutable"
        string sku_congelado "SKU inmutable"
        decimal precio_unitario_congelado "Precio congelado"
        int cantidad "Unidades solicitadas"
        decimal subtotal_linea "Importe de la línea"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    comprobantes_internos {
        int id_comprobante PK "Identificador comprobante"
        int id_pedido UK, FK "Relación 1 a 1 única con pedido"
        string serie "Serie interna NP01"
        string numero_comprobante UK "Formato CI-AAAA-NNNNN"
        string tipo_documento "nota_pedido"
        string estado "CHECK: emitido, anulado"
        decimal total "Total concordante con pedido"
        string leyenda_legal "NO VALIDO COMO COMPROBANTE TRIBUTARIO"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    historial_estados_pedido {
        int id_historial PK "Identificador de transición"
        int id_pedido FK "Llave foránea hacia pedidos"
        int id_usuario_operador FK "Operador responsable"
        string estado_anterior "Estado origen"
        string estado_nuevo "Estado destino"
        text nota_cambio "Justificación operativa"
        timestamp fecha_cambio "timestamptz de transición"
    }
```

---

### 2.3. Subdominio 3: Cumplimiento Legal (Ley 29733 / 29571), CMS y Auditoría
Modela el Libro de Reclamaciones formal con plazo de 15 días hábiles, registro inmutable de consentimientos de privacidad, formulario de contacto, banners y bitácora forense de auditoría.

#### Diagrama Estructural de Relaciones (Subdominio 3)
```mermaid
erDiagram
    clientes ||--o{ consentimientos_datos : "otorga"
    consentimientos_datos ||--o| mensajes_contacto : "vincula consentimiento"
    users ||--o{ reclamaciones : "atiende como operador"
    users ||--o{ registro_auditoria : "ejecuta acción"

    consentimientos_datos {
        int id_consentimiento PK "Identificador consentimiento"
        int id_cliente FK "Cliente opcional SET NULL"
        string finalidad "CHECK: registro, contacto, reclamo"
        string version_politica "Versión de términos v1.0"
        boolean aceptado "Flag booleano"
        timestamp aceptado_en "timestamptz de firma"
        string direccion_ip "IP de origen auditoría"
        text user_agent "Navegador y plataforma"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    mensajes_contacto {
        int id_mensaje PK "Identificador mensaje"
        int id_consentimiento FK "Consentimiento asociado"
        string nombre "Remitente"
        string correo "Correo electrónico"
        string telefono "Teléfono de contacto"
        string asunto "Tema del mensaje"
        text mensaje "Cuerpo de consulta"
        timestamp fecha_envio "timestamptz de recepción"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    reclamaciones {
        int id_reclamacion PK "Identificador de hoja"
        int id_usuario_atendio FK "Operador que resolvió"
        string codigo_correlativo UK "Formato LR-AAAA-NNNN"
        string tipo_documento "CHECK: DNI, CE, RUC"
        string numero_documento "Número identificatorio"
        string consumidor_nombre "Nombre del consumidor"
        string tipo "CHECK: queja o reclamo"
        text detalle_reclamacion "Descripción de hechos"
        text pedido_consumidor "Pretensión concreta"
        timestamp fecha_limite_respuesta "Plazo legal 15 días hábiles"
        string estado "CHECK: registrado, en_atencion, respondido"
        text respuesta_proveedor "Resolución final"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    registro_auditoria {
        int id_evento PK "Identificador forense"
        int id_usuario FK "Usuario que operó"
        string entidad "Tabla afectada"
        int id_registro "PK de la entidad"
        string accion "creacion, actualizacion, cambio_estado"
        text estado_previo "JSON snapshot previo"
        string direccion_ip "IP origen de la petición"
        timestamp fecha_evento "timestamptz inmutable"
    }

    banners {
        int id_banner PK "Identificador de banner"
        string titulo "Título promocional"
        string subtitulo "Texto descriptivo"
        string url_imagen_desktop "Imagen escritorio"
        string url_imagen_mobile "Imagen móvil"
        string enlace_destino "URL de destino"
        int orden "Orden en carrusel"
        boolean activo "Flag de publicación"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
    }

    contenidos_institucionales {
        int id_contenido PK "Identificador de contenido"
        string clave UK "Clave única mision, vision, politicas"
        string titulo "Título institucional"
        text contenido "Cuerpo formateado HTML/Markdown"
        timestamp created_at "timestamptz creación"
        timestamp updated_at "timestamptz edición"
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
