# SQLite Session Store

Tipo: MEMORY
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria) — reemplaza al `in-memory-session-store` original
Última verificación: 13/08/2026

## Qué hace

Persiste el historial de conversación de cada sesión en un archivo SQLite local (`data/conversations.sqlite`), indexado por `sessionId`. Reemplaza el `Map` en memoria: ahora la conversación sobrevive a un reinicio del servidor.

## Inputs que espera

`sessionId` para leer/escribir el historial correspondiente.

## Outputs que produce

`getHistory(sessionId)` devuelve el array de mensajes (vacío si es sesión nueva). `saveHistory(sessionId, messages)` guarda/actualiza el historial completo.

## Dependencias

`better-sqlite3` (Node.js) — librería nativa, sin servicio externo que mantener. Se agregó a `package.json`.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

CORE: el patrón completo (`db.js`) es reutilizable tal cual para cualquier agente — no depende del rubro.

Configuración por cliente: ninguna, salvo la ubicación del archivo si se quiere separar por cliente en un despliegue multi-tenant (hoy es un archivo por instancia del servidor, lo cual alcanza mientras cada cliente tenga su propia instancia).

## Cómo reutilizarlo en otro agente

Copiar `db.js` tal cual y reemplazar el `Map` de `server.js` por `getHistory`/`saveHistory`, como se hizo acá. No requiere cambios en el componente AI.

## Limitaciones conocidas

- Es un archivo en disco: si el hosting usa disco efímero (se borra en cada redeploy), el historial se pierde igual que antes. Verificar esto en el proveedor de hosting elegido.
- No pensado para múltiples instancias del servidor corriendo en paralelo sobre el mismo archivo (escritura concurrente) — para ese caso, migrar a Postgres/Supabase.
- No tiene expiración automática de sesiones viejas — si el volumen crece mucho a largo plazo, conviene un job de limpieza periódico (no implementado).
