# Agent Specification — CRM Agent Inmobiliaria

Primer ejemplo real aplicando la plantilla, sobre el primer agente de este tipo ya construido y en producción (fuera de este repo de portafolio). Sirve para validar que la plantilla funciona y para dejar en claro qué falta definir antes de poder venderlo como producto.

---

## 1. Identificación

- Nombre: CRM Agent — Atención al cliente inmobiliaria
- Versión: 1.0.0 (código existente, sin versionar formalmente todavía)
- Categoría: Atención al cliente / Ventas (inmobiliaria)
- Sector / cliente ideal: Inmobiliarias con inventario de propiedades en alquiler/venta y agenda de visitas
- Estado: VALIDADO → auditado el 13/08/2026, score 80/100 (ver `docs/audit.md`). Apto para demo, con 1 hallazgo HIGH pendiente (aprobación humana) antes de operar con clientes de forma continua.
- Fecha de creación: PENDIENTE DE DEFINIR (no hay fecha de commit revisada)
- Última actualización: PENDIENTE DE DEFINIR

## 2. Objetivo y problema

- Objetivo del agente: Atender consultas de clientes potenciales 24/7, informar disponibilidad de propiedades, agendar visitas y capturar leads sin intervención humana constante.
- Problema empresarial que resuelve: Una inmobiliaria no puede atender consultas fuera de horario laboral; leads con urgencia se pierden si nadie responde a tiempo.
- Usuario que lo va a usar: Cliente potencial (quien escribe al chat).
- Quién recibe los resultados: El equipo de la inmobiliaria (via los leads guardados en Sheets y las citas en Calendar).

## 3. Alcance

- Qué hace: Busca propiedades por criterios, consulta disponibilidad de horarios, agenda visitas, guarda datos de contacto de leads.
- Qué NO hace: No negocia precios, no confirma condiciones contractuales, no reemplaza el contacto humano para el cierre de una operación. No maneja pagos ni señas.

## 4. Disparador

Mensaje entrante al endpoint `POST /chat` (hoy pensado para un frontend web con `sessionId`; no hay disparador desde WhatsApp todavía).

## 5. Inputs

Texto libre del cliente (mensaje) + `sessionId` para mantener contexto de la conversación.

## 6. Knowledge

Inventario de propiedades (Google Sheet publicado como CSV). No usa RAG ni documentos adicionales (políticas de la inmobiliaria, FAQs) — PENDIENTE DE DEFINIR si hace falta sumar eso.

## 7. Model

Claude Haiku 4.5 (`claude-haiku-4-5`) — modelo rápido/económico, adecuado para conversación con tool-use de complejidad media. No incluida en este repo.

## 8. Memory

Historial de conversación persistido en SQLite local (`data/conversations.sqlite`, ver componente `sqlite-session-store`) — actualizado el 13/08/2026, ya sobrevive a un reinicio del servidor. Leads y catálogo persisten en Google Sheets (memoria de negocio, no de conversación).

## 9. Tools / Integraciones

- Google Calendar API (consultar disponibilidad, agendar cita)
- Google Sheets API (guardar lead, leer catálogo vía CSV)
- (Prospección, separada del agente conversacional): Apify para scraping de inventario/leads iniciales

## 10. Workflow

```
Cliente escribe por chat
↓
POST /chat (Express)
↓
Claude (tool-use loop, hasta 8 pasos)
↓
Decide: ¿buscar propiedades? ¿consultar horarios? ¿agendar? ¿guardar lead?
↓
Ejecuta la(s) herramienta(s) correspondiente(s)
↓
Google Calendar / Google Sheets (según la acción)
↓
Respuesta de texto al cliente
↓
(No hay paso de "humano si es necesario" implementado todavía)
```

## 11. Decision rules

- Qué decisiones puede tomar automáticamente: Qué propiedades mostrar, qué horarios ofrecer, cuándo guardar un lead, cuándo agendar una cita.
- Qué decisiones requieren aprobación humana: Ninguna, hoy — el agente actúa de punta a punta sin checkpoint humano.
- Qué información NO puede modificar: No modifica el inventario ni el calendario fuera de crear eventos nuevos (no cancela ni reagenda).
- Qué hace cuando no sabe qué hacer: El system prompt le indica no inventar y decir "un agente se lo confirma", intentando igual capturar el contacto.

## 12. Actions

Crear evento en Google Calendar; agregar fila en Google Sheets (leads).

## 13. Output

Respuesta de texto conversacional al cliente. Efectos secundarios: fila nueva en Sheet de leads, evento nuevo en Calendar.

## 14. Human approval

No implementado. **Gap identificado**: agendar una cita real y capturar datos de un lead son acciones con impacto directo en una persona — el master prompt sugiere evaluar aprobación humana para esto. A definir: ¿se requiere confirmación humana antes de agendar, o es aceptable que el agente lo haga solo dado que es reversible (se puede cancelar la cita manualmente)?

## 15. Error handling

- Qué pasa si falla una API externa (Calendar/Sheets/catálogo): Desde el 13/08/2026, cada llamada reintenta automáticamente con backoff exponencial (hasta 3 intentos) si el error es transitorio (rate limit, timeout, 5xx) — ver componente `retry-backoff`. Si se agotan los reintentos, el error se captura por herramienta y se le devuelve al modelo como texto, que puede intentar explicarle al cliente.
- Qué pasa si falta información: El modelo pide la información faltante en su respuesta de texto (definido por el system prompt, no por código).
- Qué pasa si llega información incorrecta o ambigua: PENDIENTE DE DEFINIR — no hay validación explícita de formato (ej. teléfono mal escrito, fecha inválida).

## 16. Security

- ¿Maneja datos sensibles? Sí: nombre, teléfono de clientes potenciales.
- ¿Qué acceso/permisos tiene? Service account con permisos de escritura sobre un Calendar y un Sheet específicos (no acceso amplio a Google Workspace).
- ¿Cómo se protegen las credenciales? `.env` con `ANTHROPIC_API_KEY`; credencial de Google en `credentials/google-calendar-service-account.json` — confirmar que ese archivo no esté en el repositorio si en algún momento se sube a un control de versiones compartido.

## 17. Cost limit

Costo operativo (correr el agente): PENDIENTE DE DEFINIR / PRECIO A VALIDAR. No hay límite de gasto configurado ni estimación de costo mensual documentada (depende de volumen de conversaciones × tokens de Claude Haiku) — no incluida en este repo.

Precio a cobrar al cliente: ver referencia de mercado en `docs/pricing-reference.md` — rango orientativo desde ~USD 300/mes (servicio gestionado) o $1.200.000–$2.500.000 ARS por proyecto, con fuentes verificadas. Precio final todavía PENDIENTE DE DEFINIR por Andresito.

## 18. Success metrics

PENDIENTE DE DEFINIR — no hay métricas instrumentadas hoy (ej. leads capturados/mes, citas agendadas/mes, tasa de resolución sin intervención humana).

## 19. Integraciones (resumen)

Anthropic Claude API · Google Calendar API · Google Sheets API · Apify (prospección, no en el flujo conversacional)
