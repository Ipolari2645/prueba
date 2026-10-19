---
titulo: "ADR-0004: Guardar el carrito en sesión y no en base de datos"
tipo: adr
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - app/Services/CartService.php
  - config/session.php
---

## 1. Contexto

Los clientes arman un carrito de tuberías y accesorios, ajustan cantidades y solo al final confirman el pedido. Mientras tanto, nada de eso debe ser un registro comercial.

## 2. Decisión

`CartService` guarda el carrito en la sesión HTTP bajo la clave `cart` (constante `CartService::SESSION_KEY`), con la estructura `[id_producto => cantidad]`. Las operaciones son `add()`, `update()`, `remove()`, `clear()` y `count()`. En cada lectura, `CartService::items()` consulta los productos en base de datos y calcula el subtotal de cada línea con el `precio_base` vigente en ese momento.

Las tablas `pedidos` y `detalle_pedidos` solo se escriben cuando `OrderService::crear()` confirma el pedido, y allí se congela el precio (`precio_unitario_congelado`). Después de crear el pedido, `Cart::confirmar()` vacía el carrito con `CartService::clear()`.

## 3. Alternativas evaluadas

- **Tabla `carritos` en base de datos:** descartada porque crearía registros transitorios que habría que depurar periódicamente cuando los carritos se abandonan.
- **Estado solo en el navegador (`localStorage`):** descartada porque el cálculo de descuentos B2B (`PoliticaDescuento` y `ClienteDescuento`) debe ejecutarse en el servidor.

## 4. Consecuencias

- **Positivas:** el precio que ve el cliente siempre es el vigente hasta la confirmación, porque el carrito guarda solo ids y cantidades.
- **Positivas:** no existe una entidad de carrito que mantener, migrar o depurar.
- **Negativas:** la vigencia del carrito depende de la configuración de la sesión. El driver por defecto es `database` (`config/session.php:21`, `.env.example:30`), así que el carrito vive en la tabla genérica `sessions`; el tiempo de vida es de 30 minutos en `.env.example` (`SESSION_LIFETIME`) y de 120 en el valor por defecto de `config/session.php:35`.
- **Negativas:** si la sesión expira antes de confirmar, el cliente debe volver a armar el carrito.
