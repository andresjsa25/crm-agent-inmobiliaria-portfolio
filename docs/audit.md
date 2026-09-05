# AUDIT REPORT — CRM Agent Inmobiliaria

Fecha: 13/08/2026 (actualizado el mismo día tras corrida real con `run-tests.mjs` y fix del hallazgo de prompt)
Auditor: revisión de código completa (`tools.js`, `agent.js`, `server.js`, `db.js`, `retry.js`) + Agent Specification + Testing Framework, más 9 conversaciones reales corridas dos veces (antes y después del fix de prompt) vía `run-tests.mjs`.

**Score: 80/100** — Listo para demo / piloto controlado, con mejoras pendientes antes de escalar.

## Checklist

| Categoría | Estado | Puntos |
|---|---|---|
| Architecture | ✓ | 10/10 |
| Workflow | ✓ (verificado en la práctica) | 10/10 |
| API connections | ✓ (retries con backoff en las 4 llamadas externas) | 9/10 |
| Prompt | ✓ (verificado en vivo: resiste los 3 jailbreaks de TEST 10) | 9/10 |
| Data handling | ✓ | 8/10 |
| Error handling | ✓ (incluye el fix de agendar_cita del 13/08, verificado en vivo) | 9/10 |
| Human approval | ✗ | 3/10 |
| Fallback | ✓ (verificado en vivo — TEST 7 y TEST 8 respondieron bien) | 9/10 |
| Cost | ⚠️ | 5/10 |
| Documentation | ✓ | 9/10 |

**CRITICAL: 0 · HIGH: 1 · MEDIUM: 2 · LOW: 1**

## Hallazgos

**[RESUELTO 13/08/2026] Protección contra manipulación del prompt.** La primera corrida de `run-tests.mjs` confirmó el hallazgo: TEST 10b logró que el agente revelara las 4 herramientas, sus parámetros y su lógica interna de uso. Se agregó una instrucción explícita al system prompt y se volvió a correr — los 3 casos de TEST 10 pasan ahora. Nota: esto cubre los intentos probados, no garantiza inmunidad a cualquier variante de jailbreak futura.

**[HIGH] Sin aprobación humana antes de agendar_cita o guardar_lead.** El agente actúa de punta a punta sin ningún checkpoint. El master prompt pide evaluar esto para decisiones que afectan a una persona. Mi lectura: agendar una visita es una acción de bajo riesgo y reversible (se puede cancelar a mano), así que no es CRITICAL — pero es una decisión consciente que falta tomar y documentar, no un olvido.

**[MEDIUM] Sin control de duplicados en guardar_lead.** Un mismo cliente que escribe varias veces genera filas repetidas en el Sheet de leads. No rompe nada, pero ensucia los datos que el equipo de la inmobiliaria va a mirar.

**[MEDIUM] Costo sin validar ni acotar.** No hay un tope de gasto configurado, y el precio real de Claude Haiku 4.5 sigue sin confirmar (precio de Claude Haiku 4.5 sin confirmar al momento de esta auditoría). A bajo volumen no es un riesgo real, pero conviene tenerlo resuelto antes de escalar a varios clientes en simultáneo.

**[LOW] Sin timeout global ni circuit breaker.** Los reintentos tienen backoff pero no hay un límite de tiempo total por request ni un mecanismo que corte llamadas si un servicio externo está caído por minutos. No hace falta a este volumen.

## Lo que ya está bien (sin objeciones)

Arquitectura simple y apropiada para el problema. El flujo completo (buscar propiedad → consultar horario → agendar → guardar lead) está verificado funcionando en la práctica, no solo por código, incluyendo casos ambiguos y mensajes largos/desordenados (TEST 4, TEST 8) manejados con criterio razonable. Memoria persistente en SQLite, verificada. Reintentos con backoff en las 4 llamadas externas. El bug de doble reserva / hora fuera de horario (TEST 3) se corrigió y se verificó el mismo día. El gap de prompt injection se encontró, corrigió y re-verificó el mismo día. Documentación completa: ficha ejecutiva (docs/agent-card.md), Agent Specification aplicada, 6 componentes reutilizables documentados, plan de pruebas con 10 casos corrido en vivo dos veces.

## RECOMMENDATION

Apto para mostrar el viernes. Ningún hallazgo es CRITICAL ni compromete datos o plata. Antes de facturarle esto a un cliente de forma continua (no solo demo), resolver el HIGH restante: decidir explícitamente (documentado) si `agendar_cita` y `guardar_lead` necesitan un paso de confirmación humana, o si se acepta conscientemente que no lo tengan por ser acciones de bajo riesgo/reversibles. Los MEDIUM y el LOW pueden esperar a que haya más de un cliente activo.
