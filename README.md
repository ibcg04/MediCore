# MediCore

Proyecto de aprendizaje: simular la **migración incremental** de un sistema hospitalario legacy a **ASP.NET Core + C#**.

> Todos los datos son **ficticios**. Ningún dato real de pacientes debe entrar en este repositorio.

## Objetivo
No solo construir una aplicación: aprender ingeniería de software resolviendo problemas reales de migración, consistencia, concurrencia y sistemas distribuidos.

## Estructura del repositorio

```
MediCore/
├── README.md
├── docs/
│   ├── roadmap.md                      # Fases, problema que resuelve cada una, retos
│   ├── legacy/
│   │   ├── requisitos-funcionales.md   # Qué hace el sistema legacy
│   │   └── antipatrones.md             # Defectos deliberados del legacy (la "deuda")
│   ├── adr/                            # Architecture Decision Records
│   │   ├── 0000-plantilla.md
│   │   └── 0001-registrar-decisiones-con-adr.md
│   ├── learning/
│   │   └── diario.md                   # Diario de aprendizaje (lo escribes tú)
│   └── challenges/                     # Un archivo por reto resuelto
├── legacy/                             # Sistema legacy (Fase 1)
├── src/                                # Sistema nuevo ASP.NET Core (Fase 3+)
├── tests/                              # Caracterización, unitarios, integración
├── data/seed/                          # Scripts de datos ficticios
└── infra/                              # Docker, compose, k8s (solo cuando se justifique)
```

Las carpetas `legacy/`, `src/`, `tests/`, `data/` e `infra/` se crean cuando su fase empieza, no antes.

## Cómo navegar
1. Lee `docs/roadmap.md`.
2. Lee `docs/legacy/requisitos-funcionales.md`.
3. Cada decisión relevante → nuevo ADR en `docs/adr/`.
4. Cada sesión de trabajo → entrada en `docs/learning/diario.md`.
