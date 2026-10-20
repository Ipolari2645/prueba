---
titulo: Índice de decisiones de arquitectura (ADR)
tipo: indice
area: decisiones
version: 1.0.0
estado: vigente
autor: George2645
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - docs/decisiones/
requisitos: []
---

# Decisiones de arquitectura (ADR)

Un **ADR** (Architecture Decision Record) registra una decisión estructural, las alternativas que
se descartaron y sus consecuencias. Sirve para responder "¿por qué está hecho así?" sin depender de
la memoria de quien lo hizo.

## Reglas

- Un archivo por decisión: `NNNN-slug.md` (numeración correlativa, 4 dígitos, kebab-case).
- Se crea con la plantilla [`../plantillas/adr.md`](../plantillas/adr.md).
- Se escribe **cuando se toma la decisión**, no meses después.
- Nunca se borra: si una decisión se revierte, se marca `reemplazado por ADR-NNNN` y se crea el nuevo.
- Forma parte del mismo PR que aplica la decisión.

## Índice

| ADR | Título | Estado | Fecha |
|---|---|---|---|
| [ADR-0001](0001-correlativo-pedidos-advisory-lock.md) | Serializar los correlativos de pedidos y reclamaciones con pg_advisory_xact_lock | `aceptado` | 2026-10-19 |
| [ADR-0002](0002-citext-correos-y-timestamptz-fechas.md) | Usar citext para el correo de usuarios y timestamptz para las fechas | `aceptado` | 2026-10-19 |
| [ADR-0003](0003-livewire-4-server-driven-ui.md) | Interfaz con Livewire 4 renderizado en servidor en lugar de una SPA | `aceptado` | 2026-10-19 |
| [ADR-0004](0004-carrito-en-sesion-http.md) | Guardar el carrito en la sesión HTTP y no en base de datos | `aceptado` | 2026-10-19 |
| [ADR-0005](0005-carpeta-supabase-en-repositorio.md) | Carpeta supabase/ presente en el repositorio sin uso en la aplicación | `propuesto` | 2026-10-19 |
| [ADR-0006](0006-precio-nulo-consultar-bloqueo-pedido.md) | Precio nulo equivale a precio por consultar y bloquea el pedido | `aceptado` | 2026-10-19 |
