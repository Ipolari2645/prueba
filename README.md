# Plataforma E-commerce Corporativa Filam Centroplas
## Documentación Técnica de Arquitectura, Dominio e Implementación

> **Estado del Proyecto:** Fase 3 — Implementación y Codificación  
> **Versión de Documentación:** v1.0 (Auditoría Técnica del Código Fuente)  
> **Ubicación Base:** `docs/webada/`  
> **Fuente de Verdad:** Código fuente real (`app/`, `config/`, `database/`, `routes/`, `composer.json`, `package.json`)

---

## 1. Resumen Ejecutivo y Propósito Funcional

### 1.1. Contexto Corporativo
**FILAM CENTROPLAST** (FILAM S.A.) es una empresa industrial peruana con sede en la Carretera Central km 10.5, Hualhuas, Huancayo (Región Junín). Es fabricante y distribuidora especializada en sistemas de canalización de fluidos:
- Tuberías de Policloruro de Vinilo no plastificado (**PVC-U**).
- Tuberías de Polietileno de Alta Densidad (**HDPE** / PE100).
- Conexiones y accesorios para infraestructura de saneamiento, edificación, minería, riego y telecomunicaciones a nivel nacional.

### 1.2. Misión y Propósito de la Plataforma
La plataforma web es un portal comercial corporativo y sistema de gestión de pedidos enfocado en el modelo de negocio industrial y mayorista (**B2B/B2C** de fábrica). A diferencia de las tiendas virtuales de retail masivo, este sistema responde a los requerimientos contractuales y operativos de la industria:
1. **Pedidos y Solicitudes Directas:** Los clientes comerciales solicitan pedidos con precios congelados al momento de la emisión. Por mandato del modelo de negocio, **no se integran pasarelas de pago con tarjeta en línea** ni facturación electrónica directa SUNAT/OSE dentro de la web; el cierre financiero y despacho se coordina mediante canales comerciales y emisión de comprobantes internos.
2. **Disponibilidad Referencial de Stock:** El catálogo expone el inventario con semáforo comercial de 3 estados (`disponible`, `stock_bajo`, `agotado_por_consultar`), permitiendo a los clientes registrar solicitudes de stock formal para productos con quiebre de inventario.
3. **Descuentos Preferenciales B2B:** Motor de asignación de descuentos porcentuales o de monto fijo asociados al cliente, aplicados automáticamente en el carrito de compras cuando se cumplen los montos mínimos requeridos.
4. **Comprobante Interno de Control:** Emisión automática e instantánea de comprobantes internos en formato PDF (`CI-AAAA-NNNNN`) sin validez tributaria ("DOCUMENTO DE CONTROL INTERNO"), acompañando el correlativo del pedido (`PED-AAAA-NNNNN`).
5. **Cumplimiento Legal y Normativo Peruano:** 
   - **Libro de Reclamaciones Virtual:** Conforme a la Ley N.° 29571 (Código de Protección y Defensa del Consumidor) y D.S. 101-2022-PCM, gestionando quejas y reclamos con numeración correlativa única, cálculo automático de fecha límite legal (15 días hábiles) y bitácora de atención.
   - **Protección de Datos Personales:** Cumplimiento de la Ley N.° 29733 y D.S. 016-2024-JUS, con registro inmutable de consentimientos expresos (IP, User-Agent, fecha y versión de política) en registro, contacto, stock y reclamos.
6. **Trazabilidad y Auditoría:** Registro forense inmutable de eventos (`registro_auditoria`), bitácora histórica por pedido (`historial_estados_pedido`) y supervisión asíncrona de colas mediante Laravel Horizon.

---

## 2. Stack Tecnológico Detallado y Justificación

El stack implementado en el código fuente ha sido seleccionado para ofrecer robustez transaccional, bajo costo operativo (cero licencias comerciales de software) y máxima velocidad de carga.

