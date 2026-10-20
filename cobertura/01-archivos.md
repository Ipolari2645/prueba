---
titulo: Cobertura por archivo
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-20
commit_auditado: no-aplica
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
- Total de artefactos: **197**. Cubiertos: **7**. Parciales: **91**. Sin documento: **99**.

---
| Ruta | Tipo | Documento | Alcance declarado | Estado |
|---|---|---|---|---|
| .editorconfig | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `.editorconfig` | cubierto |
| .gitattributes | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `.gitattributes` | cubierto |
| .github/workflows\ci.yml | Integración continua | — | — | sin-documentar |
| .github/workflows\documentacion.yml | Integración continua | — | — | sin-documentar |
| app\Actions\Fortify\CreateNewUser.php | Código PHP | — | — | sin-documentar |
| app\Actions\Fortify\PasswordValidationRules.php | Código PHP | — | — | sin-documentar |
| app\Actions\Fortify\ResetUserPassword.php | Código PHP | — | — | sin-documentar |
| app\Actions\Fortify\UpdateUserPassword.php | Código PHP | — | — | sin-documentar |
| app\Actions\Fortify\UpdateUserProfileInformation.php | Código PHP | — | — | sin-documentar |
| app\Console\Commands\GenerarMapaDocumentacion.php | Código PHP | — | — | sin-documentar |
| app\Console\Commands\ValidarDocumentacion.php | Código PHP | — | — | sin-documentar |
| app\Http\Controllers\Controller.php | Código PHP | — | — | sin-documentar |
| app\Http\Controllers\HomeController.php | Código PHP | — | — | sin-documentar |
| app\Http\Controllers\PedidoController.php | Código PHP | — | — | sin-documentar |
| app\Http\Controllers\ProductoController.php | Código PHP | — | — | sin-documentar |
| app\Http\Middleware\SecurityHeaders.php | Código PHP | — | — | sin-documentar |
| app\Livewire\AddToCart.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\BannerIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\CategoriaIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ClienteIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ContenidoIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\Dashboard.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\DescuentoIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\PedidoDetalle.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\PedidoIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ProductoForm.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ProductoIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ReclamacionIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\ReporteIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\SolicitudStockIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Admin\UsuarioIndex.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Cart.php | Código PHP | — | — | sin-documentar |
| app\Livewire\CartCount.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Catalog.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Contacto.php | Código PHP | — | — | sin-documentar |
| app\Livewire\Perfil.php | Código PHP | — | — | sin-documentar |
| app\Livewire\ReclamacionForm.php | Código PHP | — | — | sin-documentar |
| app\Livewire\SolicitudStockForm.php | Código PHP | — | — | sin-documentar |
| app\Mail\ContactoMensaje.php | Código PHP | — | — | sin-documentar |
| app\Mail\PedidoCliente.php | Código PHP | — | — | sin-documentar |
| app\Mail\PedidoOperativo.php | Código PHP | — | — | sin-documentar |
| app\Mail\ReclamacionCopia.php | Código PHP | — | — | sin-documentar |
| app\Models\Banner.php | Código PHP | — | — | sin-documentar |
| app\Models\Categoria.php | Código PHP | — | — | sin-documentar |
| app\Models\Cliente.php | Código PHP | — | — | sin-documentar |
| app\Models\ClienteDescuento.php | Código PHP | — | — | sin-documentar |
| app\Models\ComprobanteInterno.php | Código PHP | — | — | sin-documentar |
| app\Models\ConsentimientoDato.php | Código PHP | — | — | sin-documentar |
| app\Models\ContenidoInstitucional.php | Código PHP | — | — | sin-documentar |
| app\Models\DetallePedido.php | Código PHP | — | — | sin-documentar |
| app\Models\HistorialEstadoPedido.php | Código PHP | — | — | sin-documentar |
| app\Models\ImagenProducto.php | Código PHP | — | — | sin-documentar |
| app\Models\MensajeContacto.php | Código PHP | — | — | sin-documentar |
| app\Models\Pedido.php | Código PHP | — | — | sin-documentar |
| app\Models\PoliticaDescuento.php | Código PHP | — | — | sin-documentar |
| app\Models\Producto.php | Código PHP | — | — | sin-documentar |
| app\Models\Reclamacion.php | Código PHP | — | — | sin-documentar |
| app\Models\RegistroAuditoria.php | Código PHP | — | — | sin-documentar |
| app\Models\Role.php | Código PHP | — | — | sin-documentar |
| app\Models\SolicitudStock.php | Código PHP | — | — | sin-documentar |
| app\Models\User.php | Código PHP | — | — | sin-documentar |
| app\Policies\PedidoPolicy.php | Código PHP | — | — | sin-documentar |
| app\Policies\ProductoPolicy.php | Código PHP | — | — | sin-documentar |
| app\Policies\UserPolicy.php | Código PHP | — | — | sin-documentar |
| app\Providers\AppServiceProvider.php | Código PHP | — | — | sin-documentar |
| app\Providers\FortifyServiceProvider.php | Código PHP | — | — | sin-documentar |
| app\Providers\HorizonServiceProvider.php | Código PHP | — | — | sin-documentar |
| app\Services\AuditoriaService.php | Código PHP | — | — | sin-documentar |
| app\Services\CartService.php | Código PHP | — | — | sin-documentar |
| app\Services\MapaDocumentacionService.php | Código PHP | — | — | sin-documentar |
| app\Services\OrderService.php | Código PHP | — | — | sin-documentar |
| composer.json | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `composer.json` | cubierto |
| config\app.php | Configuración | — | — | sin-documentar |
| config\auth.php | Configuración | — | — | sin-documentar |
| config\cache.php | Configuración | — | — | sin-documentar |
| config\database.php | Configuración | — | — | sin-documentar |
| config\filam.php | Configuración | — | — | sin-documentar |
| config\filesystems.php | Configuración | — | — | sin-documentar |
| config\fortify.php | Configuración | — | — | sin-documentar |
| config\horizon.php | Configuración | — | — | sin-documentar |
| config\logging.php | Configuración | — | — | sin-documentar |
| config\mail.php | Configuración | — | — | sin-documentar |
| config\queue.php | Configuración | — | — | sin-documentar |
| config\services.php | Configuración | — | — | sin-documentar |
| config\session.php | Configuración | — | — | sin-documentar |
| database/factories\CategoriaFactory.php | Factory | `docs/tecnica\README.md` | `database` | parcial |
| database/factories\ProductoFactory.php | Factory | `docs/tecnica\README.md` | `database` | parcial |
| database/factories\UserFactory.php | Factory | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\0001_01_01_000000_create_users_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\0001_01_01_000001_create_cache_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\0001_01_01_000002_create_jobs_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100001_enable_citext_extension.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100002_create_roles_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100003_add_filam_fields_to_users_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100004_create_clientes_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100005_create_politicas_descuento_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100006_create_cliente_descuento_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100007_create_categorias_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100008_create_productos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100009_create_producto_categoria_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100010_create_imagenes_producto_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100011_create_pedidos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100012_create_detalle_pedidos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100013_create_historial_estados_pedido_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100014_create_comprobantes_internos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100015_create_solicitudes_stock_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100016_create_reclamaciones_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100017_create_consentimientos_datos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100018_create_mensajes_contacto_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100019_create_banners_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100020_create_contenido_institucional_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_100021_create_registro_auditoria_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_06_110001_add_slug_and_destacado_to_productos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_07_031705_add_two_factor_columns_to_users_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_07_031706_create_passkeys_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/migrations\2026_10_19_023442_make_precio_base_nullable_on_productos_table.php | Migración | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\CatalogoSeeder.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\DatabaseSeeder.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\DemoSeeder.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\RoleSeeder.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\UserSeeder.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| database/seeders\data\catalogo.php | Seeder | `docs/tecnica\README.md` | `database` | parcial |
| package.json | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `package.json` | cubierto |
| phpstan.neon | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `phpstan.neon` | cubierto |
| phpunit.xml | Configuración del proyecto | `docs/tecnica\04-operacion\01-pruebas.md` | `phpunit.xml` | cubierto |
| resources/css\app.css | Estilo | `docs/tecnica\README.md` | `resources` | parcial |
| resources/js\app.js | Script | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\admin\partials\nav.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\auth\forgot-password.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\auth\login.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\auth\register.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\auth\reset-password.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\components\estado-pedido.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\components\stock-badge.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\emails\contacto.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\emails\pedido-cliente.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\emails\pedido-operativo.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\emails\reclamacion.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\carrito.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\catalogo.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\contacto.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\descargas.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\home.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\pedidos\index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\pedidos\show.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\perfil.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\privacidad.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\producto.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\reclamaciones.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\front\terminos.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\home.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\layouts\admin.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\layouts\app.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\layouts\front.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\layouts\guest.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\add-to-cart.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\banner-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\categoria-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\cliente-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\contenido-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\dashboard.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\descuento-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\pedido-detalle.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\pedido-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\producto-form.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\producto-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\reclamacion-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\reporte-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\solicitud-stock-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\admin\usuario-index.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\cart-count.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\cart.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\catalog.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\contacto.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\perfil.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\reclamacion-form.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\livewire\solicitud-stock-form.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\pdf\comprobante.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| resources/views\welcome.blade.php | Vista Blade | `docs/tecnica\README.md` | `resources` | parcial |
| routes\console.php | Rutas | — | — | sin-documentar |
| routes\web.php | Rutas | — | — | sin-documentar |
| tests\Feature\AdminTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\AuthTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\CartTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\CatalogTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\ContactoTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\ExampleTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\FaseSieteTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\PedidoAdminTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\PedidoTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\PerfilTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\RbacTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\SeguridadTest.php | Prueba | — | — | sin-documentar |
| tests\Feature\ValidarDocumentacionTest.php | Prueba | — | — | sin-documentar |
| tests\TestCase.php | Prueba | — | — | sin-documentar |
| tests\Unit\ExampleTest.php | Prueba | — | — | sin-documentar |
| vite.config.js | Configuración del proyecto | `docs/tecnica\04-operacion\02-tooling-y-ci.md` | `vite.config.js` | cubierto |
