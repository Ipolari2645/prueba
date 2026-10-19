---
titulo: "ADR-0003: Interfaz con Livewire 4 renderizado en servidor en lugar de una SPA"
tipo: adr
area: decisiones
version: 1.0.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - composer.json
  - package.json
  - routes/web.php
  - app/Livewire/
  - resources/views/front/producto.blade.php
requisitos: []
---

# ADR-0003: Interfaz con Livewire 4 renderizado en servidor en lugar de una SPA

## Estado

`aceptado` — 2026-10-19. Registro retroactivo de una decisión ya implementada en el código auditado.

## Contexto

El portal necesita interacción dinámica en el catálogo, en el carrito con cálculo de descuentos por cliente y en un panel administrativo de 14 pantallas, con un único código base y un único despliegue. `PLAN_FASE_3.md:17` fija como pila "Blade + Livewire 4.4 + Tailwind CSS 4".

## Decisión

Construir la capa de presentación con Livewire `^4.4` (`composer.json:15`) sobre Blade. `package.json` no declara React, Vue ni Inertia: solo herramientas de compilación.

- **Panel:** las 14 pantallas son 14 componentes en `app/Livewire/Admin/` servidos por 15 rutas `Route::livewire()` (`routes/web.php:48-62`); `ProductoForm` atiende tanto la creación como la edición (`routes/web.php:50-51`).
- **Páginas públicas:** combinan `Route::view()` (`/catalogo`, `/contacto`, `/terminos`, `/descargas`, `/privacidad`, `/libro-reclamaciones`, `/carrito`, `/mi-cuenta/perfil`; `routes/web.php:24-34` y `:40-41`) con controladores para la portada, la ficha de producto y los pedidos (`routes/web.php:22`, `:36`, `:42-44`). Los componentes de `app/Livewire/` se alojan dentro de esas vistas; por ejemplo, `<livewire:solicitud-stock-form>` está en `resources/views/front/producto.blade.php:63` y no tiene ruta propia.

No existe una API REST ni un cliente separado.

## Alternativas evaluadas

El repositorio no contiene el acta de esta decisión. Los motivos de descarte son razonamiento técnico sobre la estructura actual, no motivos declarados por el equipo; el responsable debe confirmarlos.

| Alternativa | Ventajas | Por qué no aplica |
|---|---|---|
| SPA (React o Vue) con API REST | Interfaz desacoplada | Exigiría una API, un segundo proyecto y su despliegue; el código actual no los tiene (`package.json`, `routes/web.php`) |
| Vistas Blade sin componentes reactivos | Menor complejidad | Filtros del catálogo, carrito y paneles recargarían la página completa en cada acción |

## Consecuencias

- **Positivas:** cinco de los componentes llaman directamente a los servicios de dominio (`CartService`, `OrderService`, `AuditoriaService`): `ProductoForm`, `PedidoDetalle`, `Cart`, `AddToCart` y `CartCount`; no hay capa de API intermedia.
- **Negativas — estado en el servidor:** cada interacción reactiva depende de una petición al endpoint de actualización de Livewire.
- **Negativas — autorización no automática:** la ruta del panel exige `auth` y `can:access-admin` (`routes/web.php:47`), pero una búsqueda de `authorize`, `Gate::`, `->can(` y `@can` en `app/Livewire/` y `resources/views/livewire/admin/` no arroja resultados. Los gates `manage-catalog` y `manage-users` existen (`app/Providers/AppServiceProvider.php:28`, `:30`) sin invocarse desde los componentes. El detalle del hallazgo va en `docs/tecnica/02-modulos/02-panel-admin.md` (pendiente de ampliar en la Tanda 3).
