---
titulo: Tooling y CI: assets, análisis estático, estilo y documentación
tipo: guia
area: tecnica/04-operacion
version: 1.1.0
estado: borrador
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - .github/workflows/
  - .editorconfig
  - .gitattributes
  - composer.json
  - package.json
  - phpstan.neon
  - phpunit.xml
  - vite.config.js
  - config/
  - app/Console/Commands/
  - resources/css/app.css
  - resources/js/app.js
requisitos: []
---

# Tooling y CI: assets, análisis estático, estilo y documentación

Comandos que debe ejecutar cualquier cambio antes de subirse, y qué hace cada workflow de
`.github/workflows/`.

## 1. Supuestos y requisitos previos

| Requisito | Versión/valor | Cómo comprobarlo |
|---|---|---|
| PHP | `^8.3` (CI usa 8.4) | `php -v` |
| Composer | Dependencias instaladas | `composer install` |
| Node.js | `22` en CI (local puede ser superior) | `node -v` |
| npm | Dependencias instaladas | `npm install` |
| Base de datos | PostgreSQL 16 (para pruebas y para `docs:validar` no hace falta) | `php artisan db:show` |

## 2. Pasos

1. Compilar assets para que las vistas rendericen (Vite + Tailwind 4).

   ```bash
   npm install
   npm run build
   ```

   Durante el desarrollo, en lugar del build: `npm run dev`.

2. Formatear el código PHP con Pint (obligatorio si se tocó algún `.php`).

   ```bash
   vendor/bin/pint --dirty --format agent
   ```

3. Ejecutar el análisis estático (Larastan/PHPStan, nivel 5 sobre `app` y `routes`).

   ```bash
   vendor/bin/phpstan analyse --no-progress
   ```

4. Ejecutar las pruebas (ver [01-pruebas.md](01-pruebas.md)).

   ```bash
   php artisan test --compact
   ```

5. Validar la documentación contra el estándar y regenerar el mapa de cobertura.

   ```bash
   php artisan docs:mapa
   php artisan docs:validar --docblocks
   ```

6. Repetir 2-5 hasta que todo esté limpio; el mismo conjunto corre en CI.

## 3. Resultado esperado

| Comando | Salida esperada |
|---|---|
| `npm run build` | `built in ...` y archivos en `public/build/` |
| `vendor/bin/pint --dirty --format agent` | Sin cambios pendientes (o los aplica) |
| `vendor/bin/phpstan analyse --no-progress` | `[OK] No errors` |
| `php artisan test --compact` | `Tests: 56 passed` |
| `php artisan docs:validar --docblocks` | `Documentos validados: N · Errores: 0` |

## 4. Reversión

- `public/build/` y `node_modules/` están ignorados por git: `rm -rf public/build node_modules && npm install && npm run build`.
- Pint reescribe archivos: revertir con `git checkout -- .` si el formato no era del agrado (no debería pasar; el formato es el del proyecto).
- `docs/cobertura/` es generado: se restaura con `php artisan docs:mapa`, nunca a mano.

## 5. Errores conocidos

| Error | Causa | Solución |
|---|---|---|
| `Unable to locate file in Vite manifest: resources/...` | No se compiló o se editó `vite.config.js` | `npm run build` |
| `Target class [vite] does not exist` en pruebas | `APP_ENV` distinto de `testing` | Ejecutar con `php artisan test`, que carga `phpunit.xml` |
| PHPStan `Allowed memory size exhausted` | Análisis sobre todo el proyecto | `php -d memory_limit=1G vendor/bin/phpstan analyse --no-progress` |
| Pint cambia archivos ajenos al cambio | Se usó `vendor/bin/pint` sin `--dirty` | Usar `--dirty` para formatear solo lo modificado |
| `docs:validar` falla con «mapa desactualizado» | Se tocó código y no se regeneró el mapa | `php artisan docs:mapa` y commitear `docs/cobertura/` |

## 6. Integración continua

| Workflow | Disparador | Jobs | Qué verifica |
|---|---|---|---|
| `.github/workflows/ci.yml` | `push`/`pull_request` a `main` y `develop` | `Tests`, `Static analysis and style` | Suite completa contra PostgreSQL 16 (PHP 8.4, Node 22) y, en el segundo job, `pint --test` y PHPStan |
| `.github/workflows/documentacion.yml` | `push`/`pull_request` a `main` y `develop` | `Validar documentación y generar referencia` (id `documentacion`) | `php artisan docs:validar --docblocks`, genera la referencia con phpDocumentor y publica el artefacto `referencia-codigo` |

Ambos workflows fallan el PR si su job correspondiente falla. El detalle de la referencia generada
está en [`docs/referencia/README.md`](../../referencia/README.md).

## 7. Configuración de la aplicación

| Archivo | Qué configura en este proyecto |
|---|---|
| `config/app.php` | Nombre, entorno, clave, URL y zona horaria de la aplicación |
| `config/auth.php` | Guard `web` con sesión sobre `App\Models\User` y recuperación de contraseña |
| `config/cache.php` | Store por defecto (`database`/`redis` según entorno) |
| `config/database.php` | Conexión por defecto PostgreSQL (`pgsql`) y conexiones alternas |
| `config/filam.php` | Datos comerciales de FILAM: correos de ventas y atención, WhatsApp, teléfono, web y dirección |
| `config/filesystems.php` | Discos de archivos (`local`, `public`, `s3`) usados por imágenes de producto |
| `config/fortify.php` | Guard, broker de contraseñas, prefijo de rutas, features y vistas de autenticación |
| `config/horizon.php` | Supervisores y colas de Laravel Horizon (`supervisor-1`, cola `default`) |
| `config/logging.php` | Canales de log y su retención |
| `config/mail.php` | Mailers (SMTP en producción, `array` en pruebas) |
| `config/queue.php` | Conexiones de cola (base de datos, `sync` en pruebas) |
| `config/services.php` | Credenciales de servicios de terceros |
| `config/session.php` | Driver, duración y cookies de sesión |

