# AGENT CARD — CRM Agent Inmobiliaria

```
Nombre:     CRM Agent — Atención al cliente inmobiliaria
Categoría:  Atención al cliente / Ventas
Sector:     Inmobiliarias
Versión:    1.1.0 (post fixes del 13/08/2026 — ver CHANGELOG en docs/spec.md)
Estado:     VALIDADO (auditado 80/100, apto para demo)
```

## Problema

Las inmobiliarias pierden consultas que llegan fuera de horario laboral (noches, fines de semana, feriados) — justo cuando más urgencia tiene el cliente. Sin respuesta inmediata, ese lead se va a la competencia.

## Cliente ideal

Inmobiliarias chicas/medianas (catálogo de referencia probado: 20-50 propiedades) que reciben consultas por chat/WhatsApp y hoy no tienen cobertura 24/7.

## Resultado

El cliente de la inmobiliaria obtiene respuesta inmediata a cualquier hora: disponibilidad de propiedades, agendado de visita, y sus datos de contacto quedan registrados aunque no haya nadie del equipo despierto. El equipo de la inmobiliaria recibe leads calificados (con presupuesto y urgencia marcada) en un Sheet, y las visitas ya agendadas en su Google Calendar.

## Flujo

```
Cliente escribe por chat
↓
Claude interpreta el pedido (tool-use)
↓
Busca en el catálogo / consulta horarios / agenda / guarda el lead
↓
Google Calendar + Google Sheets se actualizan
↓
Respuesta al cliente
↓
Equipo de la inmobiliaria revisa leads y confirma citas
```

## Herramientas

Anthropic Claude API (Haiku 4.5) · Google Calendar API · Google Sheets API — fichas completas en `la base de conocimiento interna (no incluida en este repo)`.

## Integraciones

Google Calendar y Google Sheets del cliente (vía service account). Opcional a futuro: WhatsApp Business API (evaluada, no integrada — ver `la base de conocimiento interna (no incluida en este repo)`), Apify para prospección inicial de inventario.

## Configuración

Lo que cambia por cliente: nombre y descripción de la inmobiliaria, ID de calendario, ID de spreadsheet, URL del catálogo, horario de atención, tono de marca, criterio de urgencia. Esquema completo y ejemplo en `config/`.

## MVP

Buscar propiedades, consultar disponibilidad, agendar visita, capturar lead (con presupuesto opcional). Todo lo que hoy está construido y auditado.

## Opcionales (vendibles aparte, plan Completo/futuro)

Dashboard con métricas de leads/citas (prometido en la landing, no construido todavía). Integración con WhatsApp Business API. Toggle de herramientas por plan (Básico sin calendario/inventario) — diseño pendiente, ver gap #5 en `el README interno del sistema de agentes (no incluido en este repo)`.

## Limitaciones

No negocia precios ni condiciones contractuales. No reemplaza el cierre humano de una operación. No tiene aprobación humana antes de agendar una cita o guardar un lead (decisión consciente pendiente de confirmar — ver auditoría). No maneja pagos ni señas. La memoria de conversación es por instancia de servidor (no multi-tenant todavía).

## Requisitos

Cuenta de Google Workspace del cliente (Calendar + Sheets) con un service account autorizado, un catálogo de propiedades en Sheets, y una API key de Anthropic.

## Coste

Costo operativo: PRECIO A VALIDAR — depende de volumen de conversación × tokens de Claude Haiku (precio oficial no verificado todavía, ver `la base de conocimiento interna (no incluida en este repo)anthropic-claude-api.md`). Google Calendar/Sheets API: sin costo al volumen esperado.

## Precio

Referencia de mercado (no precio cerrado): desde ~USD 300/mes como servicio gestionado, o $1.200.000-$2.500.000 ARS por proyecto de implementación. Detalle y fuentes en `docs/pricing-reference.md`.

## Mantenimiento

Bajo — sin servidor propio que administrar más allá de mantener el proceso Node corriendo (o desplegado en un host). Revisar periódicamente: cuotas de las APIs de Google, precio/disponibilidad del modelo de Claude, y el archivo SQLite de sesiones si crece mucho a largo plazo.
