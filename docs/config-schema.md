# Sistema de Configuración por Cliente

Objetivo (master prompt, sección 18): para crear una nueva versión de un agente para otro cliente, NO se debería reconstruir nada — solo cambiar esta configuración.

Regla: todo lo que identifique a un cliente específico (IDs, credenciales, textos de marca, horarios, criterios) vive acá. El código del agente (CORE) nunca debería tener un valor hardcodeado que cambie de cliente a cliente.

---

## Campos comunes a todo agente

Estos aplican sin importar el tipo de agente (RRHH, ventas, soporte, etc.):

| Campo | Qué es | Obligatorio |
|---|---|---|
| `CLIENT_NAME` | Nombre de la empresa cliente | Sí |
| `AGENT_NAME` | Nombre del agente (para logs, para que se identifique si el usuario pregunta) | Sí |
| `CONTACT_EMAIL` | Email de contacto del cliente (para notificaciones internas) | Sí |
| `MODEL` | Qué modelo de IA usa (no incluido en este repo) | Sí |
| `SYSTEM_PROMPT` | El prompt de sistema — cambia completamente por cliente/rubro/tono de marca | Sí |
| `APPROVAL_REQUIRED` | Lista de acciones que requieren aprobación humana para este cliente (puede variar: un cliente puede querer aprobación y otro no) | Sí |
| `NOTIFICATION_CHANNEL` | Dónde se avisa al equipo del cliente (email, Slack, WhatsApp) | Sí |
| `TIMEZONE` | Zona horaria del cliente | Sí |
| `LANGUAGE` | Idioma en el que responde el agente | Sí |
| `COST_LIMIT_MONTHLY` | Presupuesto mensual aproximado, para poder alertar si se dispara | Recomendado |

## Campos específicos por componente usado

Cada componente de `components/` que el agente use agrega sus propios campos de configuración. Ejemplo con los componentes ya documentados:

**Si usa `components/action/google-calendar-actions.md`:**
- `CALENDAR_ID`
- `BUSINESS_HOURS_START`, `BUSINESS_HOURS_END`
- `APPOINTMENT_DURATION_MIN`

**Si usa `components/memory/google-sheets-store.md`:**
- `LEADS_SPREADSHEET_ID`, `LEADS_TAB` (o el nombre que corresponda al dominio: `CANDIDATES_SPREADSHEET_ID`, etc.)
- `CATALOG_CSV_URL` (si aplica un catálogo de solo lectura)

**Si usa `components/input/http-webhook-express.md`:**
- `PORT`

## Dónde van las credenciales (NO en este archivo)

Las credenciales (API keys, service account JSON) nunca van en el archivo de configuración del cliente ni en el código. Van en variables de entorno (`.env`, fuera de control de versiones) o en un gestor de secretos. Este archivo de configuración puede referenciar el *nombre* de la variable de entorno, pero no el valor.

## Formato sugerido

Un `config.json` (o `.env` para las credenciales) por cliente, en una carpeta de configuración privada por cliente (no incluida en este repo). Ver ejemplo genérico en `config/config.example.json`.
