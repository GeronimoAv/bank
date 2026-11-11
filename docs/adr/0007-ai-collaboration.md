# ADR-0007 — Colaboración con IA

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [AGENTS.md](../../AGENTS.md), [CONTRIBUTING.md](../../CONTRIBUTING.md)

## Contexto

La IA acelera el trabajo, pero también puede generar cambios no solicitados, decisiones implícitas,
pérdida de contexto y riesgos de seguridad. El proyecto necesita un protocolo explícito para que la
IA sea útil sin tener rienda suelta.

## Decisión

Adoptar un flujo obligatorio de cinco fases para agentes de IA:

1. **Contexto:** leer reglas, documentos y estado del repo.
2. **Plan:** anunciar objetivo, alcance, supuestos, archivos, comandos, riesgos y criterios de
   aceptación. El plan es una propuesta sujeta a cambios.
3. **Ejecución:** informar cada archivo tocado y cada comando ejecutado.
4. **Verificación:** revisar diff, correr validaciones y detectar secretos/PII.
5. **Resumen:** cambios, comandos, decisiones, riesgos y próximos pasos.

Reglas adicionales:

- La IA no hace commits, pushes, PRs, despliegues ni acciones destructivas sin pedido explícito.
- La IA no toma decisiones de producto, arquitectura, seguridad, dinero o credenciales sin
  aprobación.
- Toda decisión durable se registra en un ADR.
- Toda decisión abierta se registra en `decisions-pending.md`.
- No se agrega atribución de IA a los commits.
- El humano es accountable de cada cambio.

El detalle operativo vive en [`AGENTS.md`](../../AGENTS.md).

## Consecuencias

### Positivas

- Cambios previsibles y revisables.
- Menos riesgo de acciones no solicitadas.
- Decisiones trazables.
- Mejor colaboración humano-IA.
- Onboarding más rápido de cualquier agente.

### Negativas

- Más pasos y más comunicación.
- El plan puede ser más largo que el cambio.
- Requiere disciplina del humano para aprobar.

### Mitigaciones

- Plantillas cortas en `AGENTS.md`.
- Escalar el nivel de detalle según el riesgo.
- Para cambios triviales, un plan breve alcanza.
- Los cambios destructivos siempre requieren aprobación.

## Alternativas consideradas

1. **IA con autonomía total.** Rechazado por riesgo y falta de trazabilidad.
2. **IA solo de lectura.** Demasiado restrictivo; no aprovecha la asistencia.
3. **Aprobación humana en cada archivo.** Inviable; se aprobó el plan y se informa el progreso.

## Consecuencias operativas

- Cualquier agente que trabaje en el repo debe leer `AGENTS.md` antes de actuar.
- Si una instrucción contradice el protocolo, se detiene y pregunta.
- El resumen final se guarda en la conversación o en el PR, según corresponda.
