---
titulo: Índice del área de operación
tipo: indice
area: tecnica/04-operacion
version: 1.1.0
estado: vigente
autor: Hans (Filam CentroPlas)
fecha: 2026-10-19
commit_auditado: 31b565d
alcance:
  - docs/tecnica/04-operacion/
requisitos: []
---

# Operación

Procedimientos reproducibles para ejecutar, verificar y mantener la plataforma. No describe reglas
de negocio ni arquitectura: para eso están `tecnica/01-arquitectura/` y `tecnica/02-modulos/`.

## Índice

| Documento | Qué resuelve |
|---|---|
| [01-pruebas.md](01-pruebas.md) | Ejecutar la suite, interpretar sus resultados y saber qué cubre cada archivo de `tests/` |
| [02-tooling-y-ci.md](02-tooling-y-ci.md) | Compilar assets, ejecutar Pint/PHPStan, validar la documentación y entender los workflows de CI |

## Regla de oro

Toda guía de esta área debe haberse ejecutado al menos una vez en el entorno que declara. La tabla
«Validación de la guía» de cada documento registra fecha, autor, entorno y resultado. Una guía sin
esa evidencia se marca `estado: borrador`.
