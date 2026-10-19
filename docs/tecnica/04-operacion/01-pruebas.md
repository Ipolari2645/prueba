---
titulo: Pruebas automatizadas: ejecución y cobertura funcional
tipo: guia
area: tecnica/04-operacion
version: 1.1.0
estado: borrador
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - tests/
  - phpunit.xml
requisitos: []
---

# Pruebas automatizadas: ejecución y cobertura funcional

Cómo ejecutar la suite y qué comportamiento del sistema cubre cada archivo de `tests/`.

## 1. Supuestos y requisitos previos

| Requisito | Versión/valor | Cómo comprobarlo |
|---|---|---|
| PHP | `^8.3` (CI usa 8.4) | `php -v` |
| Extensiones PHP | `pdo_pgsql`, `mbstring`, `bcmath`, `intl`, `gd`, `zip` | `php -m` |
| Composer | Dependencias instaladas | `composer install` |
| Base de datos | PostgreSQL 16 accesible en `127.0.0.1:5432`, base `filam_test` | `php artisan db:show --database=pgsql` |
| Variables de prueba | Definidas en `phpunit.xml` (`DB_DATABASE=filam_test`, `MAIL_MAILER=array`, `QUEUE_CONNECTION=sync`) | `grep -n 'DB_DATABASE\|MAIL_MAILER\|QUEUE_CONNECTION' phpunit.xml` |

La suite **no usa SQLite**: `phpunit.xml` fija `DB_CONNECTION=pgsql` porque el dominio usa `citext`,
`timestamptz` y funciones propias de PostgreSQL (`app/Services/OrderService.php`).

## 2. Pasos

1. Instalar dependencias y preparar el entorno.

   ```bash
   composer install
   php -r "file_exists('.env') || copy('.env.example', '.env');"
   php artisan key:generate
   ```

2. Crear la base de datos de pruebas (una sola vez).

   ```bash
   createdb -h 127.0.0.1 -U filam filam_test
   ```

3. Ejecutar la suite completa.

   ```bash
   php artisan test --compact
   ```

4. Ejecutar solo lo afectado por un cambio (uso diario).

   ```bash
   php artisan test --compact tests/Feature/CartTest.php
   php artisan test --compact --filter=test_confirmar_creates_order_with_frozen_prices_and_comprobante
   ```

5. Repetir el paso 4 después de cada edición hasta que pase, y solo entonces correr el paso 3.

## 3. Resultado esperado

Toda la suite en verde y el resumen con el total de pruebas:

```text
   PASS  Tests\Feature\...
   ...
  Tests:    56 passed (164 assertions)
```

Cualquier `FAIL`/`ERROR` se investiga antes de continuar. Las pruebas de documentación
(`tests/Feature/ValidarDocumentacionTest.php`) fallan si alguien edita un documento incumpliendo el
estándar, no si el código de negocio cambia.

## 4. Reversión

Las pruebas usan `RefreshDatabase`, así que `filam_test` queda migrada y con los datos transaccionales
revertidos. Para dejarla en estado limpio:

```bash
php artisan migrate:fresh --database=pgsql --env=testing
```

No hay que respaldar `filam_test`: es descartable. Lo que no se debe tocar es la base de desarrollo o
producción (las pruebas se niegan a correr contra otra base porque `phpunit.xml` fuerza `filam_test`).

## 5. Errores conocidos

| Error | Causa | Solución |
|---|---|---|
| `SQLSTATE[08006] connection to server at "127.0.0.1" ... failed` | No hay PostgreSQL corriendo o no existe `filam_test` | Levantar el servicio y `createdb`; verificar usuario/clave `filam` |
| `could not find driver` | Falta `pdo_pgsql` | Instalar/habilitar la extensión (`php -m` debe listarla) |
| `Call to undefined function ... pg_advisory_xact_lock` | Se cambió la conexión de la suite a SQLite | Restaurar `DB_CONNECTION=pgsql` en `phpunit.xml` |
| `Unable to locate file in Vite manifest` al renderizar vistas | Assets sin compilar | `npm run build` (ver [02-tooling-y-ci.md](02-tooling-y-ci.md)) |
| Pruebas de documentación en rojo tras tocar `docs/` | Un documento incumple el estándar | `php artisan docs:validar` y corregir los errores listados |

