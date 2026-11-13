# ADR-0010 — Stack de aplicación

- **Estado:** Aceptado
- **Fecha:** 2026-11-13
- **Decisores:** Geronimo Acevedo
- **Relacionados:** [Arquitectura](../architecture/overview.md), [Límites de servicios](../architecture/service-boundaries.md), [Contratos](../contracts/), [Desarrollo local](../operations/local-development.md)

## Contexto

La documentación fundacional asumía un stack, pero no lo había confirmado. El walking skeleton
necesita tecnologías concretas para generar el template de servicio, los pipelines y los
Dockerfiles.

## Decisión

| Decisión | Valor |
| --- | --- |
| D-04 — Backend | Java 21 + Spring Boot |
| D-05 — Frontend | React + TypeScript + Vite |
| D-07 — Base de datos | PostgreSQL 16, una base por servicio |
| D-08 — Mensajería | SQS/SNS (Floci en local) con patrón outbox |
| D-09 — Autenticación | Keycloak (OIDC/OAuth2) |

Reglas derivadas:

- Cada servicio es dueño de su esquema y su base; no hay acceso cruzado a tablas.
- El backend usa Maven y Java 21 LTS.
- El frontend usa Node.js 22 + pnpm, React y TypeScript con Vite.
- Los eventos se publican con outbox después del commit; los consumidores son idempotentes.
- Keycloak emite JWT para usuarios y client credentials para llamadas internas.

## Consecuencias

### Positivas

- Spring Boot ofrece transacciones, JPA/JDBC, validación y observabilidad maduras para el ledger.
- PostgreSQL 16 soporta transacciones ACID, locks y tipos adecuados para dinero.
- React + Vite mantiene el frontend simple y rápido de construir.
- SQS/SNS con Floci evita depender de AWS real durante el desarrollo.
- Keycloak cubre OIDC, roles y client credentials sin desarrollo propio.

### Negativas

- Java + Spring Boot tiene más ceremonia inicial que un stack Node.
- SQS/SNS no ofrece orden global ni exactly-once; exige idempotencia y DLQ.
- Keycloak agrega un componente stateful a la plataforma local.

### Mitigaciones

- `service-template` estandariza la estructura y el CI para no repetir ceremonia.
- Contratos y tests de idempotencia cubren las limitaciones de la mensajería.
- Keycloak se levanta con Docker Compose y configuración versionada.

## Alternativas consideradas

1. **Node.js/NestJS.** Menos ceremonia, pero menos maduro para transacciones contables complejas.
2. **MySQL.** Válido, pero PostgreSQL ofrece mejores garantías y herramientas para este dominio.
3. **Kafka.** Potente, pero sobredimensionado para el MVP; SQS/SNS + outbox alcanza.
4. **Auth0 o Spring Authorization Server.** Auth0 agrega costo externo; Spring Authorization Server
   agrega desarrollo propio. Keycloak equilibra ambos.

## Notas

Este ADR cierra D-04, D-05, D-07, D-08 y D-09 de
[decisiones pendientes](../decisions-pending.md).
