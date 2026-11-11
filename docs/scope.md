# Alcance del MVP

## 1. Objetivo del MVP

Demostrar un flujo bancario completo de punta a punta, con dos microservicios, datos sintéticos,
despliegue en Kubernetes, CI/CD y observabilidad.

> Un cliente se registra, abre una cuenta, recibe un depósito simulado, transfiere a otra cuenta,
> consulta saldo y movimientos, y el backoffice puede auditar y revertir la operación.

## 2. Personas

| Persona | Permisos |
| --- | --- |
| Cliente | Ver sus cuentas, movimientos y transferencias; iniciar transferencias. |
| Operador | Ver clientes y cuentas, congelar cuentas, aprobar ajustes, auditar. |
| Auditor | Ver evidencia, logs de auditoría y reportes; no modificar datos. |
| Servicio interno | Llamar APIs internas con scopes acotados. |

## 3. Incluido en el MVP

### 3.1 Identidad

- Login con Keycloak (OIDC).
- Roles `customer`, `operator`, `auditor`.
- MFA simulada o configurable para clientes.
- Sesión con expiración y refresh.
- Autorización por recurso: un cliente solo ve sus cuentas.

### 3.2 Clientes y cuentas

- Alta de cliente simulado (sin KYC real).
- Apertura de una cuenta de depósito en moneda única.
- Estados: `ACTIVE`, `FROZEN`, `CLOSED`.
- Consulta de saldo disponible y contable.
- Historial de movimientos paginado.
- Extracto simple en CSV/PDF (puede ser CSV en el MVP).

### 3.3 Dinero y ledger

- Ledger de doble entrada.
- Asientos inmutables con débitos y créditos balanceados.
- Posting atómico de transferencias internas.
- Holds para transferencias externas futuras (diseño, no implementación inicial).
- Reversos mediante asientos compensatorios.
- Conciliación básica entre postings y saldos.

### 3.4 Transferencias

- Transferencia interna entre dos cuentas del banco.
- Idempotencia por `Idempotency-Key`.
- Estados: `RECEIVED`, `VALIDATED`, `POSTING`, `COMPLETED`, `FAILED`, `UNKNOWN`.
- Límite por operación y por día (configurable).
- Consulta de estado por ID.
- Evento `transfer.completed.v1` o `transfer.failed.v1`.

### 3.5 Backoffice

- Búsqueda de cliente/cuenta.
- Vista de ledger y auditoría.
- Congelar/descongelar cuenta.
- Depósito simulado con aprobación de cuatro ojos (operador + supervisor).
- Reverso de una transferencia con motivo y evidencia.

### 3.6 Operación

- Docker Compose para dependencias locales.
- Floci para S3, SQS/SNS, Secrets Manager y ECR.
- kind o k3d para Kubernetes local.
- CI con lint, tests, SAST, dependency scan, build, SBOM, image scan y firma.
- GitOps con Argo CD.
- Observabilidad con OpenTelemetry, Prometheus, Grafana, Loki y Tempo.
- Backups y restore probados en staging.

## 4. Fuera del MVP

- Tarjetas de crédito/débito reales.
- Préstamos, intereses, plazos fijos e inversiones.
- Multi-moneda y FX.
- Pagos reales (ACH, SEPA, SWIFT, PIX, transferencias interbancarias reales).
- KYC/AML real con proveedores externos.
- App móvil nativa.
- Open Banking.
- Fraud scoring avanzado con ML.
- Multi-tenant real.
- Cumplimiento regulatorio real.
- Alta disponibilidad multi-región.

## 5. Requisitos no funcionales

| Área | Requisito |
| --- | --- |
| Consistencia | Posting de dinero atómico y balanceado. |
| Disponibilidad | Objetivo de 99.9% en staging/producción simulada. |
| Latencia | Lecturas p95 < 300 ms; transferencia p95 < 1 s (sin contar red externa). |
| Seguridad | OIDC, RBAC, mínimo privilegio, cifrado en tránsito y reposo. |
| Auditoría | Toda operación sensible registrada con actor, timestamp y correlación. |
| Observabilidad | Trazas, métricas y logs correlacionados por `correlation_id`. |
| Recuperación | RPO ≤ 15 min, RTO ≤ 1 h en producción simulada. |
| Privacidad | Solo datos sintéticos; sin PII real. |
| Mantenibilidad | ADRs, contratos versionados y tests de caminos críticos. |

## 6. Historias de usuario principales

### HU-01 — Registro y apertura de cuenta

Como cliente, quiero registrarme y abrir una cuenta para poder recibir y transferir dinero.

**Criterios de aceptación:**

- El cliente se autentica vía Keycloak.
- Se crea un cliente y una cuenta en estado `ACTIVE`.
- El evento de apertura queda auditado.
- El cliente ve su cuenta con saldo cero.

### HU-02 — Depósito simulado

Como operador, quiero registrar un depósito simulado para acreditar fondos a una cuenta.

**Criterios de aceptación:**

- El depósito requiere aprobación de cuatro ojos.
- Se crea un asiento balanceado: débito a cuenta interna, crédito a cuenta del cliente.
- El saldo disponible aumenta exactamente por el monto.
- Queda evidencia de operador, aprobador y motivo.

### HU-03 — Transferencia interna

Como cliente, quiero transferir dinero a otra cuenta del banco sin riesgo de duplicados.

**Criterios de aceptación:**

- La solicitud exige `Idempotency-Key`.
- Un reintento con la misma clave devuelve el mismo resultado.
- Se debita y acredita en un único asiento atómico.
- Si no hay saldo, la transferencia falla sin mover dinero.
- El cliente ve el estado y el comprobante.

### HU-04 — Historial y extracto

Como cliente, quiero ver mis movimientos y descargar un extracto.

**Criterios de aceptación:**

- Los movimientos se ordenan por fecha descendente.
- Se puede filtrar por rango de fechas.
- El extracto incluye saldo inicial, movimientos y saldo final.
- No se exponen datos de otras cuentas.

### HU-05 — Auditoría y reverso

Como auditor, quiero reconstruir una operación y revertirla si fue errónea.

**Criterios de aceptación:**

- Se ve el asiento original, sus postings y eventos.
- El reverso crea un nuevo asiento compensatorio; no borra el original.
- El reverso requiere motivo y aprobación.
- El saldo queda correcto y la auditoría es completa.

## 7. Supuestos

- Moneda única (a definir; los ejemplos usan USD).
- Un cliente puede tener una cuenta en el MVP.
- Las transferencias internas son instantáneas.
- No hay integración con redes de pago reales.
- El backoffice es web.
- La nube principal es AWS, con Floci para desarrollo local.
- El stack backend es Java/Spring Boot, salvo confirmación contraria.

## 8. Criterios de salida del MVP

- [ ] Flujo completo cliente → cuenta → depósito → transferencia → extracto.
- [ ] Backoffice con auditoría y reverso.
- [ ] Dos servicios desplegados en Kubernetes local con GitOps.
- [ ] CI/CD con promoción y rollback.
- [ ] Observabilidad y SLOs básicos.
- [ ] Tests de ledger, idempotencia y contrato.
- [ ] Documentación y ADRs actualizados.
- [ ] Sin secretos ni PII real.
