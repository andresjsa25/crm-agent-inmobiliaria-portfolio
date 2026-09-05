# Referencia de precio — CRM Agent Inmobiliaria

Última verificación: 13/08/2026
Objetivo: dar un rango de referencia de mercado, no un precio cerrado. La decisión final es tuya — esto es para no cotizar a ciegas.

## Contexto del caso

Inmobiliaria chica/mediana, catálogo de 20-50 propiedades. No encontré ninguna fuente que ate el precio directamente al tamaño del catálogo — lo que sí varía el precio en las fuentes consultadas es el volumen de conversaciones, la cantidad de canales, y si el servicio es "hazlo vos mismo" o "gestionado". Un catálogo de 20-50 propiedades ubica al cliente en el extremo chico/mediano del mercado, no en una inmobiliaria grande con cientos de unidades.

## Las 3 categorías del mercado (agentes de WhatsApp/chat para inmobiliarias)

| Categoría | Rango mensual | Qué incluye |
|---|---|---|
| Bot de menús | USD 20–60/mes | Opciones fijas, no entiende consultas específicas sobre una propiedad |
| Plataforma self-service con IA | USD 80–200/mes | IA real, pero el cliente la configura y mantiene |
| Servicio gestionado (agente hecho y operado) | Desde USD 300/mes | Armado, entrenamiento, integración con inventario y operación a cargo del proveedor — **esta es la categoría del crm-agent** |

Fuente: [¿Cuánto cuesta un chatbot de WhatsApp para una inmobiliaria?](https://agenteala.com/blog/cuanto-cuesta-chatbot-whatsapp-inmobiliaria) — Agenteala, competidor directo (mismo país, mismo tipo de producto: agente de IA gestionado para inmobiliarias, integrado a WhatsApp e inventario). Fecha de publicación: julio 2026.

Nota sobre esta fuente: en el modelo de Agenteala, el plan mensual fijo (desde USD 300) cubre el trabajo, y aparte se paga "al costo" el consumo de IA y de la API de WhatsApp de Meta (que en Argentina describen como marginal, centavos por conversación) — no hay costo de setup escondido ni permanencia larga.

## Referencia alternativa: cobrar como proyecto (pago único + mantenimiento)

Si en vez de un modelo de suscripción mensual preferís cobrar por la implementación (más un fee de mantenimiento aparte), la referencia de mercado para freelancers/agencias en Argentina en 2026 es:

- Integraciones simples: freelancer junior, $400.000–$800.000 ARS.
- Agente con lógica de negocio + integraciones (como el crm-agent: Calendar + Sheets + tool-use): freelancer senior, $1.200.000–$2.500.000 ARS por proyecto.
- Agente con base de conocimiento propia y panel de administración: $1.500.000–$3.000.000 ARS con freelancer senior; $4.000.000–$10.000.000 ARS con una agencia que entrega el sistema completo.
- En dólares, para agentes de IA puntuales tipo reservas/leads: USD 500–2.500 por proyecto.

Fuente: [Cuánto cuesta implementar IA en una empresa argentina en 2026](https://segundoenfoque.com/cuanto-cuesta-implementar-inteligencia-artificial-en-una-empresa-argentina-en-2026) y [Servicios freelance de IA en Argentina: precios](https://www.trabajofreelance.com.ar/servicios-freelance-de-inteligencia-artificial-en-argentina-precios-y-como-contratar/) — julio/agosto 2026.

## Dónde encaja el crm-agent hoy

Por complejidad (tool-use con 4 herramientas, 2 integraciones de Google, memoria persistente, reintentos), el crm-agent está más cerca del segmento "servicio gestionado" o del tramo freelancer senior — no es un bot de menús ni una plataforma self-service básica. Eso lo ubica en:

- Si cobrás mensual: en la franja de referencia de Agenteala (desde ~USD 300/mes), ajustando según qué tan hecho a medida quede para este cliente en particular.
- Si cobrás por proyecto: en la franja $1.200.000–$2.500.000 ARS (o USD 500–2.500 si cotizás en dólares), dependiendo de cuánta personalización adicional pida el cliente sobre lo que ya existe.

**PRECIO A VALIDAR**: estos son rangos de mercado, no tu costo real ni tu margen deseado. Antes de poner un número final, sumá tu propio costo operativo (Claude API y APIs de Google están mayormente dentro de niveles gratuitos/bajo costo a este volumen) y decidí el margen que querés.

## Recomendación para la reunión del viernes

No cerrar un número en la primera reunión (ver la guía de presentación). Si preguntan por un rango orientativo, podés compartir que "servicios gestionados similares en el mercado argentino arrancan alrededor de los USD 300 mensuales", sin comprometerte a esa cifra exacta para este caso todavía.
