---
titulo: Cobertura de datos (migraciones, modelos, seeders y factories)
tipo: matriz
area: cobertura
version: 1.0.0
estado: vigente
autor: php artisan docs:mapa
fecha: 2026-10-20
commit_auditado: no-aplica
alcance:
  - database/
  - app/Models/
requisitos: []
---

# Cobertura de datos (migraciones, modelos, seeders y factories)

Inventario del esquema de persistencia y su correspondencia con el modelo de datos documentado.

> Documento **generado** por `php artisan docs:mapa`. No editar a mano: cualquier cambio se pierde.
> Para corregir una fila, corrige el código, el `alcance` del documento correspondiente o el propio comando.

- Se genera con `php artisan docs:mapa`; no editar a mano.
- Migraciones: **28**. Modelos: **19**. Seeders: **6**. Factories: **3**.

---
## 1. Migraciones

| Archivo | Tablas | Documento | Estado |
|---|---|---|---|
| `0001_01_01_000000_create_users_table.php` | `users`, `password_reset_tokens`, `sessions` | `docs/tecnica\README.md` | parcial |
| `0001_01_01_000001_create_cache_table.php` | `cache`, `cache_locks` | `docs/tecnica\README.md` | parcial |
| `0001_01_01_000002_create_jobs_table.php` | `jobs`, `job_batches`, `failed_jobs` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100001_enable_citext_extension.php` | — | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100002_create_roles_table.php` | `roles` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100003_add_filam_fields_to_users_table.php` | `users` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100004_create_clientes_table.php` | `clientes` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100005_create_politicas_descuento_table.php` | `politicas_descuento` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100006_create_cliente_descuento_table.php` | `cliente_descuento` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100007_create_categorias_table.php` | `categorias` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100008_create_productos_table.php` | `productos` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100009_create_producto_categoria_table.php` | `producto_categoria` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100010_create_imagenes_producto_table.php` | `imagenes_producto` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100011_create_pedidos_table.php` | `pedidos` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100012_create_detalle_pedidos_table.php` | `detalle_pedidos` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100013_create_historial_estados_pedido_table.php` | `historial_estados_pedido` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100014_create_comprobantes_internos_table.php` | `comprobantes_internos` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100015_create_solicitudes_stock_table.php` | `solicitudes_stock` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100016_create_reclamaciones_table.php` | `reclamaciones` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100017_create_consentimientos_datos_table.php` | `consentimientos_datos` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100018_create_mensajes_contacto_table.php` | `mensajes_contacto` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100019_create_banners_table.php` | `banners` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100020_create_contenido_institucional_table.php` | `contenido_institucional` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_100021_create_registro_auditoria_table.php` | `registro_auditoria` | `docs/tecnica\README.md` | parcial |
| `2026_10_06_110001_add_slug_and_destacado_to_productos_table.php` | `productos` | `docs/tecnica\README.md` | parcial |
| `2026_10_07_031705_add_two_factor_columns_to_users_table.php` | `users` | `docs/tecnica\README.md` | parcial |
| `2026_10_07_031706_create_passkeys_table.php` | `passkeys` | `docs/tecnica\README.md` | parcial |
| `2026_10_19_023442_make_precio_base_nullable_on_productos_table.php` | `productos` | `docs/tecnica\README.md` | parcial |

## 2. Modelos Eloquent

| Modelo | Tabla | Relaciones | Documento | Estado |
|---|---|---|---|---|
| `App\Models\Banner` | `banners` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\Categoria` | `categorias` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\Cliente` | `clientes` | 3 | `docs/tecnica\README.md` | parcial |
| `App\Models\ClienteDescuento` | `cliente_descuento` | 2 | `docs/tecnica\README.md` | parcial |
| `App\Models\ComprobanteInterno` | `comprobantes_internos` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\ConsentimientoDato` | `consentimientos_datos` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\ContenidoInstitucional` | `contenido_institucional` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\DetallePedido` | `detalle_pedidos` | 2 | `docs/tecnica\README.md` | parcial |
| `App\Models\HistorialEstadoPedido` | `historial_estados_pedido` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\ImagenProducto` | `imagenes_producto` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\MensajeContacto` | `mensajes_contacto` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\Pedido` | `pedidos` | 4 | `docs/tecnica\README.md` | parcial |
| `App\Models\PoliticaDescuento` | `politicas_descuento` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\Producto` | `productos` | 2 | `docs/tecnica\README.md` | parcial |
| `App\Models\Reclamacion` | `reclamaciones` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\RegistroAuditoria` | `registro_auditoria` | 0 | `docs/tecnica\README.md` | parcial |
| `App\Models\Role` | `roles` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\SolicitudStock` | `solicitudes_stock` | 1 | `docs/tecnica\README.md` | parcial |
| `App\Models\User` | `app\_models\_users` | 2 | `docs/tecnica\README.md` | parcial |

## 3. Seeders y factories

| Archivo | Tipo | Documento | Estado |
|---|---|---|---|
| `database/seeders\CatalogoSeeder.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/seeders\DatabaseSeeder.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/seeders\DemoSeeder.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/seeders\RoleSeeder.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/seeders\UserSeeder.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/seeders\data\catalogo.php` | Seeder | `docs/tecnica\README.md` | parcial |
| `database/factories\CategoriaFactory.php` | Factory | `docs/tecnica\README.md` | parcial |
| `database/factories\ProductoFactory.php` | Factory | `docs/tecnica\README.md` | parcial |
| `database/factories\UserFactory.php` | Factory | `docs/tecnica\README.md` | parcial |