| Componente / Paquete | Versión en Código | Justificación Arquitectónica y Configuración |
|---|---|---|
| **Lenguaje Backend** | **PHP ^8.3** (compatible 8.3 / 8.4) | Especificado como `^8.3` en `composer.json`. Ejecución de alto rendimiento sobre PHP-FPM / CLI. Aprovecha constructor property promotion, enumeraciones, tipos de retorno estrictos, match expressions y atributos nativos. |
| **Framework Web** | **Laravel 13.x** (`laravel/framework: ^13.17`) | Núcleo de la aplicación. Configuración moderna en `bootstrap/app.php` (sin `Kernel.php` legacy), ORM Eloquent, soporte de migraciones con extensiones nativas, sistema de autorización por Policies/Gates y Middleware perimetral. |
| **Frontend Reactivo** | **Livewire 4.4** (`livewire/livewire: ^4.4`) | Arquitectura reactiva basada en servidor (Full-Stack Reactivity sin el peso de una SPA desacoplada). Emplea atributos `#[Layout('layouts::admin')]`, `#[Title]`, `#[On('cart-updated')]`, routing nativo `Route::livewire()`, carga asíncrona de archivos (`WithFileUploads`) y eventos desacoplados. Garantiza SSR óptimo para indexación SEO del catálogo. |
| **Autenticación & 2FA** | **Laravel Fortify 1.41** (`laravel/fortify: ^1.41`) | Capa headless de autenticación. Gestiona registro, login, cambio y recuperación de contraseñas, Autenticación de Dos Factores (TOTP 2FA) y credenciales biométricas WebAuthn / Passkeys (`passkeys`). Protegido por rate limiting granular (5 intentos/min por IP + usuario transliterado). |
| **Motor de Base de Datos** | **PostgreSQL 16+/18** (`DB_CONNECTION=pgsql`) | RDBMS transaccional ACID con soporte para tipos de datos avanzados, restricciones `CHECK` nativas, claves foráneas con cascada estricta (`restrictOnDelete` / `cascadeOnDelete`), `timestamptz` con zona horaria y advisory locks (`pg_advisory_xact_lock`) para correlativos atómicos libres de condiciones de carrera. |
| **Extensión de BD** | **`citext` (PostgreSQL)** | Habilitada en migración `2026_10_06_100001`. Hace que la columna `email` en la tabla `users` sea insensible a mayúsculas y minúsculas sin requerir llamadas costosas a `LOWER()` ni índices funcionales redundantes. |
| **Colas y Monitoreo** | **Laravel Horizon 5.50** (`laravel/horizon: ^5.50`) + **Redis** | Orquestador de colas asíncronas para el despacho de correos transaccionales (`PedidoCliente`, `PedidoOperativo`, `ReclamacionCopia` que implementan `ShouldQueue`). Configuración en `.env.example` y `config/queue.php`: `QUEUE_CONNECTION=redis`, `CACHE_STORE=redis`, `REDIS_CLIENT=phpredis`. Aísla la latencia SMTP del ciclo de vida de la petición HTTP. Protegido en producción vía Gate `viewHorizon`. |
| **Generación de Documentos** | **Barryvdh DomPDF 3.1** (`barryvdh/laravel-dompdf: ^3.1`) | Renderizado de comprobantes internos en PDF a partir de plantillas Blade (`pdf.comprobante`). No requiere dependencias externas de NodeJS, Puppeteer o navegadores headless. |
| **Estilos y Bundling** | **Tailwind CSS v4** + **Vite 8** (`@tailwindcss/vite: ^4.0.0`) | Pipeline CSS-first moderno. Inyección directa del compilador Tailwind en Vite sin necesidad de archivo de configuración JavaScript intermedio. |
| **Calidad y Formato** | **Laravel Pint 1.27** + **Larastan 3.12** | Formateador de código conforme al estándar de Laravel y análisis estático de tipos PHPStan nivel estricto. |

---

## 3. Árbol Anotado del Directorio de Trabajo

