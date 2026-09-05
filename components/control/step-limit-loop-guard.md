# Step-Limit Loop Guard

Tipo: CONTROL
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026

## Qué hace

Limita el loop de tool-use a un máximo de pasos (8, en el código actual) para evitar que el agente quede en un bucle infinito pidiendo herramientas sin nunca llegar a una respuesta de texto para el usuario.

## Inputs que espera

Ninguno externo — es un contador interno (`for (let step = 0; step < 8; step++)`).

## Outputs que produce

Si se agota el límite sin que el modelo devuelva texto, un mensaje de fallback genérico ("tuve un problema procesando tu consulta").

## Dependencias

Ninguna — lógica pura dentro del componente AI (`claude-tool-use-loop.md`).

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

El patrón es CORE. El número de pasos (8) debería ser configurable según cuántas herramientas encadenadas necesite típicamente el agente.

## Cómo reutilizarlo en otro agente

Ya viene incluido dentro del componente `claude-tool-use-loop` — no es un componente separado para instalar, sino un parámetro de ese componente.

## Limitaciones conocidas

Es el único mecanismo de control que existe hoy en el crm-agent. Falta, y no está construido todavía:

- **Human approval gate**: un paso de aprobación humana antes de ejecutar acciones sensibles (agendar una cita real, por ejemplo) — el master prompt lo pide explícitamente para decisiones que afectan a una persona. Pendiente de diseñar en la Fase 5 (Agent Specification).
- **Retry / backoff**: no hay reintentos automáticos ante fallos transitorios de las APIs externas (Calendar, Sheets, Anthropic).
- **Router / condiciones**: no hay lógica de enrutamiento explícita más allá de lo que el propio modelo decide con tool-use.
