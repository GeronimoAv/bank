# Trabajo multi-repositorio

## 1. Problema

En multi-repo, un cambio puede tocar contratos, productor, consumidor, frontend, GitOps e
infraestructura. Sin un orden claro, se rompe la compatibilidad o se despliega una combinación
inconsistente.

## 2. Tipos de cambio

| Tipo | Repos afectados | Estrategia |
| --- | --- | --- |
| Servicio aislado | Un servicio | PR normal, deploy independiente. |
| Contrato aditivo | `contracts` + productor/consumidor | Compatible; merge productor primero. |
| Contrato breaking | `contracts` + productor + consumidor | Nueva versión; convivencia temporal. |
| Cross-service | `core-banking-service` + `transfers-service` | Contratos primero; orden de merge. |
| Frontend | `web` + APIs | Consumir contrato estable. |
| Plataforma | `platform` + `gitops` | Probar local y staging. |
| Infraestructura | `infra` + `gitops` | Plan, aprobación, apply. |

## 3. Contratos primero

Orden para un cambio de API:

1. PR en `contracts` con el cambio **compatible**.
2. CI valida OpenAPI/breaking changes.
3. PR en el productor que implementa el contrato.
4. PR en el consumidor que usa el nuevo campo/endpoint.
5. PR en `gitops` con los nuevos digests.
6. Promoción a staging.
7. E2E y `can-i-deploy`.
8. Promoción a producción simulada.

Si es breaking:

1. Crear `/v2` o `event.v2`.
2. Productor soporta v1 y v2.
3. Consumidores migran.
4. Se mide uso de v1.
5. Se retira v1 con ADR y fecha.

## 4. Reglas de compatibilidad

- Agregar campos opcionales: compatible.
- Agregar endpoints: compatible.
- Cambiar semántica: potencialmente breaking; tratar como breaking.
- Eliminar/renombrar campos: breaking.
- Cambiar tipo o formato: breaking.
- Cambiar códigos de error: evaluar impacto; documentar.
- Eventos: solo cambios aditivos dentro de `v1`.

## 5. Feature flags

- Separan deploy de release.
- Permiten desplegar código apagado.
- Se eliminan después de estabilizar.
- Deben tener owner y fecha de limpieza.
- No se usan para ocultar código muerto para siempre.

## 6. Orden de merge

```text
contracts → productor → consumidor → frontend → gitops → staging → prod-sim
```

Excepciones:

- Si el productor aún no soporta el nuevo contrato, no se mergea el consumidor.
- Si el cambio es solo docs, no aplica.
- Si el cambio es de infraestructura, aplicar plan antes de GitOps.

## 7. Issue épica

Para cambios cross-repo:

- Un issue en `docs` o en el Project.
- Lista de PRs vinculados.
- Orden de merge.
- Plan de rollback.
- Criterios de aceptación.
- Evidencia de staging.

## 8. Releases

- Cada servicio tiene SemVer y changelog.
- No se coordinan releases gigantes.
- Se prefiere compatibilidad y despliegues independientes.
- Si hay un cambio coordinado, se documenta en el issue épica.
- Las imágenes se referencian por digest.

## 9. Rollback

- Revertir el PR de `gitops` al digest anterior.
- Si el contrato cambió, el rollback debe ser compatible.
- Migraciones expand/contract para no romper versiones anteriores.
- Verificar smoke tests.
- Registrar incidente.

## 10. Comunicación

- PR description con qué, por qué, cómo se verificó, riesgo y rollback.
- Link al issue épica.
- Estado en el Project.
- Resumen en el canal del equipo.
- Postmortem si hubo incidente.

## 11. Checklist de PR cross-repo

- [ ] Contrato actualizado y compatible.
- [ ] Tests de contrato.
- [ ] `can-i-deploy` OK.
- [ ] Orden de merge documentado.
- [ ] Feature flag si aplica.
- [ ] Plan de rollback.
- [ ] Observabilidad.
- [ ] Documentación/ADR.
- [ ] GitOps actualizado.
- [ ] Staging verificado.

## 12. Anti-patrones

- Mergear consumidor antes que productor.
- Cambiar contrato sin actualizar `contracts`.
- Desplegar `latest`.
- Releases coordinadas sin necesidad.
- Branch de integración de larga vida.
- Copiar workflows en cada repo.
- Tocar la DB de otro servicio.
- Ocultar breaking changes como “fix”.
- No documentar el orden de despliegue.
