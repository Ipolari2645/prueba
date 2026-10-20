---
titulo: Cobertura por ruta
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-20
commit_auditado: no-aplica
alcance:
  - routes/web.php
requisitos: []
---

# Cobertura por ruta

Inventario de las rutas de la aplicación y de los proveedores, con su destino y documento.

> Documento **generado** por `php artisan docs:mapa`. No editar a mano: cualquier cambio se pierde.
> Para corregir una fila, corrige el código, el `alcance` del documento correspondiente o el propio comando.

- Se genera con `php artisan docs:mapa`; no editar a mano.
- Las rutas internas de Livewire, Boost y storage se omiten por ser mecanismos del framework.
- Total de rutas listadas: **66**. Cubiertas: **20**. Parciales: **46**. Sin documento: **0**.

---
| Métodos | URI | Nombre | Acción | Middleware | Documento | Estado |
|---|---|---|---|---|---|---|
| GET,HEAD | /login | `login` | proveedor: `Laravel\Fortify\Http\Controllers\AuthenticatedSessionController@create` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /login | `login.store` | proveedor: `Laravel\Fortify\Http\Controllers\AuthenticatedSessionController@store` | `web`, `guest:web`, `throttle:login` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /logout | `logout` | proveedor: `Laravel\Fortify\Http\Controllers\AuthenticatedSessionController@destroy` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /forgot-password | `password.request` | proveedor: `Laravel\Fortify\Http\Controllers\PasswordResetLinkController@create` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /reset-password/{token} | `password.reset` | proveedor: `Laravel\Fortify\Http\Controllers\NewPasswordController@create` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /forgot-password | `password.email` | proveedor: `Laravel\Fortify\Http\Controllers\PasswordResetLinkController@store` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /reset-password | `password.update` | proveedor: `Laravel\Fortify\Http\Controllers\NewPasswordController@store` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /register | `register` | proveedor: `Laravel\Fortify\Http\Controllers\RegisteredUserController@create` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /register | `register.store` | proveedor: `Laravel\Fortify\Http\Controllers\RegisteredUserController@store` | `web`, `guest:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| PUT | /user/profile-information | `user-profile-information.update` | proveedor: `Laravel\Fortify\Http\Controllers\ProfileInformationController@update` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| PUT | /user/password | `user-password.update` | proveedor: `Laravel\Fortify\Http\Controllers\PasswordController@update` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /user/confirm-password | `password.confirm` | proveedor: `Laravel\Fortify\Http\Controllers\ConfirmablePasswordController@show` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /user/confirmed-password-status | `password.confirmation` | proveedor: `Laravel\Fortify\Http\Controllers\ConfirmedPasswordStatusController@show` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| POST | /user/confirm-password | `password.confirm.store` | proveedor: `Laravel\Fortify\Http\Controllers\ConfirmablePasswordController@store` | `web`, `auth:web` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | /horizon/api/stats | `horizon.stats.index` | proveedor: `Laravel\Horizon\Http\Controllers\DashboardStatsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/workload | `horizon.workload.index` | proveedor: `Laravel\Horizon\Http\Controllers\WorkloadController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/masters | `horizon.masters.index` | proveedor: `Laravel\Horizon\Http\Controllers\MasterSupervisorController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/monitoring | `horizon.monitoring.index` | proveedor: `Laravel\Horizon\Http\Controllers\MonitoringController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| POST | /horizon/api/monitoring | `horizon.monitoring.store` | proveedor: `Laravel\Horizon\Http\Controllers\MonitoringController@store` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/monitoring/{tag} | `horizon.monitoring-tag.paginate` | proveedor: `Laravel\Horizon\Http\Controllers\MonitoringController@paginate` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| DELETE | /horizon/api/monitoring/{tag} | `horizon.monitoring-tag.destroy` | proveedor: `Laravel\Horizon\Http\Controllers\MonitoringController@destroy` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/metrics/jobs | `horizon.jobs-metrics.index` | proveedor: `Laravel\Horizon\Http\Controllers\JobMetricsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/metrics/jobs/{id} | `horizon.jobs-metrics.show` | proveedor: `Laravel\Horizon\Http\Controllers\JobMetricsController@show` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/metrics/queues | `horizon.queues-metrics.index` | proveedor: `Laravel\Horizon\Http\Controllers\QueueMetricsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/metrics/queues/{id} | `horizon.queues-metrics.show` | proveedor: `Laravel\Horizon\Http\Controllers\QueueMetricsController@show` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/batches | `horizon.jobs-batches.index` | proveedor: `Laravel\Horizon\Http\Controllers\BatchesController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/batches/{id} | `horizon.jobs-batches.show` | proveedor: `Laravel\Horizon\Http\Controllers\BatchesController@show` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| POST | /horizon/api/batches/retry/{id} | `horizon.jobs-batches.retry` | proveedor: `Laravel\Horizon\Http\Controllers\BatchesController@retry` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/pending | `horizon.pending-jobs.index` | proveedor: `Laravel\Horizon\Http\Controllers\PendingJobsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/completed | `horizon.completed-jobs.index` | proveedor: `Laravel\Horizon\Http\Controllers\CompletedJobsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/silenced | `horizon.silenced-jobs.index` | proveedor: `Laravel\Horizon\Http\Controllers\SilencedJobsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/failed | `horizon.failed-jobs.index` | proveedor: `Laravel\Horizon\Http\Controllers\FailedJobsController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/failed/{id} | `horizon.failed-jobs.show` | proveedor: `Laravel\Horizon\Http\Controllers\FailedJobsController@show` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| POST | /horizon/api/jobs/retry/{id} | `horizon.retry-jobs.show` | proveedor: `Laravel\Horizon\Http\Controllers\RetryController@store` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/api/jobs/{id} | `horizon.jobs.show` | proveedor: `Laravel\Horizon\Http\Controllers\JobsController@show` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /horizon/{view?} | `horizon.index` | proveedor: `Laravel\Horizon\Http\Controllers\HomeController@index` | `horizon`, `Laravel\Horizon\Http\Middleware\Authenticate` | `docs/tecnica\01-arquitectura\01-topologia-y-patron.md` | parcial |
| GET,HEAD | /up | — | proveedor: Closure del framework | `` | `docs/tecnica\02-modulos\04-autenticacion-fortify.md` | parcial |
| GET,HEAD | // | `inicio` | `App\Http\Controllers\HomeController`::index() | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | cubierto |
| GET,HEAD | /catalogo | `catalogo` | Vista `front.catalogo` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /contacto | `contacto` | Vista `front.contacto` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /terminos | `terminos` | Vista `front.terminos` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /descargas | `descargas` | Vista `front.descargas` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /privacidad | `privacidad` | Vista `front.privacidad` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /libro-reclamaciones | `reclamaciones.create` | Vista `front.reclamaciones` | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /producto/{producto} | `producto.show` | `App\Http\Controllers\ProductoController`::show() | `web` | `docs/tecnica\02-modulos\01-storefront-publico.md` | cubierto |
| GET,HEAD | /home | `home` | Vista `home` | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /carrito | `carrito` | Vista `front.carrito` | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /mi-cuenta/perfil | `perfil` | Vista `front.perfil` | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | parcial |
| GET,HEAD | /mi-cuenta/pedidos | `pedidos.index` | `App\Http\Controllers\PedidoController`::index() | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | cubierto |
| GET,HEAD | /pedido/{pedido} | `pedido.show` | `App\Http\Controllers\PedidoController`::show() | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | cubierto |
| GET,HEAD | /pedido/{pedido}/comprobante | `pedido.comprobante` | `App\Http\Controllers\PedidoController`::comprobante() | `web`, `auth` | `docs/tecnica\02-modulos\01-storefront-publico.md` | cubierto |
| GET,HEAD | /admin | `admin.dashboard` | Componente `App\Livewire\Admin\Dashboard` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/productos | `admin.productos.index` | Componente `App\Livewire\Admin\ProductoIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/productos/crear | `admin.productos.create` | Componente `App\Livewire\Admin\ProductoForm` | `web`, `auth`, `can:access-admin` | `docs/decisiones\0006-precio-nulo-consultar-bloqueo-pedido.md` | cubierto |
| GET,HEAD | /admin/productos/{producto}/editar | `admin.productos.edit` | Componente `App\Livewire\Admin\ProductoForm` | `web`, `auth`, `can:access-admin` | `docs/decisiones\0006-precio-nulo-consultar-bloqueo-pedido.md` | cubierto |
| GET,HEAD | /admin/categorias | `admin.categorias.index` | Componente `App\Livewire\Admin\CategoriaIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/pedidos | `admin.pedidos.index` | Componente `App\Livewire\Admin\PedidoIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/pedidos/{pedido} | `admin.pedidos.show` | Componente `App\Livewire\Admin\PedidoDetalle` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/solicitudes-stock | `admin.stock.index` | Componente `App\Livewire\Admin\SolicitudStockIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/reclamaciones | `admin.reclamaciones.index` | Componente `App\Livewire\Admin\ReclamacionIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/descuentos | `admin.descuentos.index` | Componente `App\Livewire\Admin\DescuentoIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/clientes | `admin.clientes.index` | Componente `App\Livewire\Admin\ClienteIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/usuarios | `admin.usuarios.index` | Componente `App\Livewire\Admin\UsuarioIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/contenido | `admin.contenido.index` | Componente `App\Livewire\Admin\ContenidoIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/banners | `admin.banners.index` | Componente `App\Livewire\Admin\BannerIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
| GET,HEAD | /admin/reportes | `admin.reportes.index` | Componente `App\Livewire\Admin\ReporteIndex` | `web`, `auth`, `can:access-admin` | `docs/tecnica\02-modulos\02-panel-admin.md` | cubierto |
