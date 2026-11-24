# ADR-0003: Selección del motor de base de datos

**Fecha:** 2026-11-24  
**Estado:** Propuesto

## Contexto

MediCore requiere una base de datos relacional que permita la coexistencia del sistema legacy y el sistema moderno, así como la práctica de transacciones, concurrencia y migración de funcionalidades.

## Alternativas consideradas

- **PostgreSQL:** código abierto, gratuito y con alta extensibilidad.
- **SQL Server:** integración con el ecosistema .NET, soporte de T-SQL y disponibilidad de una edición gratuita para desarrollo.

## Decisión

Se utilizará SQL Server Developer debido a su integración con ASP.NET Core y al interés de adquirir experiencia en tecnologías utilizadas en entornos empresariales basados en Microsoft.

## Consecuencias positivas

- Compatibilidad con ADO.NET y Entity Framework Core.
- Aprendizaje de T-SQL y procedimientos almacenados.
- Posibilidad de experimentar con transacciones, concurrencia y bloqueos.
- Sin costos de licenciamiento para desarrollo y pruebas.

## Consecuencias negativas

- Mayor dependencia del ecosistema Microsoft.
- Restricciones de licenciamiento para producción.
- Dependencia de T-SQL que dificultaría una futura migración hacia otro motor.

## Restricciones

Ambos sistemas compartirán inicialmente una instancia y una base de datos SQL Server. Su separación se evaluará durante las fases posteriores de modernización.
