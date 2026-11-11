# Seguridad

## 1. Alcance

Este documento cubre el MVP y los controles que deben existir desde el walking skeleton. No
pretende certificar cumplimiento regulatorio real.

## 2. Modelo de amenazas (resumen STRIDE)

| Amenaza | Ejemplo | Control |
| --- | --- | --- |
| Spoofing | Robo de sesión o token | OIDC, MFA, tokens cortos, PKCE. |
| Tampering | Alterar una transferencia | Validación, firma de imágenes, auditoría. |
| Repudiation | Negar una operación | Auditoría inmutable, correlation ID. |
| Information disclosure | Ver cuentas ajenas | Autorización por recurso, cifrado, minimización. |
| Denial of service | Saturar transfers | Rate limiting, cuotas, timeouts, backpressure. |
| Elevation of privilege | Operador que aprueba sus propios ajustes | RBAC, cuatro ojos, separación de duties. |
| Supply chain | Dependencia maliciosa | SBOM, firma, scans, pinning. |
| Inyección | SQL/command injection | Validación, queries parametrizadas, SAST. |

## 3. Identidad y acceso

- Keycloak como IdP.
- OIDC con PKCE para frontends.
- Client credentials para servicio a servicio.
- JWT con `sub`, `roles`, `customer_id`, `scope`, `exp`.
- Access token corto; refresh rotativo.
- MFA para clientes (configurable) y obligatoria para operadores.
- RBAC: `customer`, `operator`, `auditor`, `service`.
- Autorización por recurso: un cliente solo accede a sus cuentas.
- Los endpoints internos no se exponen por ingress.
- NetworkPolicy restringe `transfers-service` → `core-banking-service`.

## 4. Secretos

- Nunca en Git.
- Local: `.env` no versionado.
- CI: secrets de GitHub por environment + OIDC.
- Kubernetes: External Secrets o Sealed Secrets.
- Rotación y mínimo privilegio.
- Sin credenciales de larga duración en CI.
- Sin secretos en logs, trazas ni mensajes de error.

## 5. Datos

### Clasificación

| Nivel | Ejemplos | Manejo |
| --- | --- | --- |
| Público | Documentación, endpoints | Libre. |
| Interno | IDs, configuración | Control de acceso. |
| Confidencial | Saldos, movimientos | Cifrado, autorización, auditoría. |
| Restringido | Credenciales, claves | Gestor de secretos. |

### Reglas

- Solo datos sintéticos.
- No PII real.
- Cifrado en tránsito (TLS) y en reposo.
- Minimización: no guardar lo que no se necesita.
- Retención definida.
- Logs sin PII.
- Auditoría separada de logs de aplicación.

## 6. Dinero

- Ledger de doble entrada.
- Idempotencia obligatoria.
- Límites por operación y por día.
- Validación de saldo y estado de cuenta.
- Reversos con aprobación.
- Conciliación periódica.
- Alertas por anomalías.
- No se aceptan montos negativos, cero, ni floats.

## 7. Supply chain

- Dependencias con versiones pinneadas.
- Renovate/Dependabot.
- Dependency review en PRs.
- CodeQL/Semgrep.
- Trivy/Grype para imágenes.
- SBOM.
- Cosign.
- Verificación de firma en admisión.
- Actions pinneadas por SHA.
- Registro de imágenes inmutable.

## 8. Seguridad de la aplicación

- Validación de entrada y salida.
- Errores RFC 7807 sin stack traces.
- Rate limiting por usuario/IP.
- CORS restringido.
- Headers de seguridad.
- Protección CSRF donde aplique.
- Sesiones con expiración.
- Bloqueo por intentos fallidos.
- Sanitización de logs.

## 9. Auditoría

- Registrar actor, acción, recurso, timestamp, correlation ID y resultado.
- Append-only.
- Exportación a almacenamiento inmutable en producción.
- Retención según política.
- Acceso a auditoría restringido y auditado.
- No se puede modificar desde la aplicación.

## 10. Respuesta a incidentes

1. Detectar y clasificar.
2. Contener.
3. Erradicar.
4. Recuperar.
5. Postmortem sin culpa.
6. Acciones correctivas con owner y fecha.

Runbooks mínimos:

- filtración de credenciales;
- cuenta comprometida;
- transferencia duplicada;
- ledger descuadrado;
- caída de core banking;
- imagen no firmada;
- acceso no autorizado a auditoría.

## 11. Cumplimiento (conciencia, no certificación)

- OWASP ASVS como referencia.
- PCI-DSS: fuera de alcance porque no se procesan tarjetas reales.
- KYC/AML: simulado.
- Principios de privacidad por diseño.
- Separación de duties.
- Cuatro ojos en operaciones sensibles.
- Evidencia y trazabilidad.

## 12. Checklist de PR sensible

- [ ] No hay secretos.
- [ ] No hay PII real.
- [ ] Autorización verificada.
- [ ] Validación de entrada.
- [ ] Errores no filtran datos.
- [ ] Logs sin datos sensibles.
- [ ] Tests de seguridad/abuso.
- [ ] Auditoría si aplica.
- [ ] Revisión de CODEOWNERS.
- [ ] ADR si cambia una decisión de seguridad.

## 13. Riesgos abiertos

- Floci no valida IAM real.
- kind no reproduce aislamiento de red de EKS.
- Falta definir retención de datos.
- Falta definir política de contraseñas.
- Falta definir fraude/AML.
- Falta probar restore de backups.
