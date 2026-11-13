# Visión — Vela Bank

## 1. Propósito

Vela Bank es un **banco minorista simulado** construido como proyecto de ingeniería y
aprendizaje. El objetivo es reproducir, con la mayor fidelidad posible, el flujo completo de un
banco: clientes, cuentas, contabilidad, transferencias, backoffice, seguridad, auditoría,
despliegue y operación.

No es una entidad financiera regulada. No opera con dinero real ni con datos personales reales.

## 2. Problema que resuelve

Aprender a construir y operar un sistema financiero requiere más que un CRUD. Requiere:

- movimiento de dinero correcto y auditable;
- identidad y autorización robustas;
- separación de responsabilidades;
- trazabilidad de cada operación;
- despliegues reproducibles y reversibles;
- observabilidad y respuesta a incidentes;
- controles de seguridad y cumplimiento;
- trabajo multi-repositorio y multi-equipo.

Vela Bank existe para practicar todo eso en un entorno seguro, local y con datos sintéticos.

## 3. Usuarios objetivo

| Usuario | Necesidad |
| --- | --- |
| Cliente retail | Registrarse, abrir una cuenta, consultar saldo, transferir y ver movimientos. |
| Operador de backoffice | Revisar clientes, congelar cuentas, auditar operaciones y resolver excepciones. |
| Ingeniero/a de plataforma | Construir, desplegar, observar y recuperar el sistema. |
| Auditor/a | Reconstruir qué pasó, quién lo hizo y con qué evidencia. |

## 4. Principios de producto

1. **El ledger es la fuente de verdad.** Los saldos se derivan de asientos contables, no al revés.
2. **Nada se borra.** Los errores se corrigen con reversos, no con updates destructivos.
3. **Idempotencia por defecto.** Un reintento no puede mover dinero dos veces.
4. **Auditoría desde el día uno.** Toda operación sensible deja rastro.
5. **Seguridad y privacidad por diseño.** Mínimo privilegio, datos sintéticos, sin secretos en el repo.
6. **Despliegue reproducible.** La misma imagen se promueve entre entornos por digest.
7. **Documentación primero.** Las decisiones se registran antes de implementarse.
8. **Complejidad justificada.** Primero un monolito modular por servicio; microservicios solo cuando
   el límite de negocio lo justifique.

## 5. Visión técnica

```text
Cliente web
    │
    ▼
Ingress / API edge
    │
    ├── core-banking-service ── PostgreSQL (core_banking)
    │        │
    │        └── eventos ── SQS/SNS (Floci en local)
    │
    └── transfers-service ──── PostgreSQL (transfers)
             │
             └── outbox ───── eventos
```

- Dos microservicios de negocio: `core-banking-service` y `transfers-service`.
- Frontends: app cliente y backoffice.
- Plataforma: Keycloak, PostgreSQL, Redis, SQS/SNS, S3, Kubernetes, Argo CD, observabilidad.
- Nube local: Floci para servicios AWS; kind/k3d para Kubernetes.
- Nube real: AWS sandbox para staging/producción simulada.

## 6. Métricas de éxito

### Producto

- Un cliente puede completar el flujo registro → cuenta → depósito → transferencia → extracto.
- El backoffice puede auditar y revertir una operación con evidencia.
- El 100% de las transferencias exitosas tiene asiento contable balanceado.

### Ingeniería

- `main` siempre verde y desplegable.
- Pipeline reproducible de PR a staging.
- Promoción a producción simulada con aprobación y registro.
- Cobertura de tests en caminos críticos de dinero.
- SLOs definidos y medidos.
- Restore de backup probado.

### Aprendizaje

- Cada decisión relevante tiene un ADR.
- Cada incidente simulado tiene runbook y postmortem.
- La organización de repos refleja ownership y ciclos de vida reales.

## 7. No objetivos

- Operar como banco real o procesar dinero real.
- Emitir tarjetas reales o conectarse a redes de pago reales.
- Cumplir certificaciones reales (PCI-DSS, SOC 2, ISO 27001) en esta etapa.
- Soportar multi-moneda, FX o productos de inversión en el MVP.
- Tener una app móvil nativa.
- Competir por rendimiento con un core bancario comercial.

## 8. Restricciones

- Datos sintéticos únicamente.
- Sin secretos en el repositorio.
- Sin credenciales reales de nube en local.
- Presupuesto de nube acotado; usar Floci y kind para la mayor parte del desarrollo.
- El proyecto debe poder levantarse en una máquina de desarrollo razonable.

## 9. Decisiones fundacionales

Las decisiones de organización, producto, stack y plataforma están cerradas en los ADRs
[0008](adr/0008-repo-governance-and-naming.md), [0009](adr/0009-product-currency-and-simulation.md),
[0010](adr/0010-application-stack.md) y [0011](adr/0011-platform-and-operations.md).

Las nuevas decisiones abiertas se registran en [decisions-pending.md](decisions-pending.md).