```text
filam-centroplas/
├── app/
│   ├── Actions/Fortify/             # Acciones de ciclo de vida de usuarios (Create, Reset, Update)
│   ├── Http/
│   │   ├── Controllers/             # Controladores HTTP clásicos
│   │   │   ├── Controller.php       # Controlador base abstracto
│   │   │   ├── HomeController.php   # Orquestador del portal de inicio (destacados, banners)
│   │   │   ├── PedidoController.php # Historial, detalle y descarga de comprobante PDF
│   │   │   └── ProductoController.php # Ficha técnica pública y productos relacionados
│   │   └── Middleware/
│   │       └── SecurityHeaders.php  # Inyección global de cabeceras de seguridad HTTP
│   ├── Livewire/                    # Componentes reactivos del Storefront público
│   │   ├── AddToCart.php            # Botón de adición al carrito con control de stock
│   │   ├── Cart.php                 # Carrito, cálculo de descuentos y confirmación de pedido
│   │   ├── CartCount.php            # Badge reactivo con el total de artículos en sesión
│   │   ├── Catalog.php              # Catálogo con búsqueda textual y filtros por categoría
│   │   ├── Contacto.php             # Formulario de contacto con consentimiento de datos
│   │   ├── Perfil.php               # Edición de perfil del cliente y datos fiscales
│   │   ├── ReclamacionForm.php      # Formulario legal del Libro de Reclamaciones
│   │   ├── SolicitudStockForm.php   # Formulario modal para productos agotados
│   │   └── Admin/                   # Consola administrativa interna (RBAC: auth + access-admin)
│   │       ├── BannerIndex.php      # Gestión de banners de cabecera
│   │       ├── CategoriaIndex.php   # CRUD de categorías del catálogo
│   │       ├── ClienteIndex.php     # Directorio y estados de clientes comerciales
│   │       ├── ContenidoIndex.php   # Edición de textos institucionales (misión, visión)
│   │       ├── Dashboard.php        # Indicadores generales de negocio
│   │       ├── DescuentoIndex.php   # Asignación y administración de políticas de descuento
│   │       ├── PedidoDetalle.php    # Máquina de estados, transiciones y bitácora
│   │       ├── PedidoIndex.php      # Bandeja de pedidos con filtros por estado y correlativo
│   │       ├── ProductoForm.php     # Formulario de alta/edición de productos con galería
│   │       ├── ProductoIndex.php    # Listado, conmutación de estado activo y soft delete
│   │       ├── ReclamacionIndex.php # Seguimiento legal de quejas/reclamos con plazos
│   │       ├── ReporteIndex.php     # Reportes analíticos de ventas y demandas
│   │       ├── SolicitudStockIndex.php # Bandeja de atención de solicitudes de stock
│   │       └── UsuarioIndex.php     # Gestión de accesos internos (Admin/Operador)
│   ├── Mail/                        # Clases Mailables (asíncronas y síncronas)
│   │   ├── ContactoMensaje.php      # Alerta de nuevo mensaje de contacto (síncrono, no encola)
│   │   ├── PedidoCliente.php        # Confirmación de pedido con correlativo al cliente (ShouldQueue)
│   │   ├── PedidoOperativo.php      # Notificación interna al equipo de ventas (ShouldQueue)
│   │   └── ReclamacionCopia.php     # Copia certificada de hoja de reclamación al usuario (ShouldQueue)
│   ├── Models/                      # 19 Modelos Eloquent mapeados a PostgreSQL
│   │   ├── Banner.php               # Banners promocionales de portada
│   │   ├── Categoria.php            # Familias de productos (PVC, HDPE, Accesorios)
│   │   ├── Cliente.php              # Perfil comercial y datos de facturación (DNI/RUC)
│   │   ├── ClienteDescuento.php     # Asignación temporal de política de descuento a cliente
│   │   ├── ComprobanteInterno.php   # Registro 1:1 de comprobante de control interno
│   │   ├── ConsentimientoDato.php   # Trazabilidad de consentimiento Ley 29733
│   │   ├── ContenidoInstitucional.php # Bloques de contenido editable
│   │   ├── DetallePedido.php        # Línea de pedido con precio y nombre congelados
│   │   ├── HistorialEstadoPedido.php# Bitácora de transiciones de estado del pedido
│   │   ├── ImagenProducto.php       # Galería de imágenes con indicador de portada
│   │   ├── MensajeContacto.php      # Mensajes recibidos desde el formulario web
│   │   ├── Pedido.php               # Encabezado del pedido con correlativo anual
│   │   ├── PoliticaDescuento.php    # Definición de reglas de descuento comercial
│   │   ├── Producto.php             # Ficha técnica, SKU, slug, precio y estado de stock
│   │   ├── Reclamacion.php          # Hoja del Libro de Reclamaciones (D.S. 101-2022-PCM)
│   │   ├── RegistroAuditoria.php    # Auditoría forense de acciones del sistema
│   │   ├── Role.php                 # Roles de usuario (Administrador, Operador)
│   │   ├── SolicitudStock.php       # Solicitud de disponibilidad referencial
│   │   └── User.php                 # Usuario autenticable (Fortify, 2FA, Passkeys)
│   ├── Policies/                    # Reglas de autorización en servidor (RBAC)
│   │   ├── PedidoPolicy.php         # Control de acceso a pedidos según rol y propiedad
│   │   ├── ProductoPolicy.php       # Permisos sobre el catálogo
│   │   └── UserPolicy.php           # Permisos de administración de usuarios
│   ├── Providers/                   # Proveedores de servicios
│   │   ├── AppServiceProvider.php   # Definición de Gates y View Composers globales
│   │   ├── FortifyServiceProvider.php # Configuración de autenticación y rate limiting
│   │   └── HorizonServiceProvider.php # Gate de seguridad para Laravel Horizon
│   └── Services/                    # Capa de Lógica de Negocio y Dominio
│       ├── AuditoriaService.php     # Registro centralizado de auditoría forense
│       ├── CartService.php          # Gestión del carrito en sesión y cálculo de descuentos
│       └── OrderService.php         # Transacción atómica de pedidos y advisory locks
├── bootstrap/
│   └── app.php                      # Configuración de rutas, middleware y excepciones
├── config/                          # Configuración modular (database, fortify, horizon, filam...)
├── database/
│   ├── factories/                   # Fabricas de datos de prueba
│   ├── migrations/                  # 27 migraciones con citext, timestamptz y checks
│   └── seeders/                     # Seeders para roles, usuarios demo y catálogo
├── docs/
│   └── webada/                      # SUITE COMPLETA DE DOCUMENTACIÓN TÉCNICA
│       ├── README.md                # Este documento (visión general, stack y árbol)
│       ├── 01-arquitectura/         # Topología de arquitectura y modelo de datos
│       │   ├── topologia-y-patron.md# Patrones de diseño, controladores, servicios y seguridad
│       │   └── base-de-datos.md     # Diagramas DER modulares, tablas y modelos Eloquent
│       └── 02-modulos/              # Especificación técnica modular y componentes
│           ├── servicios-core.md    # CartService, OrderService y AuditoriaService
│           ├── storefront-publico.md# Controladores front, Livewire público y Mailables
│           └── panel-admin.md       # 14 componentes Livewire Admin y matriz de permisos
├── resources/
│   ├── css/app.css                  # Hoja de estilos con directivas Tailwind v4
│   ├── js/app.js                    # Entry point JavaScript compilado por Vite
│   └── views/                       # Vistas Blade (front, admin, layouts, pdf, emails)
└── routes/
    ├── console.php                  # Comandos de consola y scheduler
    └── web.php                      # Definición centralizada de rutas públicas y administrativas
```

