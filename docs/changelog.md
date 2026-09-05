# CHANGELOG — CRM Agent Inmobiliaria

Versionado MAJOR.MINOR.PATCH (Master Prompt, sección 24). MAJOR = cambio de arquitectura. MINOR = nueva funcionalidad. PATCH = corrección.

## 1.1.2 — 13/08/2026 (PATCH)
- Fix: el system prompt no protegía contra pedidos de revelar instrucciones internas o el detalle técnico de las herramientas. Confirmado con `run-tests.mjs` (TEST 10b falló), corregido, y re-verificado (los 3 casos de TEST 10 pasan). Ver auditoría.

## 1.1.1 — 13/08/2026 (PATCH)
- Fix: `agendar_cita` no validaba que la hora estuviera dentro del horario de atención (10-18hs) ni volvía a chequear disponibilidad real antes de crear el evento — riesgo de doble reserva o cita fuera de horario. Ahora valida y re-chequea `freebusy` antes de insertar. Verificado con `run-tests.mjs` (TEST 3).

## 1.1.0 — 13/08/2026 (MINOR)
- Nuevo: memoria de conversación persistida en SQLite (`db.js`), reemplaza el `Map` en memoria — sobrevive a un reinicio del servidor. Verificado en la práctica.
- Nuevo: reintentos con backoff exponencial (`retry.js`) en las 4 llamadas a APIs externas (Calendar x2, Sheets, fetch de catálogo).
- Nuevo: campo `presupuesto` opcional en `guardar_lead`, capturado sin ser insistente si el cliente no lo menciona espontáneamente. Requiere actualizar el encabezado de la columna E en el Sheet real de leads (acción manual, ya comunicada).

## 1.0.0 — fecha de creación original no confirmada (PENDIENTE DE DEFINIR)
- Versión inicial: Claude API (tool-use) + Google Calendar (consultar/agendar) + Google Sheets (leads/catálogo) + Express + memoria en `Map`. Construido antes de que existiera este ecosistema de documentación — por eso no hay fecha exacta ni changelog previo a esta entrada.
