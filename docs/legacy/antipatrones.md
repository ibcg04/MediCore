# Antipatrones deliberados del legacy

Se introducen **a propósito** para que la migración tenga problemas reales que resolver. Cada uno se cierra en una fase concreta.

| # | Antipatrón | Consecuencia | Se aborda en |
|---|---|---|---|
| AP-01 | SQL concatenado con strings | Inyección SQL | Fase 1 (lo detectas), Fase 3 |
| AP-02 | Reglas de negocio en stored procedures y en la UI | Lógica duplicada e incoherente | Fases 2–3 |
| AP-03 | "Comprobar y luego insertar" sin transacción | Doble reserva de cita/cama | Fase 5 |
| AP-04 | Unicidad validada solo en la UI | Pacientes duplicados | Fases 2 y 5 |
| AP-05 | Alta + cargo en dos operaciones separadas | Altas sin facturar | Fases 5 y 8 |
| AP-06 | Sin protección ante doble envío | Pagos y dispensaciones duplicados | Fase 6 |
| AP-07 | Contraseñas en texto plano o MD5 | Seguridad | Fase 3 |
| AP-08 | Fechas como `varchar` en alguna tabla | Errores de ordenación y de zona horaria | Fase 2 (caracterizar), Fase 4 (ACL) |
| AP-09 | Configuración y cadenas de conexión en el código | Despliegue frágil | Fases 3 y 7 |
| AP-10 | Clases "Dios" (`PacienteManager` de 2000 líneas) | Imposible de testear | Fase 2 (*seams*) |