---

## 4. Índice de la Documentación Técnica (`docs/webada/`)

Para profundizar en la arquitectura y el diseño técnico del sistema, navegue por los siguientes documentos especializados:

### [Módulo 01 — Arquitectura del Sistema](./01-arquitectura/)
1. **[Topología y Patrón de Arquitectura](./01-arquitectura/topologia-y-patron.md):**
   - Diagrama integral de arquitectura lógica y física en formato Mermaid (`flowchart TD`) y máquina de estados (`stateDiagram-v2`).
   - Patrón híbrido MVC + Componentes Reactivos Livewire 4.
   - Detalle de Controladores HTTP, Componentes Livewire (Storefront vs Admin).
   - Capa de Servicios de Negocio (`CartService`, `OrderService`, `AuditoriaService`).
   - Diagrama de clases de autorización RBAC (`classDiagram`).
   - Matriz de Autorización RBAC (`PedidoPolicy`, `ProductoPolicy`, `UserPolicy`).
   - Auditoría de Seguridad y Service Providers (`SecurityHeaders`, `Fortify`, `Horizon`).
2. **[Base de Datos y Modelo Entidad-Relación](./01-arquitectura/base-de-datos.md):**
   - Diagramas DER modulares en formato Mermaid (`erDiagram`) divididos por 3 subdominios funcionales (Usuarios/Catálogo, Comercial/Pedidos, Legal/CMS).
   - Detalle de extensiones (`citext`), tipos `timestamptz` y restricciones `CHECK`.
   - Catálogo de tablas, índices, llaves foráneas y reglas `ON DELETE`.
   - Mapeo exhaustivo de los 19 modelos Eloquent, atributos protegidos, casts y relaciones.

### [Módulo 02 — Módulos y Lógica de Negocio](./02-modulos/)
1. **[Servicios Core del Dominio](./02-modulos/servicios-core.md):**
   - Documentación profunda de `CartService.php`, `OrderService.php` y `AuditoriaService.php`.
   - Diagramas de flujo transaccional y motor de cálculo de descuentos en formato Mermaid (`flowchart TD`).
   - Fórmulas de cálculo de subtotales, reglas de umbral mínimo y asignación B2B.
   - Atomicidad con `DB::transaction` y advisory locks `SELECT pg_advisory_xact_lock(20261006)`.
   - Congelamiento comercial (*price freezing*) y emisión de comprobantes internos `CI-AAAA-NNNNN`.
2. **[Storefront Público y Canales de Atención](./02-modulos/storefront-publico.md):**
   - Controladores frontales (`HomeController`, `ProductoController`, `PedidoController`).
   - 8 Componentes Livewire públicos (`Catalog`, `AddToCart`, `Cart`, `CartCount`, `Perfil`, `Contacto`, `ReclamacionForm`, `SolicitudStockForm`).
   - Propiedades reactivas, validaciones, eventos `$this->dispatch('cart-updated')` y vistas Blade asociadas.
   - Clases Mailable asíncronas (`PedidoCliente`, `PedidoOperativo`, `ReclamacionCopia` con `ShouldQueue`).
3. **[Panel Administrativo (Back-Office)](./02-modulos/panel-admin.md):**
   - 14 Componentes Livewire de administración en `App\Livewire\Admin`.
   - Matriz cruzada de permisos según roles (`Administrador` vs `Operador`) vinculada a Policies y Gates.
   - Consolas de catálogo, pedidos, clientes, políticas de descuento, solicitudes de stock, libro de reclamaciones, usuarios, CMS y reportes analíticos.

---

> **Nota de Gobernanza de Código:** Esta documentación ha sido contrastada directamente contra las firmas de métodos, migraciones, políticas y componentes existentes en el repositorio en la rama `main`.
