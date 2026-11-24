# ADR-0001: Registrar decisiones arquitectónicas con ADR

- **Estado:** Aceptado
- **Fecha:** 2026-11-24

## Contexto
MediCore es un proyecto de aprendizaje de larga duración. Sin registro, se pierde el *porqué* de las decisiones, que es justo lo que vuelve opaco un sistema legacy.

## Opciones consideradas
1. Wiki externa: separada del código, se desactualiza.
2. Comentarios en el código: no capturan las alternativas descartadas.
3. ADR en Markdown dentro del repositorio: versionados junto al código y revisables.

## Decisión
Opción 3: un ADR numerado por decisión en `docs/adr/`, según la plantilla `0000-plantilla.md`. Un ADR no se edita una vez aceptado: se reemplaza con otro nuevo.

## Consecuencias
Exige disciplina para escribir uno por cada decisión relevante, pero el historial queda auditable.
