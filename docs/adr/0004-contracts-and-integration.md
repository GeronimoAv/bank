# ADR-0004 — Contratos e integración

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Contratos OpenAPI](../contracts/openapi/), [Eventos v0](../contracts/events-v0.md), [Trabajo multi-repo](../governance/multi-repo-workflow.md)

## Contexto

En una organización multi-repo, los servicios se despliegan de forma independiente. Sin contratos
explícitos, productores y consumidores se rompen en producción. La integración debe ser verificable
antes del despliegue.

## Decisión

1. **Contratos primero.** OpenAPI para APIs síncronas y AsyncAPI/JSON Schema para eventos.
2. **Repositorio `contracts` como fuente de verdad.** Los servicios generan clientes/stubs desde
   ahí.
3. **Versionado explícito.** `/v1` en APIs; `eventType.vN` en eventos.
4. **Compatibilidad hacia atrás.** Cambios aditivos dentro de la misma versión; breaking changes
   requieren nueva versión y plan de migración.
5. **Contract testing.** Pact para interacciones críticas y `oasdiff` para detectar breaking
   changes en OpenAPI.
6. **Expand/contract.** Agregar lo nuevo, migrar consumidores, retirar lo viejo.
7. **Feature flags** para separar deploy de release.
8. **`can-i-deploy`** como gate antes de promover a producción.

## Consecuencias

### Positivas

- Menos integraciones rotas.
- Despliegues independientes más seguros.
- Contratos visibles y auditables.
- Clientes generados reducen errores manuales.
- Se practica un proceso real de cambio cross-repo.

### Negativas

- Overhead de mantener contratos y versiones.
- Requiere disciplina de compatibilidad.
- Pact agrega infraestructura (broker) y pruebas.
- Los cambios cross-repo necesitan coordinación.

### Mitigaciones

- CI valida contratos en cada PR.
- `contracts` tiene su propio pipeline.
- Renovate/Dependabot actualizan dependencias.
- Guía de cambio cross-repo y orden de merge.
- ADR para cambios incompatibles.

## Alternativas consideradas

1. **Contratos en cada repo de servicio.** Más autonomy, pero deriva y poca visibilidad. Rechazado.
2. **Sin contratos formales.** Rechazado por riesgo de integración.
3. **gRPC/Protobuf.** Válido, pero el MVP usa HTTP/JSON por simplicidad y herramientas. Se puede
   evaluar para APIs internas de alto rendimiento.

## Notas

El broker de Pact puede correr en `platform` o usarse un servicio gestionado. La decisión queda
para la fase de implementación.