Las variables de entorno reales viven en `.env` (no versionado); `.env.example` es la plantilla
publicada en el repositorio.

### 7.1. Assets de frontend

Vite compila dos entradas, `resources/css/app.css` y `resources/js/app.js` (`vite.config.js:9`), cargadas con `@vite` en los cuatro layouts (`layouts/admin`, `layouts/app`, `layouts/front`, `layouts/guest`).

| Archivo | Qué contiene |
|---|---|
| `resources/css/app.css` | Importa Tailwind y las fuentes Barlow e Inter desde Google Fonts; registra rutas `@source` para la paginación y las vistas compiladas; define en `@theme` la tipografía y la paleta de marca (`--color-brand`, `--color-accent`, `--color-night`, `--color-surface`, `--color-ink`, `--color-muted`, `--color-success`, `--color-danger`, `--color-info`); aplica `--font-heading` a `h1`-`h4` y oculta `[x-cloak]` |
| `resources/js/app.js` | Contiene solo un comentario (`//`); no hay lógica JavaScript propia. No verificado: el origen del JavaScript de Livewire y Alpine que usan las vistas |

Nota: las fuentes se piden a un dominio externo en tiempo de ejecución, lo que implica una dependencia de red y un envío de la IP del visitante a un tercero. Está fuera del alcance de la norma, pero conviene tenerlo presente respecto a la Ley 29733.

### 7.2. Comandos Artisan de documentación

Ambos viven en `app/Console/Commands/` y no requieren base de datos.

| Comando | Opciones | Qué hace | Código de salida |
|---|---|---|---|
| `php artisan docs:mapa` (`GenerarMapaDocumentacion`) | `--verificar`, `--base=` | Sin opciones, regenera los seis archivos de `docs/cobertura/` con `MapaDocumentacionService::escribir()` e informa los artefactos sin documento. Con `--verificar` solo comprueba que lo que hay en disco coincida con lo generado | `0` correcto; `1` solo con `--verificar` si el mapa está desactualizado |
| `php artisan docs:validar` (`ValidarDocumentacion`) | `--estricto`, `--docblocks`, `--base=` | Valida front-matter (campos `titulo, tipo, area, version, estado, autor, fecha, commit_auditado, alcance`; `requisitos` además en `modulo` y `entidad`), tipos y estados permitidos, nombrado, encabezados, enlaces, citas a archivos del repo, que `docs/cobertura/` esté actualizado y el porcentaje de docblocks en `app/` | `0` sin errores; `1` si hay errores, o avisos con `--estricto` |

Detalles verificados en `ValidarDocumentacion`: los estados válidos son `borrador`, `en-revision`, `vigente` y `obsoleto`; los tipos válidos incluyen `glosario` además de los del estándar; un mapa de cobertura ausente o desactualizado es un **error**, mientras que los artefactos sin documento y el porcentaje de docblocks son **avisos**. `--docblocks` solo añade el detalle de archivos sin docblock de clase. La prueba que cubre este comando es `tests/Feature/ValidarDocumentacionTest.php` (7 pruebas).

### 7.3. Deuda de docblocks en `app/` (T5)

El validador oficial informa el porcentaje real con `php artisan docs:validar --docblocks` (aviso, no error). Las cifras de esta sección se contaron sobre el inventario generado `docs/cobertura/02-clases-y-metodos.md` (columna `Docblock`): en `app/` hay 245 métodos y 75 tienen docblock (170 sin él). La cifra de clases sin docblock (64 de 67) proviene del encargo y no se recontó aquí. Para priorizar, usa la lista viva con `grep '| no |' docs/cobertura/02-clases-y-metodos.md`.

| Prioridad | Archivo | Métodos sin docblock | Motivo |
|---|---|---|---|
| 1 | `app/Services/CartService.php` | 9 de 12 | Lógica de negocio de carrito y descuento; los servicios cambian a menudo |
| 2 | `app/Livewire/Cart.php` | 6 de 6 | Confirmación de pedido (flujo crítico) |
| 3 | `app/Models/Producto.php` | 8 de 9 | Slug, precio «por consultar» y `scopeActivos()` |
| 4 | `app/Policies/PedidoPolicy.php`, `ProductoPolicy.php`, `UserPolicy.php` | 5 a 6 en cada una | Reglas de autorización (ver hallazgo RBAC en [02-panel-admin.md](../02-modulos/02-panel-admin.md)) |
| 5 | `app/Livewire/Admin/DescuentoIndex.php`, `CategoriaIndex.php`, `UsuarioIndex.php`, `ProductoForm.php`, `BannerIndex.php` | 4 a 6 en cada uno | Acciones del panel |
| 6 | `app/Console/Commands/ValidarDocumentacion.php` | 8 de 14 | Herramienta de documentación |

| # | Hecho verificado | Cita | Impacto | Recomendación |
|---|---|---|---|---|
| 1 | La mayoría de clases no tiene docblock de clase y muchos métodos tampoco | `app/`, `ValidarDocumentacion::validarDocblocks()` | La norma exige docblocks en código nuevo o modificado (`docs/ESTANDAR_DOCUMENTACION.md` §1.2); el comando lo reporta solo como aviso, así que no bloquea la CI salvo con `--estricto` | Documentar por prioridad, en el mismo cambio que toque cada archivo; no se modificó código en esta tarea |

## 8. Validación de la guía

| Campo | Valor |
|---|---|
| Última ejecución real | |
| Ejecutada por | |
| Entorno | |
| Resultado | |
