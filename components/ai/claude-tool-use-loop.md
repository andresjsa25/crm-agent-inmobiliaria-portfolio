# Claude Tool-Use Loop

Tipo: AI
Reutilizable: Sí — es probablemente el componente más reutilizable de todo el ecosistema
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026

## Qué hace

Orquesta la conversación con Claude: manda el historial de mensajes + un system prompt + la lista de herramientas disponibles, y si el modelo pide usar una herramienta, la ejecuta, le devuelve el resultado, y repite — hasta que el modelo responde con texto en vez de pedir otra herramienta, o hasta un límite de pasos (8, en el caso del crm-agent) para evitar bucles infinitos.

## Inputs que espera

- `history`: array de mensajes previos de la conversación.
- (Fijo en el código, no parametrizado hoy): `SYSTEM_PROMPT`, `toolDefinitions`, `toolImplementations`, `model` (`claude-haiku-4-5`).

## Outputs que produce

`{ reply, messages }` — el texto de respuesta al usuario, y el historial actualizado para la próxima llamada.

## Dependencias

`@anthropic-ai/sdk` — ver ficha `llm/anthropic-claude-api.md`. Requiere `ANTHROPIC_API_KEY`.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

CORE (reutilizable sin tocar): el loop en sí — llamar al modelo, detectar `stop_reason === 'tool_use'`, ejecutar la herramienta pedida, devolver el resultado, repetir.

Configuración por cliente / por agente:
- `SYSTEM_PROMPT` — cambia completamente según el rubro (inmobiliaria, RRHH, soporte, etc.) y el tono de marca.
- `toolDefinitions` + `toolImplementations` — las herramientas son específicas de cada agente (ver componentes MEMORY/ACTION).
- `model` — hoy hardcodeado a `claude-haiku-4-5`; convendría exponerlo como variable de configuración (modelos más grandes para casos que necesiten más razonamiento).
- Límite de pasos (8) — fijo en el código, podría variar según la complejidad esperada del agente.

## Cómo reutilizarlo en otro agente

Copiar `agent.js`, reemplazar `SYSTEM_PROMPT` y el import de `tools.js` por las herramientas del nuevo agente. La estructura del loop no necesita cambios.

## Limitaciones conocidas

- No hay reintentos si la llamada a la API de Anthropic falla (rate limit, timeout) — el error se propagaría sin manejo hacia arriba, hasta el catch genérico del endpoint HTTP.
- El límite de 8 pasos es arbitrario — no está documentado por qué ese número; si un flujo real necesita más pasos, fallaría con un mensaje genérico de error.
