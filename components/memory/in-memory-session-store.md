# In-Memory Session Store

Tipo: MEMORY
Reutilizable: Sí, pero con reserva (ver limitaciones)
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026
Estado: REEMPLAZADO el 13/08/2026 por `sqlite-session-store.md` en el crm-agent real. Se deja esta ficha como referencia histórica y porque el patrón sigue siendo válido para un prototipo rápido o una demo descartable.

## Qué hace

Guarda el historial de conversación de cada sesión en un `Map` en memoria del proceso Node.js (`server.js`), indexado por `sessionId`. Permite que el agente recuerde los mensajes previos dentro de la misma conversación.

## Inputs que espera

`sessionId` (string) para identificar a qué conversación pertenece cada mensaje.

## Outputs que produce

El array de mensajes acumulado para esa sesión, que se le pasa al componente AI en cada turno.

## Dependencias

Ninguna externa — es una estructura de datos nativa de JavaScript.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

Es CORE en su forma más simple (un Map), pero es el componente más frágil del stack actual: no persiste nada.

## Cómo reutilizarlo en otro agente

Sirve tal cual para un MVP o demo. Para producción real, reemplazar por almacenamiento persistente (Redis para sesiones de corta duración, o directamente Google Sheets/una base de datos si se quiere historial de largo plazo por cliente).

## Limitaciones conocidas

- Se pierde todo si el servidor se reinicia (deploy, crash, restart).
- No escala a múltiples instancias del servidor (cada instancia tendría su propio Map, un usuario podría "perder" su sesión si el load balancer lo manda a otra instancia).
- No hay límite de tamaño ni expiración — en un uso de alto volumen y larga duración, el Map crecería indefinidamente (memory leak).
