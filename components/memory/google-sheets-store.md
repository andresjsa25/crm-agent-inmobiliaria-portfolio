# Google Sheets Store (lectura + escritura)

Tipo: MEMORY
Reutilizable: Sí
Primera vez usado en: crm-agent (inmobiliaria)
Última verificación: 12/08/2026

## Qué hace

Dos patrones distintos sobre Google Sheets, ambos presentes en `tools.js`:

1. **Lectura de catálogo** (`buscar_propiedades`): lee un Google Sheet publicado como CSV público (`PROPERTIES_CSV_URL`) vía `fetch` simple — no usa la API de Sheets para esto, así que no consume cuota de la API ni requiere autenticación.
2. **Escritura de registros** (`guardar_lead`): usa la API de Sheets (`sheets.spreadsheets.values.append`) con Service Account para agregar filas nuevas.

## Inputs que espera

Lectura: filtros opcionales (zona, tipo, ambientes mínimos). Escritura: nombre, teléfono, qué busca, presupuesto (opcional, agregado 13/08/2026), urgencia.

**Acción manual pendiente**: el rango de escritura pasó de `A:E` a `A:F` para sumar la columna de presupuesto entre "qué busca" y "urgente". Hay que agregar el encabezado "presupuesto" en la columna E del Google Sheet real de leads (y correr "urgente" a la F) antes de que el agente escriba ahí — si no, los datos van a quedar debajo del encabezado equivocado.

## Outputs que produce

Lectura: hasta 5 propiedades que matchean, en JSON. Escritura: confirmación de guardado.

## Dependencias

`googleapis` (Node.js) — ver ficha `la base de conocimiento interna (no incluida en este repo)`. La escritura requiere Service Account con scope `spreadsheets`; la lectura vía CSV público no requiere credenciales.

## Qué cambia por cliente (configuración) vs. qué es fijo (CORE)

CORE: el patrón "leer catálogo por CSV público" + "escribir leads por API con service account" es reutilizable para cualquier agente que necesite un catálogo simple de solo lectura y un registro de eventos de solo escritura.

Configuración por cliente: `PROPERTIES_CSV_URL`, `LEADS_SPREADSHEET_ID`, `LEADS_TAB` — todo esto hoy vive en `google-auth.mjs`, candidato directo a mover a un archivo de configuración por cliente (Fase 5/6 del roadmap).

## Cómo reutilizarlo en otro agente

Para un agente de otro rubro (ej. RRHH guardando candidatos en vez de leads), el patrón de escritura es idéntico — solo cambian las columnas. El patrón de lectura por CSV público sirve para cualquier catálogo simple (productos, FAQs, horarios) que el cliente ya mantenga en un Sheet.

## Limitaciones conocidas

- El catálogo vía CSV público no es apto si el cliente no quiere que ese Sheet sea público (aunque el link no esté indexado, cualquiera con el link puede leerlo).
- Sin control de duplicados: `guardar_lead` no chequea si ese teléfono ya existe antes de agregar una fila nueva — un mismo lead que escribe varias veces genera filas repetidas.
