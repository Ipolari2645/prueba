---
titulo: "ADR-0005: Mantener la carpeta supabase/ dentro del repositorio"
tipo: adr
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - supabase/
  - config/database.php
---

## 1. Contexto

El repositorio conserva en la raíz la carpeta `supabase/`. Su contenido verificado es únicamente `supabase/.temp/linked-project.json`, el archivo que genera la CLI de Supabase al vincular un proyecto (nombre del proyecto e identificadores de organización). No contiene migraciones, configuración de servicios ni semillas. `.gitignore` no la excluye.

La base de datos del sistema es PostgreSQL gestionado con migraciones de Laravel: la conexión por defecto del entorno es `pgsql` (`.env.example:23`) y el esquema vive en `database/migrations/`. Los documentos de diseño descartan el uso de Supabase para almacenamiento: `docs/DDS_FilamCentroPlas_v1.4.md:103` (ADR-007) fija el almacenamiento local protegido en el VPS, y `PLAN_FASE_3.md:22` repite "sin S3/Supabase".

## 2. Decisión

Mantener la carpeta `supabase/` versionada tal como está. No cumple ninguna función en la aplicación en ejecución, y todo cambio de esquema se gestiona exclusivamente en `database/migrations/`.

## 3. Alternativas evaluadas

- **Eliminar la carpeta o agregarla a `.gitignore`:** no adoptada por ahora; queda pendiente que el responsable del proyecto confirme que la CLI de Supabase ya no se usa para este proyecto.

## 4. Consecuencias

- **Positivas:** se conserva el vínculo con el proyecto de Supabase por si se retoma su uso.
- **Negativas:** el repositorio expone los identificadores del proyecto y de la organización de Supabase contenidos en `linked-project.json`.
- **Negativas:** la carpeta puede inducir a pensar que Supabase forma parte de la infraestructura, cuando los documentos de diseño lo descartan.
- **Negativas:** debe respetarse la regla de no duplicar migraciones fuera de `database/migrations/`.
