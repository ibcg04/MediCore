# Requisitos funcionales — Sistema legacy "HIS-Clásico"

Sistema de información hospitalaria ficticio (año ~2009) del *Hospital General San Ficticio*. Todos los datos son inventados.

## Actores
| Actor | Descripción |
|---|---|
| Recepcionista | Registra pacientes y agenda citas |
| Médico | Consulta agenda, registra notas clínicas y recetas |
| Admisión | Gestiona ingresos, camas y altas |
| Farmacéutico | Dispensa medicamentos y controla stock |
| Facturación | Emite facturas y registra pagos |
| Administrador | Gestiona usuarios y catálogos |

## Módulo PAC — Pacientes
- **RF-PAC-01** Registrar paciente: documento, nombres, fecha de nacimiento, sexo, teléfono, dirección, aseguradora (opcional).
- **RF-PAC-02** Buscar paciente por documento, nombre parcial o número de historia clínica.
- **RF-PAC-03** Editar datos del paciente.
- **RF-PAC-04** Generar número de historia clínica (HC) secuencial con formato `HC-AAAA-NNNNNN`.
- **RN-PAC-01** El documento debe ser único. *(El legacy solo lo valida en la UI, no en la BD.)*
- **RN-PAC-02** No se eliminan pacientes; se marcan como inactivos.

## Módulo CIT — Agenda y citas
- **RF-CIT-01** Agendar cita: paciente, médico, especialidad, fecha y hora (bloques de 20 min).
- **RF-CIT-02** Cancelar y reprogramar cita (con motivo).
- **RF-CIT-03** Consultar agenda diaria de un médico.
- **RF-CIT-04** Marcar cita como Atendida / No asistió.
- **RN-CIT-01** Un médico no puede tener dos citas en el mismo bloque. ⚠️ *Punto de concurrencia (Fase 5).*
- **RN-CIT-02** Un paciente no puede tener dos citas activas con la misma especialidad el mismo día.
- **RN-CIT-03** No se agenda en el pasado ni fuera del horario del médico.

## Módulo ADM — Admisión y camas
- **RF-ADM-01** Ingresar paciente: asigna cama, servicio (Medicina Interna, Cirugía, Pediatría…) y médico responsable.
- **RF-ADM-02** Trasladar paciente de cama.
- **RF-ADM-03** Dar de alta: libera la cama y genera cargos a facturación.
- **RF-ADM-04** Consultar mapa de ocupación de camas.
- **RN-ADM-01** Una cama solo puede tener un paciente ingresado. ⚠️ *Punto de concurrencia.*
- **RN-ADM-02** Un paciente solo puede tener un ingreso activo.
- **RN-ADM-03** El alta y el cargo a facturación ocurren "juntos". ⚠️ *Transacción entre módulos (Fases 5 y 8).*

## Módulo HCL — Historia clínica básica
- **RF-HCL-01** Registrar nota de evolución (texto libre, médico, fecha).
- **RF-HCL-02** Registrar diagnóstico (catálogo tipo CIE-10 reducido y ficticio).
- **RF-HCL-03** Emitir receta: medicamento, dosis, frecuencia, duración.
- **RN-HCL-01** Las notas no se editan; se corrigen con una nueva nota (auditoría).

## Módulo FAR — Farmacia
- **RF-FAR-01** Mantener catálogo de medicamentos y stock por lote.
- **RF-FAR-02** Dispensar receta: descuenta stock y genera cargo.
- **RF-FAR-03** Registrar entrada de lote.
- **RN-FAR-01** El stock nunca puede ser negativo. ⚠️ *Punto de concurrencia.*
- **RN-FAR-02** Una receta se dispensa una sola vez. ⚠️ *Punto de idempotencia (Fase 6).*
- **RN-FAR-03** Se dispensa primero el lote que vence antes (FEFO).

## Módulo FAC — Facturación
- **RF-FAC-01** Acumular cargos (consulta, estancia, medicamentos) por paciente/episodio.
- **RF-FAC-02** Emitir factura a partir de los cargos pendientes.
- **RF-FAC-03** Registrar pago (efectivo, tarjeta, aseguradora).
- **RF-FAC-04** Anular factura (con nota de crédito).
- **RN-FAC-01** Un cargo pertenece a una sola factura.
- **RN-FAC-02** Un pago no se registra dos veces. ⚠️ *Punto de idempotencia.*
- **RN-FAC-03** La numeración de facturas es correlativa y sin huecos. ⚠️ *Interesante con concurrencia.*

## Módulo SEG — Usuarios y seguridad
- **RF-SEG-01** Login con usuario y contraseña.
- **RF-SEG-02** Roles por actor; cada módulo verifica el rol.
- **RF-SEG-03** Bitácora de acciones sensibles.

## Requisitos no funcionales (tal como están en el legacy)
- Una única base de datos SQL Server compartida por todos los módulos.
- Gran parte de las reglas de negocio vive en stored procedures y en la UI.
- Sin API: la UI accede directamente a la BD.
- Sin tests automatizados.
- Despliegue manual en un único servidor.

## Alcance por fases
- **Fase 1a:** PAC, CIT, ADM, SEG (mínimo).
- **Fase 1b:** HCL, FAR, FAC.
