# ADR-0002: Uso de ASP.NET Core para el sistema legacy

**Fecha:** 2026-11-24  
**Estado:** Aceptado

## Contexto

El proyecto MediCore tiene como objetivo simular la migración progresiva de un sistema hospitalario legacy hacia una arquitectura moderna.

Se necesita seleccionar una tecnología para desarrollar el sistema legacy que permita experimentar problemas reales de migración sin aumentar innecesariamente la complejidad del entorno de desarrollo.

## Decisión

Se utilizará ASP.NET Core tanto para el sistema legacy como para el sistema nuevo, manteniendo ambos como aplicaciones independientes.

Esta decisión se toma porque el objetivo principal del proyecto es aprender sobre migración de funcionalidades, arquitectura de software, manejo de datos y coexistencia entre sistemas, utilizando las tecnologías requeridas para mi desarrollo profesional.

## Alternativas consideradas

- **.NET Framework:** proporciona mayor realismo, pero aumenta la complejidad de configuración.
- **ASP.NET Core:** facilita el desarrollo de ambos sistemas y permite concentrarse en los problemas arquitectónicos de la migración.

## Consecuencias positivas

- Menor complejidad en la configuración del entorno.
- Mayor familiarización con C# y ASP.NET Core.
- Facilidad de integración mediante YARP.
- Mayor enfoque en los problemas de migración y consistencia de datos.

## Consecuencias negativas

- Menor realismo tecnológico del sistema legacy.
- No se practicará directamente la migración de .NET Framework a ASP.NET Core.
- Existe el riesgo de implementar un sistema legacy demasiado bien estructurado.
- Compartir inicialmente la base de datos genera un acoplamiento entre ambos sistemas, por lo que una modificación del esquema podría afectar el funcionamiento de cualquiera de ellos.

## Restricciones

Para representar las limitaciones de un sistema legacy:

- La lógica de negocio estará dentro de los controladores.
- Se utilizará ADO.NET en lugar de Entity Framework.
- No se aplicará una arquitectura por capas.
- No se utilizará inyección de dependencias para los componentes de negocio propios del legacy. Se recurrirá a clases estáticas y a la instanciación directa de objetos mediante `new`.
- Existirá un alto acoplamiento entre componentes.
- Ambos sistemas tendrán soluciones independientes.
- Se compartirá inicialmente la base de datos.
- El legacy contará con un mecanismo de autenticación propio y gestión de sesiones en memoria, con el propósito de abordar la autenticación compartida durante la Fase 4.
- Se mantendrán los antipatrones AP-01 (SQL concatenado) y AP-07 (contraseñas débiles) exclusivamente con fines educativos, limitados a un máximo de dos puntos documentados por antipatrón, dentro de un entorno local y aislado con datos ficticios.
- Las vulnerabilidades intencionales deberán ser identificadas y corregidas durante la modernización de las funcionalidades correspondientes. El sistema vulnerable no deberá exponerse públicamente.

## Criterios de verificación

Antes de finalizar la Fase 1:

- El sistema legacy deberá implementar al menos 8 de los 10 antipatrones definidos en `docs/legacy/antipatrones.md`.
- Cada antipatrón implementado deberá contar con una ubicación documentada dentro del código y una explicación de sus consecuencias.
- Se deberá verificar que las vulnerabilidades intencionales permanezcan dentro de los límites establecidos.
- Se deberá comprobar que las restricciones arquitectónicas establecidas en este ADR se hayan respetado.

Estos criterios permitirán garantizar que el sistema legacy presente suficientes problemas arquitectónicos para justificar su posterior modernización.
