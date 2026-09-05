# HTTP Webhook (Express)

Tipo: INPUT
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026

## Qué hace

Expone un endpoint HTTP (`POST /chat`) que recibe mensajes de un cliente externo (frontend web, WhatsApp vía un puente, etc.) y los pasa al componente AI. También expone `GET /health` para chequeo de vida del servicio.

## Inputs que espera

JSON con `sessionId` (identifica la conversación) y `message` (texto del usuario).

## Outputs que produce

JSON con `reply` (texto de respuesta del agente) o `error` si algo falla.

## Dependencias

Node.js + Express (`express` en package.json). Sin credenciales externas — es el punto de entrada propio del servidor.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

CORE: la forma del endpoint (recibe sessionId + message, devuelve reply) es reutilizable tal cual para cualquier agente conversacional.

Configuración por cliente: el `PORT`, y si el canal real es WhatsApp en vez de un frontend web, hace falta un componente INPUT distinto (webhook de WhatsApp Business API — no construido todavía, ver ficha `integrations/whatsapp-business-api.md`) que traduzca los mensajes entrantes a este mismo formato antes de llamar al agente.

## Cómo reutilizarlo en otro agente

Copiar `server.js` tal cual; lo único que cambia entre agentes es qué función se llama dentro del handler (`runAgent` hoy, sería el componente AI de cada agente específico).

## Limitaciones conocidas

CORS abierto a cualquier origen (`Access-Control-Allow-Origin: *`) — aceptable para desarrollo/demo, pero para producción con datos de clientes reales conviene restringirlo al dominio del frontend real.
