---
titulo: "ADR-0001: Serializar los correlativos de pedidos y reclamaciones con pg_advisory_xact_lock"
tipo: adr
area: decisiones
version: 1.0.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - app/Services/OrderService.php
  - app/Livewire/ReclamacionForm.php
  - database/migrations/2026_10_06_100011_create_pedidos_table.php
  - phpunit.xml
  - config/app.php
requisitos: []
---

# ADR-0001: Serializar los correlativos de pedidos y reclamaciones con pg_advisory_xact_lock

## Estado

`aceptado` — 2026-10-19. Registro retroactivo de una decisión ya implementada en el código auditado.

## Contexto

Cada pedido recibe un código correlativo anual `PED-AAAA-NNNNN`; el número del comprobante interno se deriva de ese código (`CI-AAAA-NNNNN`). El código se calcula leyendo el mayor correlativo del año y sumando uno (`OrderService::siguienteCorrelativo()`, `app/Services/OrderService.php:62-78`). Sin serialización, dos transacciones concurrentes pueden leer el mismo máximo y calcular el mismo código.

La columna `pedidos.codigo_correlativo` tiene índice único (`database/migrations/2026_10_06_100011_create_pedidos_table.php:14`), de modo que un duplicado no se guardaría en silencio: la segunda transacción fallaría con una violación de unicidad.

El Libro de Reclamaciones usa el mismo patrón con el formato `LR-AAAA-NNNN` (`ReclamacionForm::registrar()`, `app/Livewire/ReclamacionForm.php:79-93`).

## Decisión

`OrderService::crear()` ejecuta todo su trabajo dentro de `DB::transaction()` (`app/Services/OrderService.php:20`). Dentro de esa transacción, `OrderService::siguienteCorrelativo()` ejecuta `SELECT pg_advisory_xact_lock(?)` con el valor `20261006` mediante `DB::select()` (`:68`), y solo cuando el driver de la conexión es `pgsql` (`:67`). A continuación lee el mayor `codigo_correlativo` con prefijo `PED-AAAA-`, suma uno y rellena con ceros a 5 dígitos (`:71-77`).

El número de comprobante no tiene cálculo propio: `OrderService::crear()` reemplaza `PED-` por `CI-` en el código del pedido (`:54`).

`ReclamacionForm::registrar()` repite el patrón dentro de su propia transacción con la clave `20261007` (`app/Livewire/ReclamacionForm.php:80-81`) y un correlativo de 4 dígitos (`:84-88`).

## Alternativas evaluadas

El repositorio no contiene el acta de esta decisión. Las alternativas de la tabla son opciones técnicas comparables; los motivos son consecuencias verificables del comportamiento de PostgreSQL y de la estructura del código, no motivos declarados por el equipo. El responsable del proyecto debe confirmarlos o corregirlos.

| Alternativa | Ventajas | Motivo de descarte |
|---|---|---|
| Secuencia de PostgreSQL (`CREATE SEQUENCE`) | No exige leer el máximo existente | Una secuencia no revierte su incremento cuando la transacción hace rollback, y no se reinicia sola cada año; el formato incluye el año |
| `LOCK TABLE pedidos` | Simple de escribir | Bloquea la tabla completa para cualquier escritura concurrente, no solo para la generación del código |
| Reintento ante violación de la restricción única | No necesita bloqueo | No está implementado: el código no captura la excepción de unicidad |

## Consecuencias

- **Positivas:** el candado solo serializa a las transacciones que generan un correlativo y se libera al terminar la transacción (`xact`). Un rollback no consume número.
- **Negativas — dependencia del motor:** con un driver distinto de `pgsql` el bloqueo se omite sin error; la restricción única impediría guardar un duplicado, pero la segunda transacción fallaría. La suite de pruebas usa PostgreSQL (`phpunit.xml:26`).
- **Negativas — año en UTC:** el año del prefijo sale de `now()->year` (`app/Services/OrderService.php:64`) y la zona horaria de la aplicación es `UTC` (`config/app.php:68`). Un pedido registrado entre las 19:00 y las 24:00 (hora de Lima) del 31 de diciembre recibiría el año siguiente. Es una deducción del código; ninguna prueba la ejercita.
- **Negativas — capacidad:** el correlativo se rellena a 5 dígitos y se recupera con `substr(..., -5)` (`:75`); el esquema soporta hasta 99999 pedidos por año. El de reclamaciones soporta 9999 (`app/Livewire/ReclamacionForm.php:84`).
- **Negativas — claves sin registro central:** `20261006` (pedidos) y `20261007` (reclamaciones) son constantes numéricas repetidas en el código. Toda clave nueva debe registrarse aquí para evitar colisiones.
- **Sin pruebas de concurrencia:** `CartTest::test_confirmar_creates_order_with_frozen_prices_and_comprobante` cubre la creación del pedido (`tests/Feature/CartTest.php:41`), pero ninguna prueba ejerce dos transacciones simultáneas.
