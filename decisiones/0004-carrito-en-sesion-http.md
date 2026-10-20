---
titulo: "ADR-0004: Guardar el carrito en la sesión HTTP y no en base de datos"
tipo: adr
area: decisiones
version: 1.0.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - app/Services/CartService.php
  - app/Services/OrderService.php
  - app/Livewire/Cart.php
  - config/session.php
  - .env.example
requisitos: []
---

# ADR-0004: Guardar el carrito en la sesión HTTP y no en base de datos

## Estado

`aceptado` — 2026-10-19. Registro retroactivo de una decisión ya implementada en el código auditado.

## Contexto

El cliente arma un carrito, ajusta cantidades y solo al final confirma el pedido. Mientras tanto, el carrito no debe ser un registro comercial: los precios deben seguir siendo los vigentes y solo se congelan al confirmar.

## Decisión

`CartService` guarda el carrito en la sesión bajo la clave `cart` (constante privada `CartService::SESSION_KEY`, `app/Services/CartService.php:11`), con la estructura `[id_producto => cantidad]` (`CartService::raw()`, `:16-22`). Las operaciones son `add()`, `update()`, `remove()`, `clear()` y `count()` (`:32-67`).

`CartService::items()` consulta los productos en base de datos en cada lectura y calcula cada subtotal con el `precio_base` vigente (`:72-101`). Las tablas `pedidos` y `detalle_pedidos` se escriben solo cuando `OrderService::crear()` confirma el pedido, y allí se congela el precio en `precio_unitario_congelado` (`app/Services/OrderService.php:41`). Después de crear el pedido, `Cart::confirmar()` vacía el carrito con `CartService::clear()` (`app/Livewire/Cart.php:108`).

## Alternativas evaluadas

El repositorio no contiene el acta de esta decisión. Los motivos de descarte son razonamiento técnico sobre el código actual, no motivos declarados por el equipo; el responsable debe confirmarlos.

| Alternativa | Ventajas | Motivo de descarte |
|---|---|---|
| Tabla `carritos` en base de datos | El carrito sobrevive al cierre de la sesión | Crearía registros transitorios que habría que depurar cuando se abandonan; el esquema actual no tiene esa tabla |
| Estado solo en el navegador (`localStorage`) | No usa almacenamiento del servidor | El descuento por cliente se calcula en el servidor (`CartService::descuento()`, `:113-137`), por lo que el servidor igual debería validar el contenido |

## Consecuencias

- **Positivas:** el cliente siempre ve el precio vigente hasta confirmar, porque el carrito guarda solo ids y cantidades. No hay entidad de carrito que migrar ni depurar.
- **Negativas — vigencia atada a la sesión:** el driver por defecto es `database` (`config/session.php:21`, `.env.example:30`), así que el carrito vive en la tabla `sessions` (`database/migrations/0001_01_01_000000_create_users_table.php`). La vida de la sesión es de 30 minutos en `.env.example:31` (`SESSION_LIFETIME`) y de 120 en el valor por defecto de `config/session.php:35`. Si la sesión expira antes de confirmar, el cliente debe volver a armar el carrito.
- **Negativas — productos inactivos:** `CartService::items()` no filtra por `activo` (`:80-83`) ni `Cart::confirmar()` lo comprueba (`app/Livewire/Cart.php:64-109`); un producto desactivado después de agregarse al carrito sigue pudiendo confirmarse. Los productos eliminados lógicamente sí quedan fuera (el modelo usa `SoftDeletes`, `app/Models/Producto.php:17`).
- **Interacción con ADR-0006:** el carrito guarda productos sin precio; el bloqueo ocurre al agregar y al confirmar.
