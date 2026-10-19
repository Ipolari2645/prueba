---
titulo: "ADR-0003: Livewire 4 renderizado en servidor en lugar de una SPA"
tipo: adr
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - composer.json
  - routes/web.php
  - app/Livewire/
---

## 1. Contexto

El portal necesita interacción dinámica en el catálogo, el carrito con cálculo de descuentos por cliente y un panel administrativo de 14 pantallas, y debe mantenerse con un único código base y un único despliegue.

## 2. Decisión

Construir la capa de presentación con Livewire `^4.4` (`composer.json:15`) sobre Blade. Las 14 pantallas del panel se enrutan directamente a componentes con `Route::livewire()` (`routes/web.php:45-60`). Las páginas públicas usan `Route::view()` y alojan los componentes Livewire dentro de sus vistas (por ejemplo `<livewire:solicitud-stock-form>` en `resources/views/front/producto.blade.php:61`). No se construye una SPA separada.

## 3. Alternativas evaluadas

- **SPA en React o Vue consumiendo una API REST:** descartada porque duplicaría validaciones, control de acceso, modelos de dominio y el pipeline de despliegue.
- **Vistas Blade estáticas sin componentes reactivos:** descartada porque filtros del catálogo, carrito y paneles exigirían recargar la página completa en cada acción.

## 4. Consecuencias

- **Positivas:** los componentes de `app/Livewire/` y `app/Livewire/Admin/` reutilizan directamente `CartService`, `OrderService`, `AuditoriaService` y los modelos Eloquent, sin capa de API intermedia.
- **Negativas:** cada interacción reactiva depende de peticiones al endpoint de actualización de Livewire, por lo que el estado vive en el servidor.
- **Negativas:** la autorización no es automática. Las Policies existen, pero los componentes del panel no las invocan en la versión auditada; ver el hallazgo de control de acceso en `docs/tecnica/02-modulos/02-panel-admin.md`.
