---
titulo: "ADR-0001: Serializar el correlativo de pedidos con pg_advisory_xact_lock(20261006)"
tipo: adr
estado: vigente
autor: "Ipolari2645"
fecha: "2026-10-19"
commit_auditado: "bd451e2"
alcance:
  - app/Services/OrderService.php
  - app/Livewire/ReclamacionForm.php
---

## 1. Contexto

Cada pedido recibe un código correlativo anual (`PED-AAAA-NNNNN`) y de él se deriva el número del comprobante interno (`CI-AAAA-NNNNN`). El código se calcula leyendo el mayor correlativo del año y sumando uno. Sin serialización, dos pedidos concurrentes pueden leer el mismo máximo y generar el mismo código, lo que viola la unicidad de `pedidos.codigo_correlativo`.

## 2. Decisión

`OrderService::crear()` ejecuta todo su trabajo dentro de `DB::transaction()`. Dentro de esa transacción, `OrderService::siguienteCorrelativo()` ejecuta `SELECT pg_advisory_xact_lock(20261006)` mediante `DB::select()` solo cuando el driver de la conexión es `pgsql`. Después lee el mayor `codigo_correlativo` con prefijo `PED-AAAA-`, suma uno y rellena con ceros a 5 dígitos.

El número del comprobante no tiene su propio cálculo: `OrderService::crear()` lo obtiene reemplazando `PED-` por `CI-` en el código del pedido.

El mismo patrón se repite con la clave `20261007` para el correlativo del Libro de Reclamaciones (`ReclamacionForm`).

## 3. Alternativas evaluadas

- **Secuencias de base de datos (`CREATE SEQUENCE`):** descartada porque una secuencia no revierte su incremento cuando la transacción hace rollback, lo que deja saltos en la numeración, y porque exigiría reiniciarla cada año.
- **Bloqueo de tabla (`LOCK TABLE pedidos IN EXCLUSIVE MODE`):** descartada porque bloquearía también las lecturas concurrentes del panel administrativo y del área del cliente.

## 4. Consecuencias

- **Positivas:** el candado solo serializa a las transacciones que crean pedidos y se libera automáticamente al terminar la transacción (`xact`). Un rollback no consume número.
- **Negativas:** la serialización existe únicamente en PostgreSQL; con otro driver el bloqueo se omite sin error y el riesgo de duplicados reaparece. Por eso la suite de pruebas usa PostgreSQL (`phpunit.xml`, `DB_CONNECTION`).
- **Negativas:** las claves de bloqueo son constantes numéricas sin registro central (`20261006` pedidos, `20261007` reclamaciones); cualquier clave nueva debe documentarse aquí para evitar colisiones.
- **Negativas:** el formato rellena a 5 dígitos y el máximo se toma como texto, por lo que el esquema soporta hasta 99999 pedidos por año.
