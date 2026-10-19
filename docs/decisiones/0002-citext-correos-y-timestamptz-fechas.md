---
titulo: "ADR-0002: Usar citext para correos y timestamptz para fechas"
tipo: adr
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - database/migrations/2026_10_06_100001_enable_citext_extension.php
  - database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php
  - config/app.php
---

## 1. Contexto

En PostgreSQL las columnas `varchar` y `text` distinguen mayúsculas de minúsculas. Un correo escrito con otra capitalización podría generar cuentas duplicadas o fallos de inicio de sesión. Por otro lado, los registros con valor legal (reclamaciones, consentimientos, historial de pedidos) necesitan fechas sin ambigüedad de zona horaria, mientras que la aplicación opera con `timezone = UTC` (`config/app.php:68`).

## 2. Decisión

- Habilitar la extensión `citext` con `CREATE EXTENSION IF NOT EXISTS citext` (`database/migrations/2026_10_06_100001_enable_citext_extension.php:10`).
- Convertir `users.email` a `citext` con `ALTER TABLE users ALTER COLUMN email TYPE citext` (`database/migrations/2026_10_06_100003_add_filam_fields_to_users_table.php:18`). El índice único existente sobre `email` pasa a ser insensible a mayúsculas.
- Declarar las columnas temporales con zona horaria: `timestampsTz()` en 15 de las migraciones del proyecto, `softDeletesTz()` en `users`, `clientes`, `categorias` y `productos`, y conversión de `users.email_verified_at` a `timestamptz` (`2026_10_06_100003_add_filam_fields_to_users_table.php:19`).

## 3. Alternativas evaluadas

- **Normalizar a minúsculas en PHP (`strtolower`):** descartada porque cualquier inserción o consulta que no pase por esa capa omitiría la conversión. La restricción de unicidad debe vivir en la base de datos.
- **Usar `timestamp` sin zona horaria:** descartada por la ambigüedad que introduce ante un cambio de configuración del servidor o ante una auditoría de plazos legales.

## 4. Consecuencias

- **Positivas:** comparación e índice único insensibles a mayúsculas resueltos por el motor, y instantes de tiempo almacenados sin ambigüedad.
- **Negativas:** el rol que ejecuta las migraciones necesita poder ejecutar `CREATE EXTENSION`.
- **Negativas:** `citext` se aplica solo a `users.email`; otros campos de correo (por ejemplo los de contacto, reclamaciones o solicitudes) siguen siendo `string` sensibles a mayúsculas.
- **Negativas:** el `down()` de la migración de la extensión no la elimina, para no afectar datos existentes (`2026_10_06_100001_enable_citext_extension.php:15`).
