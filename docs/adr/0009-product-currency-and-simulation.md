# ADR-0009 — Producto, moneda y operación simulada

- **Estado:** Aceptado
- **Fecha:** 2026-11-13
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Visión](../vision.md), [Alcance del MVP](../scope.md), [Ledger](../domain/ledger.md), [Eventos v0](../contracts/events-v0.md)

## Contexto

El MVP necesita una moneda y un mercado concretos para fijar ejemplos, validaciones, límites y
formato de extractos. También necesita definir cómo se simulan notificaciones, KYC/AML y los
objetivos de nivel de servicio.

## Decisión

| Decisión | Valor |
| --- | --- |
| D-02 — Moneda y mercado | **COP (peso colombiano)**, moneda única, mercado Colombia |
| D-18 — Email/SMS | Mailpit local; sin proveedor externo en el MVP |
| D-19 — KYC/AML | Mock determinista; sin proveedor externo |
| D-20 — SLOs iniciales | 99.9% de disponibilidad en staging/producción simulada |

Reglas derivadas:

- Todo monto se expresa como entero en unidades menores (`amount_minor`) + `COP`.
- COP usa dos decimales según ISO 4217; el redondeo de presentación se define por producto.
- Los ejemplos de documentación, contratos y eventos usan COP.
- No se soporta multi-moneda ni FX en el MVP.
- No se procesan pagos, tarjetas ni transferencias interbancarias reales.

## Consecuencias

### Positivas

- El dominio, la UI y los contratos quedan alineados con un mercado concreto.
- Mailpit y el mock de KYC permiten probar los flujos sin costos ni credenciales externas.
- El SLO de 99.9% es medible en local/staging y exigente sin ser irreal para el MVP.

### Negativas

- Los ejemplos dejan de ser neutros: cambiar de mercado implica revisar montos y formatos.
- COP tiene particularidades de redondeo y hábitos de uso (por ejemplo, montos sin centavos en la
  práctica) que la UI deberá manejar explícitamente.
- Un SLO de 99.9% requiere observabilidad y alertas desde el walking skeleton.

### Mitigaciones

- Centralizar moneda y formato en configuración, no en literales dispersos.
- Documentar las reglas de redondeo cuando se implemente la UI.
- Medir el SLO con Prometheus desde la Fase 1.

## Alternativas consideradas

1. **ARS, USD o EUR.** Válidas técnicamente, pero el mercado objetivo es Colombia.
2. **Multi-moneda.** Fuera del alcance del MVP; multiplica complejidad contable y de UI.
3. **Sin SLO inicial.** Rechazado: la observabilidad se agrega desde el walking skeleton.

## Notas

Este ADR cierra D-02, D-18, D-19 y D-20 de
[decisiones pendientes](../decisions-pending.md).
