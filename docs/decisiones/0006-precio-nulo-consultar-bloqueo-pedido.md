---
titulo: "ADR-0006: Precio nulo equivale a consultar precio y bloquea el pedido"
tipo: adr
estado: borrador
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - database/migrations/2026_10_06_100008_create_productos_table.php
  - app/Services/CartService.php
  - app/Services/OrderService.php
---

## 1. Contexto

Algunos productos industriales del catálogo no tienen un precio de lista estable y se cotizan con el área comercial. El esquema original no podía expresar "sin precio": `productos.precio_base` se creó como `decimal(10,2)` con valor por defecto `0` (`database/migrations/2026_10_06_100008_create_productos_table.php:18`) y con la restricción `CHECK (precio_base >= 0)` (`:26`). Un producto sin precio quedaba en `0`, y el subtotal de su línea, calculado como `round(precio_base * cantidad, 2)` en `CartService::items()`, resultaba `0`, lo que permitía confirmar pedidos con líneas de importe cero.

## 2. Decisión

Representar la ausencia de precio con `productos.precio_base = NULL`, introducida por la migración `database/migrations/2026_10_19_023442_*`. Un producto con precio nulo se muestra como "consultar precio" y no puede formar parte de un pedido confirmado: el pedido se bloquea mientras el carrito contenga productos sin precio.

## 3. Alternativas evaluadas

- **Registrar el precio como `0.00`:** descartada porque ya era la situación de partida y permitía pedidos con importe cero sin ninguna señal para el cliente.
- **Ocultar o desactivar el producto:** descartada porque el cliente necesita ver la ficha técnica y solicitar cotización.

## 4. Consecuencias

- **Positivas:** se evitan pedidos con importes erróneos y se mantiene visible todo el catálogo técnico.
- **Negativas:** la ausencia de precio debe validarse en cada punto que lo consuma: la vista, `CartService` y `OrderService::crear()`, que congela `precio_base` en `precio_unitario_congelado` (columna `decimal(10,2)` sin nulos, `2026_10_06_100012_create_detalle_pedidos_table.php:17`).
