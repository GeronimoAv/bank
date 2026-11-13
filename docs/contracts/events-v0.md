# Eventos v0

## 1. Principio

Los eventos describen hechos consumados. Se publican después del commit, mediante el patrón
outbox. Un evento no reemplaza la fuente de verdad: el ledger y las tablas de cada servicio siguen
siendo la autoridad.

## 2. Sobre (envelope)

Todo evento usa el mismo sobre:

```json
{
  "eventId": "5f0c2c9e-7a5c-4a0e-9d3a-5c2b7d9e1f10",
  "eventType": "transfer.completed.v1",
  "eventVersion": 1,
  "occurredAt": "2026-11-09T15:04:05Z",
  "producer": "transfers-service",
  "correlationId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "causationId": "3d1f2a4b-8c6d-4e5f-9a0b-1c2d3e4f5a6b",
  "aggregateType": "Transfer",
  "aggregateId": "0b7e6c1a-2d3f-4a5b-8c9d-0e1f2a3b4c5d",
  "payload": {}
}
```

| Campo | Obligatorio | Descripción |
| --- | --- | --- |
| `eventId` | Sí | UUID único del evento; usado para deduplicar. |
| `eventType` | Sí | Nombre estable con versión. |
| `eventVersion` | Sí | Entero de versión del esquema. |
| `occurredAt` | Sí | Timestamp UTC del hecho. |
| `producer` | Sí | Servicio que publica. |
| `correlationId` | Sí | ID de la operación de negocio. |
| `causationId` | No | ID del evento/comando que lo causó. |
| `aggregateType` | Sí | Tipo de agregado. |
| `aggregateId` | Sí | ID del agregado. |
| `payload` | Sí | Datos del evento. |

## 3. Catálogo v0

| Evento | Productor | Descripción | Consumidores previstos |
| --- | --- | --- | --- |
| `transfer.initiated.v1` | `transfers-service` | Se recibió una transferencia válida. | Auditoría, analytics. |
| `transfer.completed.v1` | `transfers-service` | La transferencia se completó. | Notificaciones, auditoría. |
| `transfer.failed.v1` | `transfers-service` | La transferencia falló de forma definitiva. | Notificaciones, auditoría. |
| `posting.created.v1` | `core-banking-service` | Se registró un asiento contable. | Auditoría, analytics, conciliación. |
| `account.status.changed.v1` | `core-banking-service` | Cambió el estado de una cuenta. | Notificaciones, backoffice. |

## 4. Ejemplos

### 4.1 `transfer.completed.v1`

```json
{
  "eventId": "5f0c2c9e-7a5c-4a0e-9d3a-5c2b7d9e1f10",
  "eventType": "transfer.completed.v1",
  "eventVersion": 1,
  "occurredAt": "2026-11-09T15:04:05Z",
  "producer": "transfers-service",
  "correlationId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "causationId": null,
  "aggregateType": "Transfer",
  "aggregateId": "0b7e6c1a-2d3f-4a5b-8c9d-0e1f2a3b4c5d",
  "payload": {
    "transferId": "0b7e6c1a-2d3f-4a5b-8c9d-0e1f2a3b4c5d",
    "sourceAccountId": "1a2b3c4d-5e6f-7a8b-9c0d-1e2f3a4b5c6d",
    "targetAccountId": "6f5e4d3c-2b1a-0f9e-8d7c-6b5a4f3e2d1c",
    "amount": { "amountMinor": 3000000, "currency": "COP" },
    "journalEntryId": "9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d",
    "completedAt": "2026-11-09T15:04:05Z"
  }
}
```

### 4.2 `transfer.failed.v1`

```json
{
  "eventId": "8b1d2e3f-4a5b-6c7d-8e9f-0a1b2c3d4e5f",
  "eventType": "transfer.failed.v1",
  "eventVersion": 1,
  "occurredAt": "2026-11-09T15:06:00Z",
  "producer": "transfers-service",
  "correlationId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "causationId": null,
  "aggregateType": "Transfer",
  "aggregateId": "0b7e6c1a-2d3f-4a5b-8c9d-0e1f2a3b4c5d",
  "payload": {
    "transferId": "0b7e6c1a-2d3f-4a5b-8c9d-0e1f2a3b4c5d",
    "code": "INSUFFICIENT_FUNDS",
    "message": "Saldo insuficiente",
    "failedAt": "2026-11-09T15:06:00Z"
  }
}
```

### 4.3 `posting.created.v1`

```json
{
  "eventId": "c1d2e3f4-a5b6-7c8d-9e0f-1a2b3c4d5e6f",
  "eventType": "posting.created.v1",
  "eventVersion": 1,
  "occurredAt": "2026-11-09T15:04:05Z",
  "producer": "core-banking-service",
  "correlationId": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
  "causationId": "5f0c2c9e-7a5c-4a0e-9d3a-5c2b7d9e1f10",
  "aggregateType": "JournalEntry",
  "aggregateId": "9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d",
  "payload": {
    "journalEntryId": "9a8b7c6d-5e4f-3a2b-1c0d-9e8f7a6b5c4d",
    "type": "INTERNAL_TRANSFER",
    "postings": [
      {
        "ledgerAccountId": "1a2b3c4d-5e6f-7a8b-9c0d-1e2f3a4b5c6d",
        "direction": "DEBIT",
        "amount": { "amountMinor": 3000000, "currency": "COP" }
      },
      {
        "ledgerAccountId": "6f5e4d3c-2b1a-0f9e-8d7c-6b5a4f3e2d1c",
        "direction": "CREDIT",
        "amount": { "amountMinor": 3000000, "currency": "COP" }
      }
    ]
  }
}
```

## 5. Garantías

- **Entrega:** at-least-once.
- **Orden:** no garantizado globalmente; se usa `aggregateId` como partition key cuando el broker
  lo permite.
- **Deduplicación:** el consumidor guarda `eventId` procesados.
- **Publicación:** outbox transaccional; nunca se publica antes del commit.
- **Reintentos:** backoff exponencial con jitter y dead-letter queue.
- **Retención:** definir por tópico/cola; mínimo 7 días en staging.

## 6. Reglas para consumidores

1. Validar el sobre y la versión.
2. Deduplicar por `eventId`.
3. Procesar de forma idempotente.
4. No asumir orden entre agregados distintos.
5. Si falla, reintentar; después de N intentos, enviar a DLQ.
6. Registrar `correlationId` en logs y trazas.
7. No leer la base de datos del productor.
8. No bloquear el hilo principal por un consumidor lento.

## 7. Evolución de esquema

- Cambios aditivos dentro de `v1`.
- Campos nuevos opcionales con default.
- No renombrar ni eliminar campos en `v1`.
- Cambios incompatibles → `v2` y convivencia temporal.
- El productor debe soportar consumidores de la versión anterior durante la migración.
- El contrato se valida en CI contra JSON Schema/AsyncAPI.

## 8. Seguridad y privacidad

- No incluir PII real.
- No incluir números de tarjeta, documentos, direcciones ni teléfonos.
- Usar IDs internos y seudónimos.
- Los eventos se cifran en tránsito y en reposo.
- El acceso a colas se restringe por IAM y network policies.
- Los logs no deben volcar el payload completo si contiene datos sensibles.

## 9. Pendientes

- Elegir broker definitivo: SQS/SNS vs Kafka.
- Definir schema registry o validación por JSON Schema en CI.
- Definir retención y DLQ por entorno.
- Definir si `posting.created.v1` es necesario en el MVP o si alcanza con el evento de transferencia.
