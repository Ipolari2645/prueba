---
titulo: "ADR-0002: Usar citext para el correo de usuarios y timestamptz para las fechas"
tipo: adr
area: decisiones
version: 1.0.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - database/migrations/2026_10_06_100001_enable_citext_extension.php
  - database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php
  - config/app.php
requisitos: []
---

# ADR-0002: Usar citext para el correo de usuarios y timestamptz para las fechas

## Estado

`aceptado` — 2026-10-19. Registro retroactivo de una decisión ya implementada en el código auditado.

## Contexto

En PostgreSQL las columnas `varchar` y `text` distinguen mayúsculas de minúsculas, de modo que `Ana@x.com` y `ana@x.com` serían correos distintos para el índice único de `users.email` (`database/migrations/0001_01_01_000000_create_users_table.php:17`). Por otro lado, los pedidos, las reclamaciones, los consentimientos y el historial de estados guardan instantes de emisión o aceptación (`pedidos.fecha_emision`, `reclamaciones.fecha_registro`, `consentimientos_datos.aceptado_en`, `historial_estados_pedido.fecha_cambio`) que deben interpretarse sin ambigüedad de zona horaria.

## Decisión

- Habilitar la extensión con `CREATE EXTENSION IF NOT EXISTS citext` (`database/migrations/2026_10_06_100001_enable_citext_extension.php:10`).
- Convertir `users.email` con `ALTER TABLE users ALTER COLUMN email TYPE citext` (`database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php:18`). El índice único existente pasa a ser insensible a mayúsculas.
- Declarar las columnas temporales con zona horaria: `timestampsTz()` en 15 de las migraciones del proyecto, `softDeletesTz()` en `users`, `clientes`, `categorias` y `productos`, y conversión explícita de `users.email_verified_at`, `created_at` y `updated_at` a `timestamptz` (`database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php:19-21`).

La aplicación opera con zona horaria `UTC` (`config/app.php:68`).

## Alternativas evaluadas

El repositorio no contiene el acta de esta decisión. Los motivos de descarte son consecuencias verificables del comportamiento de PostgreSQL, no motivos declarados por el equipo; el responsable debe confirmarlos.

| Alternativa | Ventajas | Motivo de descarte |
|---|---|---|
| Normalizar a minúsculas en PHP | No requiere extensión | La unicidad dejaría de estar garantizada por la base: cualquier escritura que no pase por esa capa omitiría la conversión |
| `timestamp` sin zona horaria | Es el tipo por defecto de `timestamps()` | El valor almacenado depende de la configuración de zona del servidor al momento de leerlo o escribirlo |

## Consecuencias

- **Positivas:** comparación e índice único del correo insensibles a mayúsculas, resueltos por el motor; instantes almacenados sin ambigüedad.
- **Negativas — privilegios:** el rol que ejecuta las migraciones debe poder ejecutar `CREATE EXTENSION`.
- **Negativas — alcance parcial de `citext`:** solo `users.email` es `citext`. Los correos de `mensajes_contacto`, `reclamaciones`, `consentimientos_datos` y `solicitudes_stock` son `string` sensibles a mayúsculas (`database/migrations/2026_10_06_100018_create_mensajes_contacto_table.php:14`, `database/migrations/2026_10_06_100016_create_reclamaciones_table.php:18`, `database/migrations/2026_10_06_100017_create_consentimientos_datos_table.php:15`, `database/migrations/2026_10_06_100015_create_solicitudes_stock_table.php:17`), igual que `password_reset_tokens.email` (`database/migrations/0001_01_01_000000_create_users_table.php:25`).
- **Negativas — alcance parcial de `timestamptz`:** no todas las tablas lo usan. `passkeys` declara `timestamps()` sin zona (`database/migrations/2026_10_07_031706_create_passkeys_table.php:22`) y `password_reset_tokens.created_at` es `timestamp` (`database/migrations/0001_01_01_000000_create_users_table.php:27`).
- **Negativas — `down()`:** la migración de la extensión no la elimina para no afectar datos existentes (`database/migrations/2026_10_06_100001_enable_citext_extension.php:15`).
- **Trabajo derivado:** `DDS_FilamCentroPlas_v1.10.md:703` indica que la fecha legal de las reclamaciones se registra en `America/Lima`, mientras que la aplicación usa `UTC`. No se verificó si el código convierte la zona al registrar esa fecha; queda pendiente de revisión.
