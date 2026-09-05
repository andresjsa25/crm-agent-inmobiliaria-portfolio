# Cómo clonar este agente a un cliente nuevo

Proceso de reutilización (Master Prompt, sección 23): no se reconstruye desde cero. Se carga la versión base, se identifica qué cambia, y se pregunta solamente lo necesario.

## 1. Cargar la versión base

Partir del código base de este agente (versión 1.1.2, fuera de este repo de portafolio — ver `docs/changelog.md`). El CORE (loop de tool-use, componentes de `components/`) se mantiene intacto.

## 2. Preguntar solamente lo necesario

Usar las preguntas de descubrimiento ya armadas en `la guía de presentación comercial (no incluida en este repo)` (sección 3, grupos A/B/C) — ya están pensadas específicamente para este agente. No repetir las 20 preguntas genéricas del master prompt si estas alcanzan.

## 3. Crear la configuración nueva

Completar el esquema de `docs/config-schema.md` para el cliente nuevo, usando `config/config.example.json` como modelo. Como mínimo hace falta:

- Nombre de la inmobiliaria y tono de marca
- ID del Google Calendar del cliente (nuevo service account o el mismo con acceso al calendar correcto)
- ID del Google Sheet de leads (con las columnas: fecha, nombre, teléfono, qué busca, presupuesto, urgente — mismo orden que la v1.1.0)
- URL del catálogo (Sheet publicado como CSV)
- Horario de atención para visitas (hoy hardcodeado a 10-18hs — sacar a config si el cliente nuevo tiene otro horario)
- Criterio de urgencia del cliente

## 4. Detectar integraciones diferentes

¿Este cliente usa Google Workspace, o Outlook/Microsoft 365? Si es Microsoft, hace falta un componente ACTION nuevo (no existe todavía — sería el primer caso de "Microsoft Calendar Actions" en `components/action/`). ¿Tiene WhatsApp Business API ya activo? Si sí, es la oportunidad de construir el componente INPUT de WhatsApp que hoy está pendiente.

## 5. Identificar cambios en el prompt

Usar la plantilla de `config/prompts/system.txt` y completar las variables con los datos del cliente nuevo. Revisar especialmente `{{RESTRICCIONES_ESPECIFICAS}}` — cada inmobiliaria puede tener reglas distintas sobre qué no debe prometer el agente.

## 6. Testear antes de entregar

Correr `run-tests.mjs` (o una copia adaptada) contra la instancia del cliente nuevo, con foco en TEST 1 (caso normal, con el catálogo real del cliente) y TEST 10 (los 3 jailbreaks — la protección de prompt ya está en el CORE, pero conviene reverificar con el nombre/contexto del cliente nuevo).

## 7. Auditar

Correr el framework de `el framework de auditoría interno (no incluido en este repo; ver docs/audit.md para un resultado ya corrido)` sobre la instancia configurada. Si el score baja de 75, resolver antes de entregar.

## 8. Crear la nueva versión y documentar

- Nueva ficha en `la base de conocimiento interna (no incluida en este repo)` si el cliente nuevo tiene diferencias notables de comportamiento.
- Nueva carpeta en `una carpeta de configuración privada por cliente (no incluida en este repo)` con su configuración específica.
- Registrar en el CHANGELOG de este paquete si el cliente nuevo requirió un cambio al CORE (no solo a la configuración) — eso implica que otros clientes también se benefician del cambio.

## Qué NO hacer

No dupliques el código fuente por cliente si el único cambio es configuración — el objetivo es un solo CORE, N configuraciones. Solo forkear el código si el cliente pide algo que rompe el patrón CORE/configuración (por ejemplo, una lógica de negocio completamente distinta) — y en ese caso, evaluar si en realidad es un agente distinto, no una variante de este.
