# CRM Agent — Atención al cliente inmobiliaria

Agente de IA (Claude, tool-use) que atiende consultas de una inmobiliaria
24/7: busca propiedades en el catálogo, consulta disponibilidad, agenda
visitas en Google Calendar y guarda leads calificados en Google Sheets —
incluso fuera de horario laboral, que es cuando la mayoría de las
inmobiliarias pierde esas consultas.

**Estado:** validado en producción con el primer cliente real (auditado
80/100 — ver `docs/audit.md`).

Este repo documenta la **arquitectura reutilizable** del agente — el
código de producción del cliente real vive fuera de este repo por
confidencialidad; lo que se muestra acá es el diseño CORE + configuración
que permite clonar el mismo agente a un cliente nuevo sin reescribir nada,
más la especificación técnica, la auditoría de seguridad/calidad y el
proceso de puesta en marcha ya corridos sobre la instancia real.

## Por qué "CORE + configuración" en vez de un repo por cliente

Todo lo que **no cambia** entre clientes (el loop de tool-use con Claude,
la integración con Calendar/Sheets, la memoria de conversación, los
reintentos con backoff) vive una sola vez en `components/`. Lo que **sí**
cambia por cliente — nombre de la inmobiliaria, IDs de Calendar/Sheet,
catálogo, horario, tono — es configuración (`config/`), nunca código
nuevo. Clonar el agente a una inmobiliaria nueva es completar un archivo
de configuración, no reescribir el sistema — ver `docs/client-setup-guide.md`
para el proceso completo.

## Estructura

```
docs/
  agent-card.md                  Ficha ejecutiva de una página (problema, resultado, precio)
  spec.md                        Especificación técnica completa
  agent-specification-template.md Plantilla para especificar un agente nuevo
  config-schema.md               Qué campos cambian por cliente y por qué
  changelog.md                   Historial de versiones (MAJOR.MINOR.PATCH)
  client-setup-guide.md          Cómo clonar este agente a un cliente nuevo
  audit.md                       Auditoría de seguridad/calidad ya corrida (80/100)
  pricing-reference.md           Referencia de precio de mercado, con fuentes

components/                      Piezas CORE reutilizables, documentadas una por una
  ai/claude-tool-use-loop.md
  action/google-calendar-actions.md
  memory/google-sheets-store.md
  memory/sqlite-session-store.md
  memory/in-memory-session-store.md
  control/retry-backoff.md
  control/step-limit-loop-guard.md
  input/http-webhook-express.md

config/
  config.example.json            Configuración por cliente (valores de ejemplo genéricos)
  prompts/system.txt             System prompt, parametrizado por cliente
```

## Stack

Claude API (tool-use, modelo Haiku 4.5) · Google Calendar API · Google
Sheets API · Node.js/Express · SQLite (memoria de conversación
persistente).

## Nota sobre datos del cliente real

Los IDs de Calendar/Sheet/catálogo en `config/config.example.json` son
**valores de ejemplo genéricos** — los reales, del cliente en producción,
nunca se versionan (ver la nota dentro del propio archivo y
`docs/config-schema.md`).
