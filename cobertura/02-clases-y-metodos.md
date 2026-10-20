---
titulo: Cobertura por clase y método
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-20
commit_auditado: no-aplica
alcance:
  - app/
  - database/seeders/
  - database/factories/
requisitos: []
---

# Cobertura por clase y método

Inventario de cada clase y cada método del código de aplicación, seeders y factories, con su docblock y el documento que lo cubre.

> Documento **generado** por `php artisan docs:mapa`. No editar a mano: cualquier cambio se pierde.
> Para corregir una fila, corrige el código, el `alcance` del documento correspondiente o el propio comando.

- Se genera con `php artisan docs:mapa`; no editar a mano.
- El documento asociado proviene del `alcance` del documento que cubre el archivo de la clase.
- Total de métodos: **256**. Con docblock: **82**. Archivos cubiertos: **0**. Parciales: **9**. Sin documento: **67**.

---
| Archivo | Clase | Docblock | Visibilidad | Firma | Línea | Documento | Estado |
|---|---|---|---|---|---|---|---|
| `app\Actions\Fortify\CreateNewUser.php` | App\Actions\Fortify\CreateNewUser | sí | public | `create(array $input): User` | 23 | — | sin-documentar |
| `app\Actions\Fortify\PasswordValidationRules.php` | App\Actions\Fortify\PasswordValidationRules | sí | protected | `passwordRules(): array` | 15 | — | sin-documentar |
| `app\Actions\Fortify\ResetUserPassword.php` | App\Actions\Fortify\ResetUserPassword | sí | public | `reset(User $user, array $input): void` | 22 | — | sin-documentar |
| `app\Actions\Fortify\UpdateUserPassword.php` | App\Actions\Fortify\UpdateUserPassword | sí | public | `update(User $user, array $input): void` | 22 | — | sin-documentar |
| `app\Actions\Fortify\UpdateUserProfileInformation.php` | App\Actions\Fortify\UpdateUserProfileInformation | sí | public | `update(User $user, array $input): void` | 21 | — | sin-documentar |
| `app\Actions\Fortify\UpdateUserProfileInformation.php` | App\Actions\Fortify\UpdateUserProfileInformation | sí | protected | `updateVerifiedUser(User $user, array $input): void` | 51 | — | sin-documentar |
| `app\Console\Commands\GenerarMapaDocumentacion.php` | App\Console\Commands\GenerarMapaDocumentacion | sí | public | `handle(): int` | 19 | — | sin-documentar |
| `app\Console\Commands\GenerarMapaDocumentacion.php` | App\Console\Commands\GenerarMapaDocumentacion | sí | private | `base(): string` | 61 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `documentosEnAlcance(): array` | 67 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `validarFrontMatter(string $relativa, array $meta): void` | 138 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `areaEsperada(string $relativa): string` | 198 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `validarNombrado(string $relativa, string $ruta): void` | 205 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `validarEncabezados(string $relativa, string $cuerpo, int $desplazamiento = 0): void` | 226 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `validarEnlaces(string $relativa, string $ruta, string $cuerpo): void` | 255 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `validarMapaDeCobertura(): void` | 288 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `validarCitas(array $documentos): void` | 323 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `esRutaDelRepo(string $token): bool` | 343 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `validarDocblocks(): void` | 354 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `base(): string` | 448 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | sí | private | `frontMatter(string $contenido): array` | 458 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `registrarError(string $origen, string $mensaje): void` | 506 | — | sin-documentar |
| `app\Console\Commands\ValidarDocumentacion.php` | App\Console\Commands\ValidarDocumentacion | no | private | `registrarAviso(string $origen, string $mensaje): void` | 511 | — | sin-documentar |
| `app\Http\Controllers\HomeController.php` | App\Http\Controllers\HomeController | no | public | `index(): View` | 13 | — | sin-documentar |
| `app\Http\Controllers\PedidoController.php` | App\Http\Controllers\PedidoController | no | public | `index(Request $request): View` | 14 | — | sin-documentar |
| `app\Http\Controllers\PedidoController.php` | App\Http\Controllers\PedidoController | no | public | `show(Pedido $pedido): View` | 27 | — | sin-documentar |
| `app\Http\Controllers\PedidoController.php` | App\Http\Controllers\PedidoController | no | public | `comprobante(Pedido $pedido): Response` | 38 | — | sin-documentar |
| `app\Http\Controllers\ProductoController.php` | App\Http\Controllers\ProductoController | no | public | `show(Producto $producto): View` | 10 | — | sin-documentar |
| `app\Http\Middleware\SecurityHeaders.php` | App\Http\Middleware\SecurityHeaders | sí | public | `handle(Request $request, Closure $next): Response` | 14 | — | sin-documentar |
| `app\Livewire\AddToCart.php` | App\Livewire\AddToCart | no | public | `mount(Producto $producto): void` | 15 | — | sin-documentar |
| `app\Livewire\AddToCart.php` | App\Livewire\AddToCart | no | public | `agregar(CartService $cart): void` | 20 | — | sin-documentar |
| `app\Livewire\AddToCart.php` | App\Livewire\AddToCart | no | public | `render()` | 41 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | sí | protected | `rules(): array` | 35 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | no | public | `editar(int $id): void` | 46 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | no | public | `cancelar(): void` | 56 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | no | public | `guardar(): void` | 63 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | no | public | `eliminar(int $id): void` | 89 | — | sin-documentar |
| `app\Livewire\Admin\BannerIndex.php` | App\Livewire\Admin\BannerIndex | no | public | `render()` | 95 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | sí | protected | `rules(): array` | 31 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `updatedNombre(string $value): void` | 42 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `editar(int $id): void` | 49 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `cancelar(): void` | 61 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `guardar(): void` | 68 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `eliminar(int $id): void` | 83 | — | sin-documentar |
| `app\Livewire\Admin\CategoriaIndex.php` | App\Livewire\Admin\CategoriaIndex | no | public | `render()` | 89 | — | sin-documentar |
| `app\Livewire\Admin\ClienteIndex.php` | App\Livewire\Admin\ClienteIndex | no | public | `updatingSearch(): void` | 19 | — | sin-documentar |
| `app\Livewire\Admin\ClienteIndex.php` | App\Livewire\Admin\ClienteIndex | no | public | `render()` | 24 | — | sin-documentar |
| `app\Livewire\Admin\ContenidoIndex.php` | App\Livewire\Admin\ContenidoIndex | no | public | `mount(): void` | 17 | — | sin-documentar |
| `app\Livewire\Admin\ContenidoIndex.php` | App\Livewire\Admin\ContenidoIndex | no | public | `guardar(): void` | 27 | — | sin-documentar |
| `app\Livewire\Admin\ContenidoIndex.php` | App\Livewire\Admin\ContenidoIndex | no | public | `render()` | 43 | — | sin-documentar |
| `app\Livewire\Admin\Dashboard.php` | App\Livewire\Admin\Dashboard | no | public | `render()` | 17 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | sí | protected | `rules(): array` | 35 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `editar(int $id): void` | 46 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `cancelar(): void` | 57 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `guardar(): void` | 66 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `eliminar(int $id): void` | 81 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `asignar(): void` | 87 | — | sin-documentar |
| `app\Livewire\Admin\DescuentoIndex.php` | App\Livewire\Admin\DescuentoIndex | no | public | `render()` | 104 | — | sin-documentar |
| `app\Livewire\Admin\PedidoDetalle.php` | App\Livewire\Admin\PedidoDetalle | sí | public | `transicionesDisponibles(): array` | 39 | — | sin-documentar |
| `app\Livewire\Admin\PedidoDetalle.php` | App\Livewire\Admin\PedidoDetalle | no | public | `cambiarEstado(): void` | 44 | — | sin-documentar |
| `app\Livewire\Admin\PedidoDetalle.php` | App\Livewire\Admin\PedidoDetalle | no | public | `agregarNota(): void` | 69 | — | sin-documentar |
| `app\Livewire\Admin\PedidoDetalle.php` | App\Livewire\Admin\PedidoDetalle | no | public | `render()` | 87 | — | sin-documentar |
| `app\Livewire\Admin\PedidoIndex.php` | App\Livewire\Admin\PedidoIndex | no | public | `updatingSearch(): void` | 21 | — | sin-documentar |
| `app\Livewire\Admin\PedidoIndex.php` | App\Livewire\Admin\PedidoIndex | no | public | `updatingEstado(): void` | 26 | — | sin-documentar |
| `app\Livewire\Admin\PedidoIndex.php` | App\Livewire\Admin\PedidoIndex | no | public | `render()` | 31 | — | sin-documentar |
| `app\Livewire\Admin\ProductoForm.php` | App\Livewire\Admin\ProductoForm | sí | protected | `rules(): array` | 68 | — | sin-documentar |
| `app\Livewire\Admin\ProductoForm.php` | App\Livewire\Admin\ProductoForm | no | public | `guardar(): void` | 85 | — | sin-documentar |
| `app\Livewire\Admin\ProductoForm.php` | App\Livewire\Admin\ProductoForm | no | public | `marcarPortada(int $id): void` | 137 | — | sin-documentar |
| `app\Livewire\Admin\ProductoForm.php` | App\Livewire\Admin\ProductoForm | no | public | `eliminarImagen(int $id): void` | 148 | — | sin-documentar |
| `app\Livewire\Admin\ProductoForm.php` | App\Livewire\Admin\ProductoForm | no | public | `render()` | 159 | — | sin-documentar |
| `app\Livewire\Admin\ProductoIndex.php` | App\Livewire\Admin\ProductoIndex | no | public | `updatingSearch(): void` | 19 | — | sin-documentar |
| `app\Livewire\Admin\ProductoIndex.php` | App\Livewire\Admin\ProductoIndex | no | public | `alternarActivo(int $id): void` | 24 | — | sin-documentar |
| `app\Livewire\Admin\ProductoIndex.php` | App\Livewire\Admin\ProductoIndex | no | public | `eliminar(int $id): void` | 31 | — | sin-documentar |
| `app\Livewire\Admin\ProductoIndex.php` | App\Livewire\Admin\ProductoIndex | no | public | `render()` | 38 | — | sin-documentar |
| `app\Livewire\Admin\ReclamacionIndex.php` | App\Livewire\Admin\ReclamacionIndex | no | public | `marcar(int $id, string $estado): void` | 17 | — | sin-documentar |
| `app\Livewire\Admin\ReclamacionIndex.php` | App\Livewire\Admin\ReclamacionIndex | no | public | `render()` | 34 | — | sin-documentar |
| `app\Livewire\Admin\ReporteIndex.php` | App\Livewire\Admin\ReporteIndex | no | public | `mount(): void` | 19 | — | sin-documentar |
| `app\Livewire\Admin\ReporteIndex.php` | App\Livewire\Admin\ReporteIndex | no | public | `render()` | 25 | — | sin-documentar |
| `app\Livewire\Admin\SolicitudStockIndex.php` | App\Livewire\Admin\SolicitudStockIndex | no | public | `cambiarEstado(int $id, string $estado): void` | 17 | — | sin-documentar |
| `app\Livewire\Admin\SolicitudStockIndex.php` | App\Livewire\Admin\SolicitudStockIndex | no | public | `render()` | 31 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | sí | protected | `rules(): array` | 32 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | no | public | `editar(int $id): void` | 42 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | no | public | `cancelar(): void` | 52 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | no | public | `guardar(): void` | 58 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | no | public | `bloquear(int $id): void` | 83 | — | sin-documentar |
| `app\Livewire\Admin\UsuarioIndex.php` | App\Livewire\Admin\UsuarioIndex | no | public | `render()` | 95 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `mount(): void` | 28 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `incrementar(int $idProducto, CartService $cart): void` | 44 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `decrementar(int $idProducto, CartService $cart): void` | 51 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `eliminar(int $idProducto, CartService $cart): void` | 58 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `confirmar(CartService $cart, OrderService $orders): void` | 64 | — | sin-documentar |
| `app\Livewire\Cart.php` | App\Livewire\Cart | no | public | `render(CartService $cart)` | 116 | — | sin-documentar |
| `app\Livewire\CartCount.php` | App\Livewire\CartCount | no | public | `refresh(): void` | 12 | — | sin-documentar |
| `app\Livewire\CartCount.php` | App\Livewire\CartCount | no | public | `render(CartService $cart)` | 17 | — | sin-documentar |
| `app\Livewire\Catalog.php` | App\Livewire\Catalog | no | public | `updatingSearch(): void` | 21 | — | sin-documentar |
| `app\Livewire\Catalog.php` | App\Livewire\Catalog | no | public | `updatingCategoria(): void` | 26 | — | sin-documentar |
| `app\Livewire\Catalog.php` | App\Livewire\Catalog | no | public | `limpiarFiltros(): void` | 31 | — | sin-documentar |
| `app\Livewire\Catalog.php` | App\Livewire\Catalog | no | public | `render()` | 38 | — | sin-documentar |
| `app\Livewire\Contacto.php` | App\Livewire\Contacto | sí | protected | `rules(): array` | 31 | — | sin-documentar |
| `app\Livewire\Contacto.php` | App\Livewire\Contacto | sí | protected | `messages(): array` | 46 | — | sin-documentar |
| `app\Livewire\Contacto.php` | App\Livewire\Contacto | no | public | `enviar(): void` | 53 | — | sin-documentar |
| `app\Livewire\Contacto.php` | App\Livewire\Contacto | no | public | `render()` | 93 | — | sin-documentar |
| `app\Livewire\Perfil.php` | App\Livewire\Perfil | no | public | `mount(): void` | 32 | — | sin-documentar |
| `app\Livewire\Perfil.php` | App\Livewire\Perfil | no | public | `guardar(): void` | 46 | — | sin-documentar |
| `app\Livewire\Perfil.php` | App\Livewire\Perfil | no | public | `cambiarPassword(): void` | 81 | — | sin-documentar |
| `app\Livewire\Perfil.php` | App\Livewire\Perfil | no | public | `render()` | 103 | — | sin-documentar |
| `app\Livewire\ReclamacionForm.php` | App\Livewire\ReclamacionForm | sí | protected | `rules(): array` | 39 | — | sin-documentar |
| `app\Livewire\ReclamacionForm.php` | App\Livewire\ReclamacionForm | sí | protected | `messages(): array` | 58 | — | sin-documentar |
| `app\Livewire\ReclamacionForm.php` | App\Livewire\ReclamacionForm | no | public | `registrar(): void` | 65 | — | sin-documentar |
| `app\Livewire\ReclamacionForm.php` | App\Livewire\ReclamacionForm | no | public | `render()` | 111 | — | sin-documentar |
| `app\Livewire\SolicitudStockForm.php` | App\Livewire\SolicitudStockForm | no | public | `mount(Producto $producto): void` | 24 | — | sin-documentar |
| `app\Livewire\SolicitudStockForm.php` | App\Livewire\SolicitudStockForm | sí | protected | `rules(): array` | 34 | — | sin-documentar |
| `app\Livewire\SolicitudStockForm.php` | App\Livewire\SolicitudStockForm | no | public | `enviar(): void` | 44 | — | sin-documentar |
| `app\Livewire\SolicitudStockForm.php` | App\Livewire\SolicitudStockForm | no | public | `render()` | 71 | — | sin-documentar |
| `app\Mail\ContactoMensaje.php` | App\Mail\ContactoMensaje | no | public | `__construct(public MensajeContacto $mensaje)` | 16 | — | sin-documentar |
| `app\Mail\ContactoMensaje.php` | App\Mail\ContactoMensaje | no | public | `envelope(): Envelope` | 18 | — | sin-documentar |
| `app\Mail\ContactoMensaje.php` | App\Mail\ContactoMensaje | no | public | `content(): Content` | 25 | — | sin-documentar |
| `app\Mail\PedidoCliente.php` | App\Mail\PedidoCliente | no | public | `__construct(public Pedido $pedido)` | 17 | — | sin-documentar |
| `app\Mail\PedidoCliente.php` | App\Mail\PedidoCliente | no | public | `envelope(): Envelope` | 19 | — | sin-documentar |
| `app\Mail\PedidoCliente.php` | App\Mail\PedidoCliente | no | public | `content(): Content` | 26 | — | sin-documentar |
| `app\Mail\PedidoOperativo.php` | App\Mail\PedidoOperativo | no | public | `__construct(public Pedido $pedido)` | 17 | — | sin-documentar |
| `app\Mail\PedidoOperativo.php` | App\Mail\PedidoOperativo | no | public | `envelope(): Envelope` | 19 | — | sin-documentar |
| `app\Mail\PedidoOperativo.php` | App\Mail\PedidoOperativo | no | public | `content(): Content` | 26 | — | sin-documentar |
| `app\Mail\ReclamacionCopia.php` | App\Mail\ReclamacionCopia | no | public | `__construct(public Reclamacion $reclamacion)` | 17 | — | sin-documentar |
| `app\Mail\ReclamacionCopia.php` | App\Mail\ReclamacionCopia | no | public | `envelope(): Envelope` | 19 | — | sin-documentar |
| `app\Mail\ReclamacionCopia.php` | App\Mail\ReclamacionCopia | no | public | `content(): Content` | 26 | — | sin-documentar |
| `app\Models\Banner.php` | App\Models\Banner | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\Categoria.php` | App\Models\Categoria | no | protected | `casts(): array` | 20 | — | sin-documentar |
| `app\Models\Categoria.php` | App\Models\Categoria | no | public | `productos()` | 27 | — | sin-documentar |
| `app\Models\Cliente.php` | App\Models\Cliente | no | protected | `casts(): array` | 21 | — | sin-documentar |
| `app\Models\Cliente.php` | App\Models\Cliente | no | public | `user()` | 28 | — | sin-documentar |
| `app\Models\Cliente.php` | App\Models\Cliente | sí | public | `pedidos(): HasMany` | 36 | — | sin-documentar |
| `app\Models\Cliente.php` | App\Models\Cliente | sí | public | `descuentos(): HasMany` | 44 | — | sin-documentar |
| `app\Models\Cliente.php` | App\Models\Cliente | sí | public | `descuentosVigentes(): HasMany` | 52 | — | sin-documentar |
| `app\Models\ClienteDescuento.php` | App\Models\ClienteDescuento | no | protected | `casts(): array` | 23 | — | sin-documentar |
| `app\Models\ClienteDescuento.php` | App\Models\ClienteDescuento | no | public | `politica(): BelongsTo` | 32 | — | sin-documentar |
| `app\Models\ClienteDescuento.php` | App\Models\ClienteDescuento | no | public | `cliente(): BelongsTo` | 37 | — | sin-documentar |
| `app\Models\ComprobanteInterno.php` | App\Models\ComprobanteInterno | no | protected | `casts(): array` | 20 | — | sin-documentar |
| `app\Models\ComprobanteInterno.php` | App\Models\ComprobanteInterno | no | public | `pedido()` | 27 | — | sin-documentar |
| `app\Models\ConsentimientoDato.php` | App\Models\ConsentimientoDato | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\DetallePedido.php` | App\Models\DetallePedido | no | protected | `casts(): array` | 19 | — | sin-documentar |
| `app\Models\DetallePedido.php` | App\Models\DetallePedido | no | public | `pedido(): BelongsTo` | 27 | — | sin-documentar |
| `app\Models\DetallePedido.php` | App\Models\DetallePedido | no | public | `producto(): BelongsTo` | 32 | — | sin-documentar |
| `app\Models\HistorialEstadoPedido.php` | App\Models\HistorialEstadoPedido | no | protected | `casts(): array` | 20 | — | sin-documentar |
| `app\Models\HistorialEstadoPedido.php` | App\Models\HistorialEstadoPedido | no | public | `pedido()` | 27 | — | sin-documentar |
| `app\Models\ImagenProducto.php` | App\Models\ImagenProducto | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\ImagenProducto.php` | App\Models\ImagenProducto | no | public | `producto()` | 25 | — | sin-documentar |
| `app\Models\MensajeContacto.php` | App\Models\MensajeContacto | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\Pedido.php` | App\Models\Pedido | no | protected | `casts(): array` | 21 | — | sin-documentar |
| `app\Models\Pedido.php` | App\Models\Pedido | no | public | `cliente(): BelongsTo` | 31 | — | sin-documentar |
| `app\Models\Pedido.php` | App\Models\Pedido | no | public | `detalles(): HasMany` | 36 | — | sin-documentar |
| `app\Models\Pedido.php` | App\Models\Pedido | no | public | `historial(): HasMany` | 41 | — | sin-documentar |
| `app\Models\Pedido.php` | App\Models\Pedido | no | public | `comprobante(): HasOne` | 46 | — | sin-documentar |
| `app\Models\PoliticaDescuento.php` | App\Models\PoliticaDescuento | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | protected | `casts(): array` | 28 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | protected | `booted(): void` | 37 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | public | `getRouteKeyName(): string` | 46 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | public | `scopeActivos(Builder $query): Builder` | 51 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | public | `categorias(): BelongsToMany` | 56 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | public | `imagenes(): HasMany` | 61 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | sí | protected | `precioFormateado(): Attribute` | 69 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | public | `tienePrecio(): bool` | 76 | — | sin-documentar |
| `app\Models\Producto.php` | App\Models\Producto | no | protected | `generateUniqueSlug(Producto $producto): string` | 81 | — | sin-documentar |
| `app\Models\Reclamacion.php` | App\Models\Reclamacion | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\RegistroAuditoria.php` | App\Models\RegistroAuditoria | no | protected | `casts(): array` | 20 | — | sin-documentar |
| `app\Models\Role.php` | App\Models\Role | no | protected | `casts(): array` | 18 | — | sin-documentar |
| `app\Models\Role.php` | App\Models\Role | no | public | `users()` | 25 | — | sin-documentar |
| `app\Models\SolicitudStock.php` | App\Models\SolicitudStock | no | public | `producto(): BelongsTo` | 19 | — | sin-documentar |
| `app\Models\User.php` | App\Models\User | no | protected | `casts(): array` | 26 | — | sin-documentar |
| `app\Models\User.php` | App\Models\User | no | public | `role(): BelongsTo` | 35 | — | sin-documentar |
| `app\Models\User.php` | App\Models\User | no | public | `cliente(): HasOne` | 40 | — | sin-documentar |
| `app\Models\User.php` | App\Models\User | no | public | `isAdministrador(): bool` | 45 | — | sin-documentar |
| `app\Models\User.php` | App\Models\User | no | public | `isOperador(): bool` | 50 | — | sin-documentar |
| `app\Policies\PedidoPolicy.php` | App\Policies\PedidoPolicy | no | public | `viewAny(User $user): bool` | 10 | — | sin-documentar |
| `app\Policies\PedidoPolicy.php` | App\Policies\PedidoPolicy | no | public | `view(User $user, Pedido $pedido): bool` | 15 | — | sin-documentar |
| `app\Policies\PedidoPolicy.php` | App\Policies\PedidoPolicy | no | public | `create(User $user): bool` | 24 | — | sin-documentar |
| `app\Policies\PedidoPolicy.php` | App\Policies\PedidoPolicy | no | public | `update(User $user, Pedido $pedido): bool` | 29 | — | sin-documentar |
| `app\Policies\PedidoPolicy.php` | App\Policies\PedidoPolicy | no | public | `delete(User $user, Pedido $pedido): bool` | 34 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | public | `viewAny(?User $user): bool` | 10 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | public | `view(?User $user, Producto $producto): bool` | 15 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | public | `create(User $user): bool` | 20 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | public | `update(User $user, Producto $producto): bool` | 25 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | public | `delete(User $user, Producto $producto): bool` | 30 | — | sin-documentar |
| `app\Policies\ProductoPolicy.php` | App\Policies\ProductoPolicy | no | private | `gestionaCatalogo(?User $user): bool` | 35 | — | sin-documentar |
| `app\Policies\UserPolicy.php` | App\Policies\UserPolicy | no | public | `viewAny(User $user): bool` | 9 | — | sin-documentar |
| `app\Policies\UserPolicy.php` | App\Policies\UserPolicy | no | public | `view(User $user, User $model): bool` | 14 | — | sin-documentar |
| `app\Policies\UserPolicy.php` | App\Policies\UserPolicy | no | public | `create(User $user): bool` | 19 | — | sin-documentar |
| `app\Policies\UserPolicy.php` | App\Policies\UserPolicy | no | public | `update(User $user, User $model): bool` | 24 | — | sin-documentar |
| `app\Policies\UserPolicy.php` | App\Policies\UserPolicy | no | public | `delete(User $user, User $model): bool` | 29 | — | sin-documentar |
| `app\Providers\AppServiceProvider.php` | App\Providers\AppServiceProvider | sí | public | `register(): void` | 16 | — | sin-documentar |
| `app\Providers\AppServiceProvider.php` | App\Providers\AppServiceProvider | sí | public | `boot(): void` | 24 | — | sin-documentar |
| `app\Providers\FortifyServiceProvider.php` | App\Providers\FortifyServiceProvider | sí | public | `register(): void` | 22 | — | sin-documentar |
| `app\Providers\FortifyServiceProvider.php` | App\Providers\FortifyServiceProvider | sí | public | `boot(): void` | 30 | — | sin-documentar |
| `app\Providers\HorizonServiceProvider.php` | App\Providers\HorizonServiceProvider | sí | public | `boot(): void` | 15 | — | sin-documentar |
| `app\Providers\HorizonServiceProvider.php` | App\Providers\HorizonServiceProvider | sí | protected | `gate(): void` | 29 | — | sin-documentar |
| `app\Services\AuditoriaService.php` | App\Services\AuditoriaService | no | public | `registrar(string $entidad, ?int $idRegistro, string $accion, ?string $estadoPrevio = null): void` | 9 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | sí | public | `raw(): array` | 16 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | sí | private | `save(array $cart): void` | 27 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `add(int $idProducto, int $cantidad = 1): void` | 32 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `update(int $idProducto, int $cantidad): void` | 39 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `remove(int $idProducto): void` | 52 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `clear(): void` | 59 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `count(): int` | 64 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | sí | public | `items(): Collection` | 72 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `subtotal(): float` | 103 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `tieneSinPrecio(): bool` | 108 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `descuento(?Cliente $cliente): float` | 113 | — | sin-documentar |
| `app\Services\CartService.php` | App\Services\CartService | no | public | `total(?Cliente $cliente): float` | 139 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `__construct(private readonly ?string $base = null)` | 35 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `generar(): array` | 42 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `escribir(?string $directorio = null): array` | 62 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `desactualizados(?string $directorio = null): array` | 85 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `sinDocumentar(): int` | 110 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `resumenDocblocks(): array` | 120 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | public | `leer(string $contenido): array` | 157 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `cargarDocumentos(): void` | 208 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `cobertura(string $ruta): ?array` | 233 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `estadoDeArchivo(string $ruta): array` | 266 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reporteArchivos(): string` | 284 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `inventario(): array` | 324 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reporteClasesYMetodos(): string` | 386 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reporteVistas(): string` | 446 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `estadoDeVista(string $vista, array $referentes): array` | 494 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reporteRutas(): string` | 528 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reporteDatos(): string` | 611 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `reportePruebas(): string` | 686 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `analizarVistas(): void` | 730 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `propietarioDeVista(string $vista, array $referentes): ?string` | 826 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `tipoDeVista(string $ruta): string` | 851 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `mapaDeVistasEnRutas(): array` | 870 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `metodosDe(string $codigo): array` | 887 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `nombreDeClase(string $codigo): ?string` | 917 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `rutaDeClase(string $clase): string` | 933 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `cabecera(string $titulo, string $descripcion, array $alcance, array $notas): string` | 946 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `tabla(array $cabeceras, array $filas): string` | 994 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `celda(string $valor): string` | 1009 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `archivosDe(string $carpeta, ?string $extension = null): array` | 1017 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `cuerpo(string $contenido): string` | 1051 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `commitActual(): string` | 1059 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `fechaDelCommit(): string` | 1073 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `contenidoDe(string $ruta): string` | 1088 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `ruta(string $relativa): string` | 1098 | — | sin-documentar |
| `app\Services\MapaDocumentacionService.php` | App\Services\MapaDocumentacionService | sí | private | `base(): string` | 1106 | — | sin-documentar |
| `app\Services\OrderService.php` | App\Services\OrderService | no | public | `__construct(private CartService $cart)` | 13 | — | sin-documentar |
| `app\Services\OrderService.php` | App\Services\OrderService | sí | public | `crear(Cliente $cliente, array $items, ?string $observacionesEntrega = null): Pedido` | 18 | — | sin-documentar |
| `app\Services\OrderService.php` | App\Services\OrderService | no | private | `siguienteCorrelativo(): string` | 62 | — | sin-documentar |
| `database/seeders\CatalogoSeeder.php` | Database\Seeders\CatalogoSeeder | sí | public | `run(): void` | 17 | `docs/tecnica\README.md` | parcial |
| `database/seeders\DatabaseSeeder.php` | Database\Seeders\DatabaseSeeder | no | public | `run(): void` | 9 | `docs/tecnica\README.md` | parcial |
| `database/seeders\DemoSeeder.php` | Database\Seeders\DemoSeeder | sí | public | `run(): void` | 16 | `docs/tecnica\README.md` | parcial |
| `database/seeders\RoleSeeder.php` | Database\Seeders\RoleSeeder | no | public | `run(): void` | 10 | `docs/tecnica\README.md` | parcial |
| `database/seeders\UserSeeder.php` | Database\Seeders\UserSeeder | sí | public | `run(): void` | 14 | `docs/tecnica\README.md` | parcial |
| `database/factories\CategoriaFactory.php` | Database\Factories\CategoriaFactory | sí | public | `definition(): array` | 19 | `docs/tecnica\README.md` | parcial |
| `database/factories\ProductoFactory.php` | Database\Factories\ProductoFactory | sí | public | `definition(): array` | 18 | `docs/tecnica\README.md` | parcial |
| `database/factories\ProductoFactory.php` | Database\Factories\ProductoFactory | no | public | `destacado(): static` | 32 | `docs/tecnica\README.md` | parcial |
| `database/factories\ProductoFactory.php` | Database\Factories\ProductoFactory | no | public | `agotado(): static` | 37 | `docs/tecnica\README.md` | parcial |
| `database/factories\UserFactory.php` | Database\Factories\UserFactory | sí | public | `definition(): array` | 25 | `docs/tecnica\README.md` | parcial |
| `database/factories\UserFactory.php` | Database\Factories\UserFactory | sí | public | `unverified(): static` | 39 | `docs/tecnica\README.md` | parcial |
