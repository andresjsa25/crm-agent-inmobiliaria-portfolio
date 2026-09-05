# Google Calendar Actions (consultar + agendar)

Tipo: ACTION
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026

## Qué hace

Dos acciones sobre un calendario compartido vía Service Account:

1. `consultar_disponibilidad`: usa `freebusy.query` para calcular huecos libres en una franja horaria fija (10-18hs) para una fecha dada.
2. `agendar_cita`: usa `events.insert` para crear el evento, con la info del cliente en la descripción.

## Inputs que espera

Consulta: fecha. Agendar: fecha, hora, nombre del cliente, teléfono, propiedad/motivo.

## Outputs que produce

Consulta: lista de horarios libres en texto. Agendar: confirmación en texto.

## Dependencias

`googleapis` (Node.js), Service Account con scope `calendar` — ver ficha `la base de conocimiento interna (no incluida en este repo)`.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

CORE: el patrón freebusy + insert es reutilizable para cualquier agente que agende turnos/citas (clínicas, servicios, inmobiliarias, etc.).

Configuración por cliente: `CALENDAR_ID`, la franja horaria fija (10-18hs está hardcodeada — debería ser configurable por cliente), duración del turno (hoy fija en 1 hora).

## Cómo reutilizarlo en otro agente

Directamente aplicable a cualquier "Agente de Reservas" (ver roadmap del master prompt). Generalizar la franja horaria y duración como parámetros de configuración antes de reutilizar.

## Limitaciones conocidas

- Sin verificación de que la fecha no sea en el pasado.
- Sin manejo de zona horaria configurable (hardcodeado a `-03:00`, Argentina) — un cliente en otro país necesitaría este cambio.
- Sin confirmación humana antes de crear el evento — ver componente CONTROL faltante (`human-approval-gate`, no construido todavía).
- ~~Sin validación de horario ni re-chequeo de disponibilidad antes de insertar~~ — corregido el 13/08/2026: `agendar_cita` ahora valida el rango horario y vuelve a consultar `freebusy` justo antes de crear el evento (ver `el plan de pruebas interno (no incluido en este repo)`, TEST 3).
