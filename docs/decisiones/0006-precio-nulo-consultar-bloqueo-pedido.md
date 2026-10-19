---
titulo: "ADR-0006: Precio nulo equivale a precio por consultar y bloquea el pedido"
tipo: adr
area: decisiones
version: 1.0.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - database/migrations/2026_10_06_100008_create_productos_table.php
  - database/migrations/2026_10_19_023442_make_precio_base_nullable_on_productos_table.php
  - app/Models/Producto.php
  - app/Services/CartService.php
  - app/Livewire/AddToCart.php
  - app/Livewire/Cart.php
  - app/Livewire/Admin/ProductoForm.php
  - database/seeders/CatalogoSeeder.php
requisitos: []
---

# ADR-0006: Precio nulo equivale a precio por consultar y bloquea el pedido

## Estado

`aceptado` — 2026-10-19. Registro retroactivo de una decisión ya implementada en el código auditado.

## Contexto

El catálogo comercial del cliente no publica precios; la propia migración lo declara en su docblock (`database/migrations/2026_10_19_023442_make_precio_base_nullable_on_productos_table.php:10-13`). El esquema original no podía expresar "sin precio": `productos.precio_base` se creó como `decimal(10,2)` con valor por defecto `0` (`database/migrations/2026_10_06_100008_create_productos_table.php:18`) y con la restricción `CHECK (precio_base >= 0)` (`:26`). Un producto sin precio quedaba en `0`, y como `CartService::items()` calcula el subtotal con `round((float) $producto->precio_base * $cantidad, 2)` (`app/Services/CartService.php:96`), su línea resultaba en `0`.

## Decisión

Representar la ausencia de precio con `productos.precio_base = NULL`. La migración `2026_10_19_023442_make_precio_base_nullable_on_productos_table.php` cambia la columna a `nullable()` con default `null` (`:16-18`); no modifica la restricción `CHECK`, que un valor nulo no incumple. El `down()` convierte los nulos a `0` antes de restaurar la columna (`:23-26`).

El comportamiento se resuelve en la aplicación, no en la base:

- **Presentación:** `Producto::tienePrecio()` devuelve `false` si el precio es nulo (`app/Models/Producto.php:76-79`) y el atributo `precio_formateado` muestra "Precio por consultar" (`:66-74`). La ficha ofrece consultar por WhatsApp o por el formulario de contacto (`resources/views/livewire/add-to-cart.blade.php:6-16`).
- **Al agregar:** `AddToCart::agregar()` rechaza el producto con un error en `cantidad` (`app/Livewire/AddToCart.php:20-26`).
- **Al confirmar:** `Cart::confirmar()` rechaza el pedido si alguna línea no tiene precio (`app/Livewire/Cart.php:74-78`); `Cart::render()` expone `sinPrecio` mediante `CartService::tieneSinPrecio()` (`app/Livewire/Cart.php:125`, `app/Services/CartService.php:108-111`).
- **Carga de datos:** el formulario de administración acepta el campo vacío y lo guarda como nulo (`app/Livewire/Admin/ProductoForm.php:75`, `:94`), y `CatalogoSeeder` siembra productos con `'precio_base' => null` (`database/seeders/CatalogoSeeder.php:47`).

`OrderService::crear()` no comprueba el precio: recibe las líneas ya validadas y copia `precio_base` a `precio_unitario_congelado` (`app/Services/OrderService.php:41`).

## Alternativas evaluadas

El repositorio no contiene el acta de esta decisión. Los motivos son razonamiento técnico sobre el estado previo del código, no motivos declarados por el equipo.

| Alternativa | Ventajas | Por qué no aplica |
|---|---|---|
| Registrar el precio como `0.00` (situación previa) | No requiere migración | Ya era el estado de partida y permitía líneas de importe cero sin ninguna señal (`app/Services/CartService.php:96`) |
| Ocultar o desactivar el producto sin precio | Evita el caso | El producto dejaría de verse en el catálogo; la ficha actual lo muestra y ofrece cotizar |

## Consecuencias

- **Positivas:** se evitan pedidos con importes de cero y todo el catálogo técnico sigue visible.
- **Negativas — validación dispersa:** la ausencia de precio se valida en tres puntos de la capa de interfaz (`AddToCart::agregar()`, `Cart::confirmar()` y las vistas) y no dentro de `OrderService::crear()`. Un llamador nuevo de `OrderService::crear()` que omita la comprobación no tendría otra defensa en la capa de servicio; la última barrera es que `detalle_pedidos.precio_unitario_congelado` no admite nulos (`database/migrations/2026_10_06_100012_create_detalle_pedidos_table.php:17`), por lo que la inserción fallaría con una excepción de base de datos.
- **Negativas — subtotal engañoso:** `CartService::items()` y `CartService::subtotal()` tratan el nulo como `0` (`(float) null`), de modo que el carrito muestra un subtotal que excluye de hecho las líneas sin precio; la interfaz lo señala con `sinPrecio` (`app/Livewire/Cart.php:125`).
- **Pruebas:** `tests/Feature/CartTest.php::test_product_without_price_cannot_be_added_to_cart` (`:109`), `tests/Feature/CartTest.php::test_confirming_order_with_unpriced_product_is_rejected` (`:121`) y `tests/Feature/CatalogTest.php::test_product_without_price_shows_por_consultar` (`:98`).
