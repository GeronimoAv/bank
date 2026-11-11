# ADR-0003 — Ledger de doble entrada

- **Estado:** Aceptado
- **Fecha:** 2026-11-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Ledger](../domain/ledger.md), [ADR-0002](0002-service-boundaries.md)

## Contexto

Un banco no puede representar el dinero con un simple campo `balance` que se actualiza. Necesita
trazabilidad, auditabilidad y la posibilidad de reconstruir saldos. Los errores deben corregirse
sin borrar historia.

## Decisión

Usar un **ledger de doble entrada** con:

- `journal_entry` inmutable y balanceado;
- `posting` por cada línea de débito/crédito;
- saldos derivados y reconciliables;
- holds para reservas;
- reversos como nuevos asientos;
- montos en enteros de unidades menores + moneda;
- idempotencia a nivel de asiento.

El ledger vive en `core-banking-service` y es la fuente de verdad del dinero.

## Consecuencias

### Positivas

- Auditoría completa y reconstruible.
- Invariantes verificables automáticamente.
- Reversos sin pérdida de información.
- Base sólida para conciliación y reportes.
- Se evita el doble gasto por diseño.

### Negativas

- Más tablas y complejidad que un simple balance.
- Requiere tests de invariantes y reconciliación.
- Las consultas de saldo necesitan estrategia de performance.
- Los desarrolladores deben entender contabilidad básica.

### Mitigaciones

- Documentación y glosario.
- Tests de propiedad para invariantes.
- Snapshots de saldo con reconciliación.
- Revisión obligatoria de cambios en ledger.

## Alternativas consideradas

1. **Balance mutable.** Simple, pero no auditable ni reconstruible. Rechazado.
2. **Event sourcing puro.** Atractivo, pero agrega complejidad de proyecciones y versionado. Se puede
   evaluar más adelante sin cambiar la fuente de verdad.
3. **Ledger en un servicio separado.** Prematuro para el MVP; se puede extraer luego.

## Reglas no negociables

- No se actualiza ni borra un asiento `POSTED`.
- No se usan floats para dinero.
- Todo asiento debe balancear.
- Todo reverso referencia el asiento original.
- La reconciliación alerta, no corrige silenciosamente.
