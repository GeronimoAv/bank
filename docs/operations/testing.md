# Estrategia de testing

## 1. Objetivo

Los tests son la especificación del comportamiento. En un sistema que mueve dinero, los caminos
críticos deben estar cubiertos por pruebas automatizadas y verificación humana.

## 2. Pirámide

```text
        E2E / UI
      Contract tests
    Integration tests
  Unit / domain tests
```

No obsesionarse con la forma: lo importante es cubrir riesgo. Un test de ledger vale más que diez
tests de getters.

## 3. Tipos de test

| Tipo | Herramienta | Qué cubre |
| --- | --- | --- |
| Unitarios | JUnit 5 / Vitest | Reglas de dominio, cálculos, validaciones. |
| Property-based | jqwik / fast-check | Invariantes del ledger y montos. |
| Integración | Testcontainers + Floci | DB, colas, S3, Keycloak. |
| Contrato | Pact + `oasdiff` | Productor/consumidor y OpenAPI. |
| E2E | Playwright | Flujo cliente → transferencia → extracto. |
| Carga | k6 | Latencia y saturación. |
| Seguridad | ZAP + scans | OWASP Top 10 y dependencias. |
| Arquitectura | ArchUnit | Límites entre módulos. |
| Migraciones | Flyway + Testcontainers | Upgrade/downgrade seguro. |

## 4. Tests de dominio críticos

### Ledger

- [ ] Todo asiento balancea.
- [ ] No se permiten montos ≤ 0.
- [ ] No se modifican asientos `POSTED`.
- [ ] El reverso restaura el saldo.
- [ ] El hold reduce disponible, no contable.
- [ ] La suma de postings coincide con el saldo.
- [ ] No se permite sobregiro no configurado.
- [ ] El orden de locks evita deadlocks.

### Transferencias

- [ ] Transferencia exitosa.
- [ ] Saldo insuficiente.
- [ ] Cuenta congelada.
- [ ] Misma cuenta origen/destino.
- [ ] Monedas distintas.
- [ ] Idempotencia con misma clave/payload.
- [ ] Conflicto con misma clave/otro payload.
- [ ] Timeout con posting exitoso.
- [ ] Timeout sin posting.
- [ ] Conciliación de `UNKNOWN`.
- [ ] Falla de outbox y reintento.

### Seguridad

- [ ] Cliente no accede a cuentas ajenas.
- [ ] Operador no aprueba su propio ajuste.
- [ ] Token expirado rechazado.
- [ ] Scope interno requerido.
- [ ] Endpoint interno no expuesto.
- [ ] Rate limit activo.
- [ ] Errores no filtran datos.

## 5. Contract testing

1. El consumidor define la interacción esperada (Pact).
2. El productor verifica el contrato en su CI.
3. El broker publica el resultado.
4. `can-i-deploy` bloquea promociones incompatibles.
5. `oasdiff` detecta breaking changes en OpenAPI.
6. Los eventos se validan contra JSON Schema/AsyncAPI.

## 6. Entornos de test

| Entorno | Qué corre | Datos |
| --- | --- | --- |
| Local | Unit + integration + E2E | Sintéticos. |
| CI (PR) | Unit + integration + contract + SAST | Efímeros. |
| CI (main) | Todo + imagen + scan | Efímeros. |
| Staging | E2E + smoke + carga ligera | Sintéticos. |
| Prod-sim | Smoke + canary | Sintéticos. |

## 7. Gates

### PR

- Unit tests.
- Integration tests.
- Contract tests.
- Lint/format.
- SAST.
- Dependency scan.
- Secret scan.
- OpenAPI breaking change check.

### Main

- Todo lo anterior.
- Build de imagen.
- SBOM.
- Image scan.
- Firma.
- Publicación de chart.

### Promoción

- Staging verde.
- Smoke tests.
- E2E.
- `can-i-deploy`.
- Aprobación manual.

## 8. Datos de test

- Solo sintéticos.
- Factories determinísticas.
- Sin PII real.
- Seed reproducible.
- Limpieza entre tests.
- No depender del orden de ejecución.

## 9. Flaky tests

- Un test flaky se marca, se investiga y se corrige.
- No se ignora silenciosamente.
- No se reintenta infinitamente para “pasar”.
- Si bloquea el pipeline, se prioriza.

## 10. Cobertura

No se persigue un porcentaje ciego. Se exige cobertura de:

- reglas de dinero;
- idempotencia;
- autorización;
- conciliación;
- migraciones;
- caminos de error.

## 11. Definition of Done de testing

- [ ] Tests nuevos para el comportamiento.
- [ ] Tests de regresión si hubo bug.
- [ ] Contract test si cambió un contrato.
- [ ] E2E si cambió el flujo.
- [ ] Migración probada.
- [ ] CI verde.
- [ ] Evidencia en el PR.
