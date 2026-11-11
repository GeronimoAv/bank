# Glosario

Términos usados en el proyecto. Si un término no está acá y es relevante, se agrega.

## Banca y dominio

| Término | Definición |
| --- | --- |
| **Asiento contable** | Registro inmutable de una operación con al menos dos postings que se compensan. |
| **Posting** | Línea de un asiento: cuenta, dirección (débito/crédito) y monto. |
| **Ledger** | Libro de asientos contables; fuente de verdad de los saldos. |
| **Doble entrada** | Todo asiento tiene débitos y créditos por el mismo monto total. |
| **Hold / reserva** | Bloqueo temporal de fondos disponible para una operación futura. |
| **Reverso** | Asiento compensatorio que anula el efecto de otro sin borrarlo. |
| **Conciliación** | Verificación de que postings, saldos y extractos coinciden. |
| **KYC** | Know Your Customer; verificación de identidad del cliente. |
| **AML** | Anti-money laundering; controles contra lavado de dinero. |
| **PCI-DSS** | Estándar de seguridad para datos de tarjetas. Fuera del alcance del MVP. |
| **Cuenta interna** | Cuenta contable del banco usada como contrapartida (cash, suspense). |

## Ingeniería

| Término | Definición |
| --- | --- |
| **Idempotencia** | Propiedad por la cual repetir una operación produce el mismo efecto una sola vez. |
| **Outbox** | Tabla transaccional donde se guardan eventos para publicarlos después, sin perderlos. |
| **Saga** | Secuencia de pasos distribuidos con compensaciones ante fallos. |
| **Exactly-once** | Garantía de procesamiento único; en la práctica se logra con idempotencia. |
| **At-least-once** | Garantía de entrega al menos una vez; requiere consumidores idempotentes. |
| **Contrato** | Especificación de API o evento que productores y consumidores acuerdan. |
| **Contract testing** | Pruebas que verifican que productor y consumidor respetan el contrato. |
| **Expand/contract** | Técnica de migración compatible: agregar lo nuevo, migrar, retirar lo viejo. |
| **GitOps** | Despliegue declarativo desde Git, reconciliado por una herramienta como Argo CD. |
| **IaC** | Infraestructura como código. |
| **SLO** | Service Level Objective; objetivo medible de nivel de servicio. |
| **SLI** | Service Level Indicator; métrica que mide el SLO. |
| **RTO** | Recovery Time Objective; tiempo máximo de recuperación. |
| **RPO** | Recovery Point Objective; pérdida máxima de datos aceptable. |
| **SBOM** | Software Bill of Materials; inventario de componentes de un artefacto. |
| **Supply chain** | Cadena de dependencias, builds, artefactos y firmas. |
| **Floci** | Emulador local de servicios de nube (AWS, Azure, GCP, OCI). |
| **kind** | Kubernetes in Docker; clúster local para pruebas. |
| **Argo CD** | Herramienta de GitOps para sincronizar Kubernetes con Git. |
| **Keycloak** | Servidor de identidad OIDC/OAuth2. |
| **OpenAPI** | Especificación estándar para describir APIs HTTP. |
| **AsyncAPI** | Especificación para describir APIs asíncronas y eventos. |

## Proceso

| Término | Definición |
| --- | --- |
| **ADR** | Architecture Decision Record; registro de una decisión de arquitectura. |
| **Slice** | Unidad pequeña de trabajo con valor y criterios de aceptación. |
| **Walking skeleton** | Implementación mínima de punta a punta que valida la arquitectura y el pipeline. |
| **Definition of Done** | Criterios que debe cumplir un cambio para considerarse terminado. |
| **Code owner** | Persona o equipo responsable de revisar cambios en un área. |
| **Ruleset** | Regla de protección de ramas o repositorios en GitHub. |
| **Environment** | Entorno de despliegue con reglas y secretos propios. |
