# Observabilidad

## 1. Objetivo

Poder responder: ¿está funcionando?, ¿qué se rompió?, ¿a quién afecta?, ¿qué cambió? Sin
observabilidad, un banco simulado no sirve para practicar operación.

## 2. Pilares

| Pilar | Herramienta local | Uso |
| --- | --- | --- |
| Métricas | Prometheus + Grafana | Salud, latencia, errores, saturación. |
| Logs | Loki | Eventos de aplicación y diagnóstico. |
| Trazas | Tempo + OpenTelemetry | Flujo entre servicios. |
| Auditoría | PostgreSQL + S3 | Evidencia inmutable de operaciones. |

## 3. Convenciones

- `service.name` obligatorio.
- `service.version` = versión del artefacto.
- `deployment.environment` = local/staging/prod-sim.
- `correlation_id` en logs y trazas.
- `traceparent` W3C propagado.
- `customer_id` seudonimizado; nunca PII.
- Errores con `error.type` y `error.code`.
- No loggear payloads completos de transferencias.

## 4. Métricas clave

### RED por servicio

- `http_requests_total{service,method,route,status}`
- `http_request_duration_seconds{service,route}`
- `http_errors_total{service,route,code}`

### Negocio

| Métrica | Descripción |
| --- | --- |
| `transfers_total{status}` | Transferencias por estado. |
| `transfer_duration_seconds` | Latencia de transferencia. |
| `posting_total{type}` | Asientos creados. |
| `ledger_imbalance_total` | Asientos descuadrados (debe ser 0). |
| `outbox_lag_seconds` | Atraso de publicación de eventos. |
| `unknown_transfers_total` | Transferencias en estado `UNKNOWN`. |
| `reconciliation_mismatches_total` | Diferencias de conciliación. |
| `auth_failures_total` | Fallos de autenticación. |

### Infraestructura

- CPU, memoria, GC, conexiones DB, pool, réplicas.
- Latencia y errores de Postgres.
- Profundidad de cola y DLQ.
- Errores de Redis.

## 5. Dashboards

1. **Vista general:** salud por servicio, SLOs, errores.
2. **Transferencias:** embudo, estados, latencia, fallos, `UNKNOWN`.
3. **Ledger:** postings, balance, conciliación, descuadres.
4. **Plataforma:** Kubernetes, nodos, pods, Argo CD.
5. **Seguridad:** auth failures, rate limits, accesos denegados.

## 6. SLOs iniciales

| SLO | Objetivo | Ventana |
| --- | --- | --- |
| Disponibilidad de API | 99.9% | 30 días |
| Latencia de lectura p95 | < 300 ms | 30 días |
| Latencia de transferencia p95 | < 1 s | 30 días |
| Transferencias exitosas | > 99.5% | 30 días |
| Ledger descuadrado | 0 | Siempre |
| Outbox lag p95 | < 5 s | 30 días |

Los SLOs son objetivos de aprendizaje; se ajustan con datos reales.

## 7. Alertas

| Alerta | Condición | Severidad |
| --- | --- | --- |
| `LedgerImbalance` | `ledger_imbalance_total > 0` | Crítica |
| `HighTransferErrorRate` | > 5% en 10 min | Alta |
| `UnknownTransfersGrowing` | > umbral | Alta |
| `OutboxLagHigh` | p95 > 30 s | Media |
| `CoreBankingDown` | healthcheck fallando | Crítica |
| `PostgresConnectionsHigh` | > 80% | Media |
| `DLQNotEmpty` | > 0 | Alta |
| `AuthFailureSpike` | > umbral | Media |

Cada alerta debe tener runbook y owner.

## 8. Trazas

- Una traza por request.
- Spans: HTTP server, DB, llamada interna, outbox.
- Atributos: `correlation_id`, `transfer_id`, `journal_entry_id`.
- Muestreo: 100% en local, 10-20% en staging, ajustar por costo.
- No incluir PII.

## 9. Logs

- JSON estructurado.
- Niveles: ERROR, WARN, INFO, DEBUG.
- Campos: timestamp, level, service, version, correlation_id, trace_id, message.
- No loggear tokens, passwords, PII ni payloads sensibles.
- Retención: 7 días local, 30 días staging, según política en prod-sim.

## 10. Auditoría

- Separada de logs.
- Append-only.
- Campos: actor, acción, recurso, timestamp, correlation_id, resultado.
- Exportación a S3 con object lock en producción.
- Consulta desde backoffice con permisos.

## 11. Runbooks

Cada alerta crítica debe tener un runbook en `docs/operations/runbooks/` con:

- síntoma;
- impacto;
- diagnóstico;
- mitigación;
- rollback;
- escalamiento;
- postmortem.

## 12. Reglas

- No observar solo infraestructura; observar negocio.
- No alertar por todo; alertar por síntomas que afectan SLOs.
- No loggear datos sensibles.
- No depender de una sola herramienta.
- No cerrar un incidente sin evidencia y aprendizaje.
