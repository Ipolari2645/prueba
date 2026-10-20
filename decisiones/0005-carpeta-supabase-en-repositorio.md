---
titulo: "ADR-0005: Carpeta supabase/ presente en el repositorio sin uso en la aplicación"
tipo: adr
area: decisiones
version: 1.0.0
estado: borrador
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - supabase/
  - .gitignore
  - PLAN_FASE_3.md
requisitos: []
---

# ADR-0005: Carpeta supabase/ presente en el repositorio sin uso en la aplicación

## Estado

`propuesto` — 2026-10-19. La decisión de conservar o retirar la carpeta no está confirmada por ninguna persona responsable; este ADR queda en `borrador` hasta que se confirme.

## Contexto

En la raíz del repositorio recibido existe la carpeta `supabase/`. Su único contenido es `supabase/.temp/linked-project.json`: el archivo que genera la CLI de Supabase al vincular un proyecto, con la referencia del proyecto (`ref`), su nombre (`filam-centroplas`) y los identificadores de la organización. No hay migraciones, configuración de servicios ni semillas dentro de `supabase/`. `.gitignore` no la excluye. Con solo el contenido del zip no se puede saber si el archivo está versionado en Git; se comprueba con `git ls-files supabase`.

El resto de la documentación descarta Supabase:

- `PLAN_FASE_3.md:22` fija el almacenamiento como "disco local protegido (sin S3/Supabase)".
- `docs/DDS_FilamCentroPlas_v1.10.md:114` (ADR-007 del DDS, estado Aceptado) fija el almacenamiento local protegido en el VPS, sin servicios externos. Las versiones 1.3 y anteriores del DDS sí mencionaban Supabase Storage como opción.

La base de datos de la aplicación es PostgreSQL gestionado con migraciones de Laravel: conexión `pgsql` (`.env.example:23`) y esquema en `database/migrations/`. Ningún archivo de `app/`, `config/` ni `routes/` hace referencia a Supabase.

## Decisión

Pendiente. Se propone conservar el hecho verificado (la carpeta no cumple función en la aplicación) y que el responsable del proyecto elija una de las opciones de la tabla.

## Alternativas evaluadas

| Alternativa | Ventajas | Motivo de descarte |
|---|---|---|
| Mantener la carpeta tal como está | No requiere ningún cambio | El repositorio expone la referencia y la organización del proyecto de Supabase; la carpeta puede hacer pensar que Supabase forma parte de la infraestructura |
| Agregar `supabase/` a `.gitignore` | Evita que se vuelva a versionar | Si el archivo ya está versionado, hay que quitarlo del índice con `git rm --cached` además de ignorarlo |
| Eliminar la carpeta | Deja el repositorio coherente con ADR-007 del DDS | Pierde el vínculo con el proyecto de Supabase si algún día se retoma su uso |

Estas opciones son comparables desde el punto de vista técnico; el repositorio no registra cuál evaluó el equipo.

## Consecuencias

- **Si se mantiene:** la exposición de los identificadores del proyecto y de la organización continúa, y debe respetarse la regla de que todo cambio de esquema vive solo en `database/migrations/`.
- **Si se ignora o elimina:** hay que verificar antes con el responsable del proyecto que la CLI de Supabase ya no se usa en ninguna máquina de desarrollo.
- **Quién confirma:** ningún documento del repositorio nombra a la persona. `docs/README.md:43-49` atribuye la titularidad de la documentación a un "Responsable del proyecto" sin nombre, y `docs/README.md:7` y `docs/ESTANDAR_DOCUMENTACION.md:7` figuran a nombre de Hans (Filam CentroPlas). La asignación debe hacerla el equipo.
- **Trabajo derivado:** registrar la confirmación en este ADR (estado `aceptado` o `reemplazado por`) y actualizar el apartado de ADR sin registrar en `docs/README.md:117`, que hoy menciona "Supabase en el repo".
