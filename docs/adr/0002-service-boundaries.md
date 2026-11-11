# ADR-0002 — Límites de los servicios

- **Estado:** Aceptado
- **Fecha:** 2026-10-09
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Límites de servicios](../architecture/service-boundaries.md), [Flujo de transferencia](../architecture/transfer-flow.md)

## Contexto

El MVP necesita dos microservicios conectados que cubran el flujo bancario completo. La división
debe respetar transacciones de negocio, ownership de datos y posibilidades reales de despliegue
independiente.

## Decisión

Dividir en:

1. **`core-banking-service`**: clientes, cuentas, ledger, saldos, holds y extractos. Es dueño de
   la base `core_banking` y del posting atómico.
2. **`transfers-service`**: solicitudes de transferencia, idempotencia, orquestación, máquina de
   estados y eventos. Es dueño de la base `transfers`.

Reglas:

- Cada servicio tiene su propia base de datos.
- No hay acceso cruzado a tablas ni joins cross-service.
- `transfers-service` llama a `core-banking-service` por API interna.
- El débito y el crédito de una transferencia se registran en un único asiento en core banking.
- No se usa 2PC; los timeouts se concilian por idempotencia.
- Los eventos se publican con outbox.

## Consecuencias

### Positivas

- El dinero se mueve de forma atómica.
- Los límites reflejan transacciones reales.
- Se practica comunicación síncrona y asíncrona.
- Cada servicio puede desplegarse y escalar por separado.
- El ledger queda protegido en un solo dominio.

### Negativas

- `transfers-service` depende de la disponibilidad de core banking.
- Hay que manejar timeouts, reintentos y conciliación.
- La lectura de datos cruza servicios.
- El frontend consume dos APIs.

### Mitigaciones

- Idempotencia en ambos lados.
- Estado `UNKNOWN` y job de conciliación.
- Timeouts, circuit breaker y backoff.
- Contratos versionados y contract tests.
- Observabilidad con correlation ID.

## Alternativas consideradas

1. **`accounts-service` + `transfers-service`.** Similar, pero el nombre “accounts” no refleja que
   también posee clientes y ledger.
2. **`customers-service` + `accounts-service`.** No cubre transferencias sin un tercer servicio.
3. **Un solo servicio.** Más simple, pero no cumple el objetivo de dos microservicios conectados.
4. **Tres servicios con ledger separado.** Prematuro; agrega complejidad sin necesidad en el MVP.

## Cuándo revisar

- Si core banking se vuelve demasiado grande.
- Si el ledger necesita escalar o desplegarse por separado.
- Si notificaciones o fraude requieren ciclos de vida propios.
- Si el equipo se divide por dominios.