## 6. Cobertura funcional por archivo de prueba

| Archivo de prueba | Pruebas | Qué comportamiento verifica | Dónde está especificado |
|---|---|---|---|
| `tests/Feature/AdminTest.php` | 4 | El panel exige autenticación y rol; un `Cliente` no entra; un `Administrador` crea categorías y productos con imagen | `tecnica/02-modulos/02-panel-admin.md` §2 y §3 |
| `tests/Feature/AuthTest.php` | 4 | Login renderiza y autentica; credenciales inválidas se rechazan; registro crea usuario | `tecnica/01-arquitectura/01-topologia-y-patron.md` §3.3 |
| `tests/Feature/CartTest.php` | 6 | Carrito exige sesión; agregar producto; confirmar pedido congela precios y emite comprobante; aplica descuento preferencial; rechaza productos sin precio | `tecnica/02-modulos/03-servicios-core.md` §2 y §3 |
| `tests/Feature/CatalogTest.php` | 8 | Home lista solo destacados; catálogo solo activos; búsqueda y filtro por categoría; componente embebido; detalle por `slug`; página de descargas; precio nulo muestra «por consultar» | `tecnica/02-modulos/01-storefront-publico.md` §2 y §3 |
| `tests/Feature/ContactoTest.php` | 3 | El formulario guarda el mensaje y envía correo; exige consentimiento; la página renderiza | `tecnica/02-modulos/01-storefront-publico.md` §3.6 y §4 |
| `tests/Feature/ExampleTest.php` | 1 | La ruta raíz responde 200 | Plantilla por defecto del framework; candidata a eliminar |
| `tests/Feature/FaseSieteTest.php` | 4 | Solicitud de stock se persiste; reclamo se registra con correlativo y copias; páginas legales y de admin renderizan | `tecnica/02-modulos/01-storefront-publico.md` §3.7 y §3.8 |
| `tests/Feature/PedidoAdminTest.php` | 2 | El admin avanza el estado del pedido con registro de auditoría; una transición inválida se rechaza | `tecnica/02-modulos/03-servicios-core.md` §3 |
| `tests/Feature/PedidoTest.php` | 2 | El cliente ve y descarga su comprobante; no puede ver el pedido de otro cliente | `tecnica/02-modulos/01-storefront-publico.md` §2.3 |
| `tests/Feature/PerfilTest.php` | 6 | Perfil exige sesión y renderiza; actualización de datos; cambio de contraseña correcto e incorrecto; DNI único entre clientes | `tecnica/02-modulos/01-storefront-publico.md` §3.5 |
| `tests/Feature/RbacTest.php` | 4 | `Administrador` gestiona catálogo; `Operador` no gestiona catálogo pero entra al panel; `Cliente` no entra; `Cliente` solo ve sus pedidos | `tecnica/02-modulos/02-panel-admin.md` §2 |
| `tests/Feature/SeguridadTest.php` | 4 | Cabeceras de seguridad presentes; `Cliente` no accede a gestión de productos; invitado no accede al área privada; subida de archivo inválida se rechaza | `tecnica/01-arquitectura/01-topologia-y-patron.md` §3.1 |
| `tests/Feature/ValidarDocumentacionTest.php` | 7 | El validador detecta front-matter incompleto, encabezados duplicados, enlaces rotos, mapa de cobertura ausente o desactualizado, e inventaría clases y métodos | `ESTANDAR_DOCUMENTACION.md` §14 |
| `tests/TestCase.php` | 0 | Clase base de las pruebas de feature (sin casos propios) | — |
| `tests/Unit/ExampleTest.php` | 1 | Aserción trivial del framework (`true` es `true`) | Plantilla por defecto del framework; candidata a eliminar |

Además, `.github/workflows/ci.yml` ejecuta esta misma suite en cada `push` y `pull_request` contra un
servicio PostgreSQL 16, por lo que el resultado local y el de CI deben coincidir (56 pruebas).

## 7. Validación de la guía

| Campo | Valor |
|---|---|
| Última ejecución real | |
| Ejecutada por | |
| Entorno | |
| Resultado | |
