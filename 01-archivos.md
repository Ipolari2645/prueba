---
titulo: Cobertura por archivo
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-19
commit_auditado: 59e1994
alcance:
  - app/
  - config/
  - database/
  - resources/
  - routes/
  - tests/
  - composer.json
  - package.json
requisitos: []
---

# Cobertura por archivo

Inventario de todos los artefactos del repositorio y el documento que los cubre.

> Documento **generado** por `php artisan docs:mapa`. No editar a mano: cualquier cambio se pierde.
> Para corregir una fila, corrige el código, el `alcance` del documento correspondiente o el propio comando.

- Se genera con `php artisan docs:mapa`; no editar a mano.
- Un archivo «cubierto» significa que aparece en el campo `alcance` de un documento.
- Total de artefactos: **197**. Cubiertos: **175**. Parciales: **22**. Sin documento: **0**.

---
| Ruta | Tipo | Documento | Alcance declarado | Estado |
|---|---|---|---|---|
| .editorconfig | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `.editorconfig` | cubierto |
| .gitattributes | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `.gitattributes` | cubierto |
| .github/workflows/ci.yml | Integración continua | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `.github/workflows` | cubierto |
| .github/workflows/documentacion.yml | Integración continua | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `.github/workflows` | cubierto |
| app/Actions/Fortify/CreateNewUser.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Actions/Fortify/PasswordValidationRules.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Actions/Fortify/ResetUserPassword.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Actions/Fortify/UpdateUserPassword.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Actions/Fortify/UpdateUserProfileInformation.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Console/Commands/GenerarMapaDocumentacion.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Console/Commands/ValidarDocumentacion.php | Código PHP | `docs/tecnica/README.md` | `app` | parcial |
| app/Http/Controllers/Controller.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Http/Controllers` | cubierto |
| app/Http/Controllers/HomeController.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Http/Controllers/HomeController.php` | cubierto |
| app/Http/Controllers/PedidoController.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Http/Controllers/PedidoController.php` | cubierto |
| app/Http/Controllers/ProductoController.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Http/Controllers/ProductoController.php` | cubierto |
| app/Http/Middleware/SecurityHeaders.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Http/Middleware/SecurityHeaders.php` | cubierto |
| app/Livewire/AddToCart.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/AddToCart.php` | cubierto |
| app/Livewire/Admin/BannerIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/CategoriaIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ClienteIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ContenidoIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/Dashboard.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/DescuentoIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/PedidoDetalle.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/PedidoIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ProductoForm.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ProductoIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ReclamacionIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/ReporteIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/SolicitudStockIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Admin/UsuarioIndex.php | Código PHP | `docs/tecnica/02-modulos/02-panel-admin.md` | `app/Livewire/Admin` | cubierto |
| app/Livewire/Cart.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/Cart.php` | cubierto |
| app/Livewire/CartCount.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/CartCount.php` | cubierto |
| app/Livewire/Catalog.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/Catalog.php` | cubierto |
| app/Livewire/Contacto.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/Contacto.php` | cubierto |
| app/Livewire/Perfil.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/Perfil.php` | cubierto |
| app/Livewire/ReclamacionForm.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/ReclamacionForm.php` | cubierto |
| app/Livewire/SolicitudStockForm.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Livewire/SolicitudStockForm.php` | cubierto |
| app/Mail/ContactoMensaje.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Mail` | cubierto |
| app/Mail/PedidoCliente.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Mail` | cubierto |
| app/Mail/PedidoOperativo.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Mail` | cubierto |
| app/Mail/ReclamacionCopia.php | Código PHP | `docs/tecnica/02-modulos/01-storefront-publico.md` | `app/Mail` | cubierto |
| app/Models/Banner.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Categoria.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Cliente.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/ClienteDescuento.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/ComprobanteInterno.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/ConsentimientoDato.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/ContenidoInstitucional.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/DetallePedido.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/HistorialEstadoPedido.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/ImagenProducto.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/MensajeContacto.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Pedido.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/PoliticaDescuento.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Producto.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Reclamacion.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/RegistroAuditoria.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/Role.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/SolicitudStock.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Models/User.php | Código PHP | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `app/Models` | cubierto |
| app/Policies/PedidoPolicy.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Policies` | cubierto |
| app/Policies/ProductoPolicy.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Policies` | cubierto |
| app/Policies/UserPolicy.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Policies` | cubierto |
| app/Providers/AppServiceProvider.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Providers` | cubierto |
| app/Providers/FortifyServiceProvider.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Providers` | cubierto |
| app/Providers/HorizonServiceProvider.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Providers` | cubierto |
| app/Services/AuditoriaService.php | Código PHP | `docs/tecnica/02-modulos/03-servicios-core.md` | `app/Services/AuditoriaService.php` | cubierto |
| app/Services/CartService.php | Código PHP | `docs/tecnica/02-modulos/03-servicios-core.md` | `app/Services/CartService.php` | cubierto |
| app/Services/MapaDocumentacionService.php | Código PHP | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `app/Services` | cubierto |
| app/Services/OrderService.php | Código PHP | `docs/tecnica/02-modulos/03-servicios-core.md` | `app/Services/OrderService.php` | cubierto |
| composer.json | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `composer.json` | cubierto |
| config/app.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/auth.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/cache.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/database.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/filam.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/filesystems.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/fortify.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/horizon.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/logging.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/mail.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/queue.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/services.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| config/session.php | Configuración | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `config` | cubierto |
| database/factories/CategoriaFactory.php | Factory | `docs/tecnica/README.md` | `database` | parcial |
| database/factories/ProductoFactory.php | Factory | `docs/tecnica/README.md` | `database` | parcial |
| database/factories/UserFactory.php | Factory | `docs/tecnica/README.md` | `database` | parcial |
| database/migrations/0001_01_01_000000_create_users_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/0001_01_01_000001_create_cache_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/0001_01_01_000002_create_jobs_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100001_enable_citext_extension.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100002_create_roles_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100004_create_clientes_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100005_create_politicas_descuento_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100006_create_cliente_descuento_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100007_create_categorias_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100008_create_productos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100009_create_producto_categoria_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100010_create_imagenes_producto_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100011_create_pedidos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100012_create_detalle_pedidos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100013_create_historial_estados_pedido_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100014_create_comprobantes_internos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100015_create_solicitudes_stock_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100016_create_reclamaciones_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100017_create_consentimientos_datos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100018_create_mensajes_contacto_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100019_create_banners_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100020_create_contenido_institucional_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_100021_create_registro_auditoria_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_06_110001_add_slug_and_destacado_to_productos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_07_031705_add_two_factor_columns_to_users_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_07_031706_create_passkeys_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/migrations/2026_10_19_023442_make_precio_base_nullable_on_productos_table.php | Migración | `docs/tecnica/01-arquitectura/02-base-de-datos.md` | `database/migrations` | cubierto |
| database/seeders/CatalogoSeeder.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| database/seeders/DatabaseSeeder.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| database/seeders/DemoSeeder.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| database/seeders/RoleSeeder.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| database/seeders/UserSeeder.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| database/seeders/data/catalogo.php | Seeder | `docs/tecnica/README.md` | `database` | parcial |
| package.json | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `package.json` | cubierto |
| phpstan.neon | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `phpstan.neon` | cubierto |
| phpunit.xml | Configuración del proyecto | `docs/tecnica/04-operacion/01-pruebas.md` | `phpunit.xml` | cubierto |
| resources/css/app.css | Estilo | `docs/tecnica/README.md` | `resources` | parcial |
| resources/js/app.js | Script | `docs/tecnica/README.md` | `resources` | parcial |
| resources/views/admin/partials/nav.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/admin` | cubierto |
| resources/views/auth/forgot-password.blade.php | Vista Blade | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `resources/views/auth` | cubierto |
| resources/views/auth/login.blade.php | Vista Blade | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `resources/views/auth` | cubierto |
| resources/views/auth/register.blade.php | Vista Blade | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `resources/views/auth` | cubierto |
| resources/views/auth/reset-password.blade.php | Vista Blade | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `resources/views/auth` | cubierto |
| resources/views/components/estado-pedido.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/components` | cubierto |
| resources/views/components/stock-badge.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/components` | cubierto |
| resources/views/emails/contacto.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/emails` | cubierto |
| resources/views/emails/pedido-cliente.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/emails` | cubierto |
| resources/views/emails/pedido-operativo.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/emails` | cubierto |
| resources/views/emails/reclamacion.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/emails` | cubierto |
| resources/views/front/carrito.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/catalogo.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/contacto.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/descargas.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/home.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/pedidos/index.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/pedidos/show.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/perfil.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/privacidad.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/producto.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/reclamaciones.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/front/terminos.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/front` | cubierto |
| resources/views/home.blade.php | Vista Blade | `docs/tecnica/README.md` | `resources` | parcial |
| resources/views/layouts/admin.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/layouts/admin.blade.php` | cubierto |
| resources/views/layouts/app.blade.php | Vista Blade | `docs/tecnica/README.md` | `resources` | parcial |
| resources/views/layouts/front.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/layouts/front.blade.php` | cubierto |
| resources/views/layouts/guest.blade.php | Vista Blade | `docs/tecnica/01-arquitectura/01-topologia-y-patron.md` | `resources/views/layouts/guest.blade.php` | cubierto |
| resources/views/livewire/add-to-cart.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/admin/banner-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/categoria-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/cliente-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/contenido-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/dashboard.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/descuento-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/pedido-detalle.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/pedido-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/producto-form.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/producto-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/reclamacion-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/reporte-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/solicitud-stock-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/admin/usuario-index.blade.php | Vista Blade | `docs/tecnica/02-modulos/02-panel-admin.md` | `resources/views/livewire/admin` | cubierto |
| resources/views/livewire/cart-count.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/cart.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/catalog.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/contacto.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/perfil.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/reclamacion-form.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/livewire/solicitud-stock-form.blade.php | Vista Blade | `docs/tecnica/02-modulos/01-storefront-publico.md` | `resources/views/livewire` | cubierto |
| resources/views/pdf/comprobante.blade.php | Vista Blade | `docs/tecnica/02-modulos/03-servicios-core.md` | `resources/views/pdf` | cubierto |
| resources/views/welcome.blade.php | Vista Blade | `docs/tecnica/README.md` | `resources` | parcial |
| routes/console.php | Rutas | `docs/tecnica/README.md` | `routes` | parcial |
| routes/web.php | Rutas | `docs/tecnica/02-modulos/02-panel-admin.md` | `routes/web.php` | cubierto |
| tests/Feature/AdminTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/AuthTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/CartTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/CatalogTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/ContactoTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/ExampleTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/FaseSieteTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/PedidoAdminTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/PedidoTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/PerfilTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/RbacTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/SeguridadTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Feature/ValidarDocumentacionTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/TestCase.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| tests/Unit/ExampleTest.php | Prueba | `docs/tecnica/04-operacion/01-pruebas.md` | `tests` | cubierto |
| vite.config.js | Configuración del proyecto | `docs/tecnica/04-operacion/02-tooling-y-ci.md` | `vite.config.js` | cubierto |
