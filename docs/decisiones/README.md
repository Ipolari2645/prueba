---
titulo: "Índice de Decisiones de Arquitectura (ADR)"
tipo: guia
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - docs/decisiones/
---

## 1. Propósito

Este directorio contiene los Registros de Decisiones de Arquitectura (ADR) de Filam CentroPlas. Cada ADR documenta el contexto técnico, la decisión adoptada, las alternativas descartadas y las consecuencias, con referencias al código fuente.

## 2. Catálogo de decisiones

| ID | Título | Estado | Evidencia principal |
|---|---|---|---|
| [ADR-0001](./0001-correlativo-pedidos-advisory-lock.md) | Serializar el correlativo de pedidos con `pg_advisory_xact_lock(20261006)` | Vigente | `OrderService::siguienteCorrelativo()` en `app/Services/OrderService.php` |
| [ADR-0002](./0002-citext-correos-y-timestamptz-fechas.md) | Usar `citext` para correos y `timestamptz` para fechas | Vigente | `database/migrations/2026_10_06_100001_enable_citext_extension.php:10` |
| [ADR-0003](./0003-livewire-4-server-driven-ui.md) | Livewire 4 renderizado en servidor en lugar de una SPA | Vigente | `composer.json:15`, `routes/web.php:45-60` |
| [ADR-0004](./0004-carrito-en-sesion-http.md) | Guardar el carrito en sesión y no en base de datos | Vigente | `CartService::SESSION_KEY` en `app/Services/CartService.php` |
| [ADR-0005](./0005-carpeta-supabase-en-repositorio.md) | Mantener la carpeta `supabase/` dentro del repositorio | Vigente | `supabase/.temp/linked-project.json` |
| [ADR-0006](./0006-precio-nulo-consultar-bloqueo-pedido.md) | Precio nulo equivale a consultar precio y bloquea el pedido | Borrador | `database/migrations/2026_10_19_023442_*` |
