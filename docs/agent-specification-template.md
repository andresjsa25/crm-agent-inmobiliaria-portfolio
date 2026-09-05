# Agent Specification

Plantilla que todo agente de este ecosistema debe completar antes de considerarse diseñado. Fusiona la sección 10 del Master Prompt ("Diseño del Agente") con el "Agent Specification" de la arquitectura acordada. Ningún campo se deja en blanco: si no se sabe, se escribe "PENDIENTE DE DEFINIR" — nunca se inventa.

---

## 1. Identificación

- Nombre:
- Versión: (MAJOR.MINOR.PATCH)
- Categoría: (RRHH / Ventas / Marketing / Atención al cliente / Finanzas / Operaciones / Soporte / E-commerce / Gestión de proyectos / otra)
- Sector / cliente ideal:
- Estado: IDEA / DISEÑANDO / MVP / TESTING / VALIDADO / PRODUCTIZADO / VENDIBLE
- Fecha de creación:
- Última actualización:

## 2. Objetivo y problema

- Objetivo del agente:
- Problema empresarial que resuelve:
- Usuario que lo va a usar (quién interactúa con el agente):
- Quién recibe los resultados (si es distinto del usuario):

## 3. Alcance

- Qué hace:
- Qué NO hace (explícito — evita expectativas infladas):

## 4. Disparador

¿Qué inicia al agente? (mensaje entrante, formulario, webhook, cron, etc.)

## 5. Inputs

¿Qué información recibe, y de dónde?

## 6. Knowledge

¿Qué conocimiento necesita para operar? (fichas de la base de conocimiento interna que apliquen, documentos del cliente, catálogos, políticas)

## 7. Model

¿Qué LLM/modelo usa y por qué ese y no otro? (no incluido en este repo)

## 8. Memory

¿Dónde y cómo persiste estado/contexto entre turnos o sesiones? (ver componentes en components/memory/)

## 9. Tools / Integraciones

Lista de herramientas/APIs que el agente puede invocar (ver la base de conocimiento interna y los componentes en components/action/)

## 10. Workflow

Flujo paso a paso, en el formato simple del master prompt:

```
DISPARADOR
↓
AUTOMATIZACIÓN
↓
IA
↓
DECISIÓN
↓
ACCIÓN
↓
REGISTRO
↓
NOTIFICACIÓN / RESPUESTA
↓
HUMANO SI ES NECESARIO
```

## 11. Decision rules

- Qué decisiones puede tomar automáticamente:
- Qué decisiones requieren aprobación humana:
- Qué información NO puede modificar:
- Qué hace cuando no sabe qué hacer:

## 12. Actions

Qué acciones concretas ejecuta (ver componentes ACTION)

## 13. Output

¿Qué produce y en qué formato? ¿A quién se lo entrega?

## 14. Human approval

¿En qué puntos exactos se detiene a esperar aprobación humana? (contratación, despido, decisiones médicas/financieras/legales importantes, pagos, envío de info sensible, cualquier decisión que pueda afectar negativamente a una persona)

## 15. Error handling

- Qué pasa si falla una API externa:
- Qué pasa si falta información:
- Qué pasa si llega información incorrecta o ambigua:

## 16. Security

- ¿Maneja datos sensibles? ¿Cuáles?
- ¿Qué acceso/permisos tiene y cuáles NO tiene?
- ¿Cómo se protegen las credenciales?

## 17. Cost limit

Presupuesto aproximado de operación (tokens, APIs, automatización) — marcar "PRECIO A VALIDAR" si no está confirmado.

## 18. Success metrics

¿Cómo se mide que el agente funciona / que vale la pena?

## 19. Integraciones (resumen)

Lista corta de todos los sistemas externos que toca este agente (para referencia rápida).
