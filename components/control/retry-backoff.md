# Retry con Backoff Exponencial

Tipo: CONTROL
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 13/08/2026

## Qué hace

Envuelve una llamada a una API externa y la reintenta automáticamente si falla por un error transitorio (rate limit, timeout, error 5xx), esperando cada vez más tiempo entre intentos (backoff exponencial + jitter aleatorio). Si el error no es transitorio (permisos, datos inválidos), no reintenta — falla al toque, para no perder tiempo en algo que nunca va a funcionar.

## Inputs que espera

Una función que devuelve una Promise (la llamada a la API), y opcionalmente cuántos reintentos (default 3) y el delay base (default 500ms).

## Outputs que produce

El resultado de la función si tuvo éxito (en el primer intento o en un reintento), o el error original si se agotaron los reintentos o el error no era reintentable.

## Dependencias

Ninguna externa — función propia (`retry.js`), sin librerías.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

Es CORE puro — no depende del cliente ni del rubro. Los parámetros (cantidad de reintentos, delay base) podrían exponerse como configuración si un agente de alto volumen necesita ajustarlos, pero los valores por defecto sirven para la gran mayoría de los casos.

## Cómo reutilizarlo en otro agente

Copiar `retry.js` tal cual. Envolver cualquier llamada a una API externa (Calendar, Sheets, Apify, WhatsApp, lo que sea) con `withRetry(() => miLlamadaAsync())`.

Aplicado en crm-agent a las 4 llamadas externas de `tools.js`: `consultar_disponibilidad`, `agendar_cita`, `guardar_lead` (las 3 a Google APIs) y el fetch del catálogo CSV en `buscar_propiedades`.

## Limitaciones conocidas

- No distingue entre "vale la pena reintentar ahora" y "hay una caída larga del servicio" — con una caída de varios minutos, cada request individual va a agotar sus 3 reintentos igual, sumando latencia. Para volumen alto, un circuit breaker sería el siguiente paso (no necesario a esta escala).
- No hay límite de tiempo total (timeout) en la función `fn` que se le pasa — si la llamada en sí se cuelga sin arrojar error, el retry no ayuda. Depende de que la librería subyacente (googleapis, fetch) tenga sus propios timeouts.
