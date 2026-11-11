# Límites de los servicios

## 1. Principios

1. **Un servicio, una base de datos.** No hay acceso cruzado a tablas.
2. **Un servicio, un contrato.** OpenAPI para síncrono; eventos versionados para asíncrono.
3. **El ledger vive en un solo lugar.** El posting atómico no se reparte entre servicios.
4. **Los límites siguen transacciones de negocio.** No se separa lo que debe ser atómico.
5. **Se extrae un servicio cuando hay un límite real**, no por moda.

## 2. Contextos delimitados

| Contexto | Servicio | Responsabilidad |
| --- | --- | --- |
| Core banking | `core-banking-service` | Clientes, cuentas, ledger, saldos, holds, extractos. |
| Payments | `transfers-service` | Solicitud de transferencia, idempotencia, orquestación, estado, eventos. |
| Identidad | Keycloak | Autenticación, tokens, roles, clientes OIDC. |
| Experiencia cliente | `web` | Interfaz de cliente. |
| Backoffice | `admin` | Interfaz de operación y auditoría. |

## 3. `core-banking-service`

### Responsabilidades

- Mantener el cliente y sus datos mínimos.
- Abrir, congelar y cerrar cuentas.
- Registrar asientos contables inmutables.
- Calcular saldos disponible y contable.
- Gestionar holds/reservas.
- Generar extractos.
- Publicar eventos de postings.
- Exponer lecturas al cliente y una API interna para transfers.

### Agregados

| Agregado | Invariantes |
| --- | --- |
| `Customer` | Identidad única, estado válido. |
| `Account` | Moneda, estado, owner, límites. |
| `JournalEntry` | Débitos = créditos, monto > 0, postings inmutables. |
| `Hold` | No supera el saldo disponible, expira, se libera o captura. |

### Datos

- Base `core_banking`.
- Tablas principales: `customers`, `accounts`, `ledger_accounts`, `journal_entries`, `postings`,
  `account_balances`, `holds`, `outbox`, `audit_log`.

### APIs

- Públicas: `/v1/customers/me`, `/v1/accounts`, `/v1/accounts/{id}`,
  `/v1/accounts/{id}/transactions`, `/v1/accounts/{id}/statements`.
- Internas: `/internal/v1/transfers`, `/internal/v1/transfers/{idempotencyKey}`.

### Eventos

- `posting.created.v1`
- `account.status.changed.v1`

## 4. `transfers-service`

### Responsabilidades

- Recibir solicitudes de transferencia.
- Aplicar idempotencia y validaciones de canal.
- Orquestar el posting contra core banking.
- Mantener la máquina de estados de la transferencia.
- Publicar eventos de transferencia.
- Conciliar transferencias en estado `UNKNOWN`.

### Agregados

| Agregado | Invariantes |
| --- | --- |
| `Transfer` | Estado válido, monto > 0, cuentas distintas, moneda consistente. |
| `IdempotencyRecord` | Clave única por cliente, hash de request, respuesta estable. |

### Datos

- Base `transfers`.
- Tablas principales: `transfers`, `transfer_attempts`, `idempotency_records`, `outbox`,
  `audit_log`.

### APIs

- Públicas: `POST /v1/transfers`, `GET /v1/transfers/{id}`, `GET /v1/transfers`.
- No expone API interna en el MVP.

### Eventos

- `transfer.initiated.v1`
- `transfer.completed.v1`
- `transfer.failed.v1`

## 5. Matriz de propiedad de datos

| Dato | Owner | Acceso de otros servicios |
| --- | --- | --- |
| Cliente | core-banking | Solo por API o evento. |
| Cuenta | core-banking | Solo por API o evento. |
| Asiento/posting | core-banking | Solo por API o evento. |
| Saldo | core-banking | Solo por API. |
| Hold | core-banking | Solo por API interna. |
| Transferencia | transfers | Solo por API o evento. |
| Idempotencia de transferencia | transfers | Solo interna. |
| Auditoría | Cada servicio | Exportada a almacenamiento central. |

## 6. Contrato interno

`transfers-service` llama a `core-banking-service`:

```http
POST /internal/v1/transfers
Authorization: Bearer <service-token>
Idempotency-Key: <clave>
Content-Type: application/json

{
  "sourceAccountId": "uuid",
  "targetAccountId": "uuid",
  "amount": { "amountMinor": 1250, "currency": "USD" },
  "description": "Alquiler",
  "correlationId": "uuid"
}
```

Respuesta:

```json
{
  "journalEntryId": "uuid",
  "status": "POSTED",
  "completedAt": "2026-11-09T15:04:05Z"
}
```

Reglas:

- La clave de idempotencia es obligatoria.
- Si la clave ya existe con el mismo payload, devuelve `200` con el mismo resultado.
- Si la clave ya existe con otro payload, devuelve `409`.
- Si no hay saldo, devuelve `422` con `code: INSUFFICIENT_FUNDS`.
- Si la cuenta está congelada, devuelve `422` con `code: ACCOUNT_FROZEN`.
- El endpoint registra débito y crédito en un solo asiento.

## 7. Comunicación y seguridad

- Público: JWT de usuario emitido por Keycloak.
- Interno: token de cliente (client credentials) con scope `internal:transfers`.
- Los endpoints internos no se exponen por ingress.
- NetworkPolicy permite solo `transfers-service` → `core-banking-service`.
- Cada request propaga `X-Correlation-Id` y `traceparent`.
- Los errores usan `application/problem+json`.

## 8. Consistencia y fallos

| Escenario | Comportamiento |
| --- | --- |
| Core banking responde OK | Transferencia `COMPLETED`; evento en outbox. |
| Core banking rechaza por saldo | Transferencia `FAILED`; no se mueve dinero. |
| Core banking timeout | Transferencia `UNKNOWN`; conciliación con la misma clave. |
| Falla la publicación del evento | El outbox reintenta; la transferencia ya está confirmada. |
| Reintento del cliente | Misma clave → misma respuesta. |
| Clave repetida con otro payload | `409 Conflict`. |

## 9. Cuándo extraer otro servicio

Solo si se cumple al menos una condición:

- El ledger necesita escalar o desplegarse independientemente.
- Notificaciones tiene canales, proveedores y equipo propio.
- Fraude/AML requiere modelos y ciclos de vida separados.
- Un contexto tiene un límite transaccional claro y distinto.

No extraer `customers-service` solo por “microservicios”. El cliente y la cuenta participan de la
misma transacción de apertura en el MVP.

## 10. Anti-patrones prohibidos

- Compartir la base de datos entre servicios.
- Hacer joins cross-service.
- Compartir entidades JPA o modelos de dominio.
- Llamar a la base del otro servicio “solo para leer”.
- Resolver una transferencia con dos transacciones locales sin saga/compensación.
- Publicar eventos antes del commit.
- Consumir eventos sin idempotencia.
