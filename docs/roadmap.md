# Roadmap de MediCore

Cada fase responde a: **¿qué problema real resolvemos?**, **qué aprendes**, **reto personal** y **criterio de salida**.
Una tecnología solo entra cuando una fase demuestra que la necesitamos.

---

## Fase 0 — Fundamentos del proyecto
- **Problema real:** en los equipos, las decisiones se pierden y nadie recuerda *por qué* algo se hizo así. La mitad del dolor de un legacy es arqueología.
- **Aprendes:** Git y ramas, ADR, convenciones, `dotnet` CLI, `.editorconfig`.
- **Reto:** escribir el ADR-0002 eligiendo la tecnología del legacy (ver decisión pendiente abajo).
- **Salida:** repositorio Git inicializado, ADR-0001 y ADR-0002 aceptados.

## Fase 1 — Construir el sistema legacy (a propósito, mal)
- **Problema real:** no se puede migrar lo que no existe. Necesitamos un sistema con la deuda técnica típica de los hospitales: lógica en stored procedures, SQL concatenado, sin tests, acoplamiento a la base de datos compartida.
- **Aprendes:** SQL Server, stored procedures, ADO.NET, y a reconocer antipatrones porque los habrás escrito tú.
- **Alcance:** módulos Pacientes, Agenda/Citas y Admisión/Camas (Farmacia y Facturación se añaden al final de la fase).
- **Reto:** implementar RF-CIT-01 (agendar cita) *sin* protección contra doble reserva y luego provocar el bug con dos peticiones simultáneas.
- **Salida:** el legacy funciona, tiene datos ficticios y está documentado en `docs/legacy/`.

## Fase 2 — Entender el legacy: tests de caracterización
- **Problema real:** el legacy no tiene especificación fiable; *el comportamiento actual ES la especificación*, bugs incluidos. Si migras sin fijarlo, romperás cosas sin saberlo.
- **Aprendes:** tests de caracterización (golden master), tests de integración contra BD real, *seams* (Michael Feathers).
- **Reto:** descubrir y documentar 3 comportamientos "raros" que un usuario real daría por correctos.
- **Salida:** suite que captura el comportamiento de los flujos críticos.

## Fase 3 — El nuevo núcleo en ASP.NET Core
- **Problema real:** necesitamos un destino con límites claros para que no se convierta en el próximo legacy.
- **Aprendes:** ASP.NET Core, inyección de dependencias (lifetimes Singleton/Scoped/Transient), EF Core, monolito modular vs. Clean Architecture vs. capas, validación, manejo de errores (ProblemDetails).
- **Reto:** elegir y justificar la arquitectura en un ADR; implementar tú el módulo Pacientes.
- **Salida:** API nueva para Pacientes con tests, leyendo la misma BD del legacy.

## Fase 4 — Migración incremental (Strangler Fig)
- **Problema real:** un "big bang rewrite" en un hospital es inaceptable: no se puede parar la operación. Hay que migrar módulo a módulo con ambos sistemas vivos.
- **Aprendes:** patrón Strangler Fig, reverse proxy (YARP), Anti-Corruption Layer, base de datos compartida vs. separada, feature flags, rollback.
- **Reto:** enrutar `/pacientes` al sistema nuevo y el resto al legacy sin que el usuario lo note; diseñar el plan de vuelta atrás.
- **Salida:** tráfico real dividido entre legacy y nuevo.

## Fase 5 — Transacciones y concurrencia
- **Problema real:** dos recepcionistas asignan la misma cama o el mismo hueco del médico. En un hospital, esto es un incidente clínico.
- **Aprendes:** ACID, niveles de aislamiento, bloqueo optimista (`rowversion`) vs. pesimista, restricciones únicas como última defensa, deadlocks.
- **Reto:** reproducir la doble reserva con un test concurrente y corregirla de **dos** formas distintas; comparar.
- **Salida:** ADR de estrategia de concurrencia + tests que lo demuestran.

## Fase 6 — Idempotencia
- **Problema real:** la red falla, el usuario pulsa dos veces, el cliente reintenta → factura cobrada dos veces o medicamento dispensado dos veces.
- **Aprendes:** idempotency keys, semántica de HTTP, deduplicación, "exactly-once" como ilusión.
- **Reto:** hacer idempotente el registro de pago y probarlo con reintentos simulados.
- **Salida:** endpoints críticos idempotentes y documentados.

## Fase 7 — Docker
- **Justificación:** a estas alturas tendremos legacy + nuevo + SQL Server + proxy. Arrancarlo a mano es frágil y no reproducible.
- **Aprendes:** imágenes, multi-stage builds, docker compose, redes, volúmenes.
- **Reto:** que `docker compose up` levante todo el entorno desde cero con datos semilla.

## Fase 8 — Mensajería con RabbitMQ
- **Problema real:** Farmacia y Facturación migran como servicios separados; las llamadas HTTP síncronas encadenadas acoplan disponibilidad (si Facturación cae, no se puede dar de alta a un paciente).
- **Aprendes:** eventos de dominio vs. integración, Transactional Outbox, entrega at-least-once, consumidores idempotentes, dead-letter queues, consistencia eventual.
- **Reto:** demostrar el "dual write problem" (guardar en BD y publicar falla a medias) y resolverlo con Outbox.

## Fase 9 — Redis
- **Justificación:** solo si medimos un problema: varias instancias del servicio nuevo (caché local inconsistente) o consultas repetidas costosas (catálogo de medicamentos, agenda).
- **Aprendes:** cache-aside, invalidación, TTL, caché distribuida, por qué los locks distribuidos son peligrosos.
- **Reto:** medir antes/después y decidir con datos si Redis se queda.

## Fase 10 — Tolerancia a fallos
- **Problema real:** en un sistema distribuido, *algo* siempre está fallando.
- **Aprendes:** timeouts, retries con backoff + jitter, circuit breaker, bulkhead (Polly / `Microsoft.Extensions.Resilience`), health checks, observabilidad (logs estructurados, OpenTelemetry).
- **Reto:** apagar Facturación en mitad de un flujo y que el sistema degrade con elegancia.

## Fase 11 — Kubernetes (opcional, solo si se justifica)
- **Justificación:** necesidad de escalado horizontal, autorecuperación y despliegues sin corte con varios servicios. Si Compose basta, este capítulo se escribe como ADR de "por qué NO".
- **Aprendes:** Pods, Deployments, Services, probes, ConfigMaps/Secrets, rolling updates.

## Fase 12 — Retirada del legacy
- **Problema real:** las migraciones que nunca terminan duplican el coste para siempre.
- **Aprendes:** migración de datos final, verificación de paridad, decomisión.
- **Salida:** legacy apagado y retrospectiva escrita.

---

## Decisiones pendientes (Fase 0)
| Decisión | Opciones | Recomendación |
|---|---|---|
| Tecnología del legacy | (A) .NET Framework 4.8 WebForms/MVC5 · (B) ASP.NET Core escrito con antipatrones · (C) App de consola/WinForms + SQL | **A (MVC5 o WebForms)** si tienes Visual Studio en Windows: es el legacy más realista del mercado laboral. **B** si prefieres menos fricción de herramientas. |
| Base de datos | SQL Server · PostgreSQL | **SQL Server** (Developer/Express): es lo típico en hospitales con stack Microsoft y tiene `rowversion` y stored procedures maduros. |
