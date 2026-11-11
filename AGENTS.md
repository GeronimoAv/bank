# AGENTS.md — Protocolo de trabajo para agentes de IA

Este documento define cómo debe comportarse cualquier agente de IA que trabaje en este
repositorio o en los repositorios de la organización. Su objetivo es que el agente **no tenga
rienda suelta**: primero propone, después ejecuta y al final rinde cuentas.

Si una instrucción del usuario contradice este documento, el agente debe detenerse, señalar el
conflicto y pedir una decisión explícita. El humano siempre es el responsable del cambio.

---

## 1. Principios no negociables

1. **Plan primero.** No se edita ningún archivo ni se ejecuta ningún comando sin haber anunciado
   antes el objetivo y el plan.
2. **El plan es una propuesta.** El usuario puede cambiarlo, recortarlo o rechazarlo. El agente no
   defiende el plan: lo ajusta.
3. **Ejecución visible.** Durante el trabajo, el agente dice qué está haciendo, qué archivo está
   tocando y qué comando está lanzando.
4. **Cambios pequeños.** Un cambio = un propósito. Si el plan crece, se divide.
5. **Cierre con resumen.** Al terminar, el agente resume cambios, comandos, decisiones, riesgos y
   próximos pasos.
6. **El humano decide.** Las decisiones de producto, arquitectura, seguridad, dinero, datos,
   credenciales y despliegues requieren aprobación humana.
7. **Sin secretos ni PII real.** Nunca leer, escribir, commitear ni registrar credenciales, tokens,
   claves, datos personales reales o dinero real.
8. **Sin atribución de IA.** No agregar trailers de IA a los commits. El autor humano es el
   accountable.

---

## 2. Flujo obligatorio

El agente trabaja en cinco fases. No puede saltarse la fase de plan.

### Fase 1 — Contexto

Antes de proponer nada, el agente debe:

- leer `README.md`, `CONTRIBUTING.md`, este `AGENTS.md`;
- leer los documentos relevantes de `docs/` y los ADRs relacionados;
- revisar el estado del repo (`git status`, rama actual);
- identificar supuestos, dependencias y decisiones pendientes.

Si falta contexto crítico, lo dice antes de inventar.

### Fase 2 — Plan

El agente presenta un plan con esta estructura:

```markdown
## Plan propuesto

**Objetivo:** ...
**Alcance:** ...
**Fuera de alcance:** ...
**Supuestos:** ...
**Decisiones que necesito:** ...
**Archivos que voy a tocar:** ...
**Comandos que voy a ejecutar:** ...
**Riesgos:** ...
**Criterios de aceptación:** ...
**Pasos:**
1. ...
2. ...
3. ...
```

Reglas del plan:

- Debe ser concreto: nombres de archivos, comandos, resultados esperados.
- Debe indicar qué partes son propuestas y qué partes ya están decididas.
- Si hay una decisión abierta, debe ofrecer opciones con recomendación.
- Si el usuario pidió solo un análisis, el agente **no ejecuta**: termina en el plan.
- Si el usuario pidió ejecutar, el agente puede avanzar después de presentar el plan, pero debe
  detenerse en los puntos de aprobación de la sección 5.

### Fase 3 — Ejecución

Durante la ejecución, el agente:

- anuncia cada paso antes de hacerlo;
- informa qué archivo crea, edita o elimina;
- informa el comando exacto que va a ejecutar y por qué;
- reporta el resultado del comando, incluyendo errores y códigos de salida relevantes;
- mantiene el plan actualizado con checkboxes;
- si el plan cambia, se detiene, explica el cambio y pide aprobación.

Formato recomendado:

```text
Paso 2/6 — Crear el ADR de límites de servicios.
Voy a crear: docs/adr/0002-service-boundaries.md
Motivo: dejar registrada la decisión antes de escribir contratos.
```

Después de la acción:

```text
Hecho: docs/adr/0002-service-boundaries.md
Resultado: archivo creado, 84 líneas.
```

Para comandos:

```text
Voy a ejecutar: git status --short --branch
Motivo: confirmar que no hay cambios previos antes de editar.
```

```text
Resultado: rama docs/project-foundation, sin cambios pendientes.
```

### Fase 4 — Verificación

Antes de cerrar, el agente debe verificar lo que hizo:

- revisar el diff o los archivos creados;
- correr el linter, tests o validaciones disponibles;
- comprobar que no se tocaron archivos fuera del plan;
- comprobar que no hay secretos ni PII;
- comprobar que la documentación quedó enlazada desde `docs/README.md` si corresponde.

Si una validación falla, el agente lo informa y propone el siguiente paso. No oculta fallos.

### Fase 5 — Resumen

El cierre debe incluir:

```markdown
## Resumen

**Qué se hizo:** ...
**Archivos creados:** ...
**Archivos modificados:** ...
**Comandos ejecutados y resultado:** ...
**Decisiones tomadas:** ... (con enlace al ADR)
**Decisiones pendientes:** ... (con enlace a docs/decisions-pending.md)
**Riesgos o limitaciones:** ...
**Verificación:** ...
**Próximo paso propuesto:** ...
```

El resumen debe ser honesto: si algo quedó incompleto, se dice.

---

## 3. Plantillas

### 3.1 Plantilla de plan

```markdown
## Plan propuesto — <título>

**Objetivo:** <resultado observable>
**Alcance:** <qué incluye>
**Fuera de alcance:** <qué no incluye>
**Supuestos:** <qué estoy asumiendo>
**Decisiones que necesito:** <preguntas concretas>

**Archivos que voy a tocar:**
- `ruta/archivo`: <motivo>

**Comandos que voy a ejecutar:**
- `<comando>`: <motivo y resultado esperado>

**Riesgos:** <qué puede salir mal>
**Criterios de aceptación:**
- [ ] ...
- [ ] ...

**Pasos:**
1. [ ] ...
2. [ ] ...
```

### 3.2 Plantilla de progreso

```text
Paso <n>/<total> — <descripción>
Voy a: <acción>
Archivo/comando: <ruta o comando>
Motivo: <por qué>
```

### 3.3 Plantilla de resumen

```markdown
## Resumen

**Qué se hizo:** ...
**Archivos creados:** ...
**Archivos modificados:** ...
**Comandos ejecutados y resultado:** ...
**Decisiones tomadas:** ...
**Decisiones pendientes:** ...
**Riesgos o limitaciones:** ...
**Verificación:** ...
**Próximo paso propuesto:** ...
```

---

## 4. Disciplina de archivos y comandos

### Archivos

- Antes de editar: indicar ruta exacta y motivo.
- Después de editar: indicar qué cambió y cuántas líneas aproximadas.
- No editar archivos fuera del plan sin anunciarlo.
- No borrar archivos sin aprobación explícita.
- No sobrescribir trabajo del usuario sin haberlo leído.
- No reformatear archivos completos si el cambio es puntual.

### Comandos

- Antes de ejecutar: mostrar el comando exacto y el motivo.
- Después de ejecutar: mostrar resultado, errores y código de salida.
- No ejecutar comandos destructivos sin aprobación explícita:
  - `rm -rf`, `git reset --hard`, `git clean -fd`, `git push --force`;
  - `docker system prune`, borrado de volúmenes, `kubectl delete` sobre entornos compartidos;
  - `terraform destroy`, `terraform apply` sin plan revisado;
  - migraciones destructivas o `DROP` de bases de datos.
- No ejecutar `git commit`, `git push`, apertura de PR, despliegues ni `terraform apply` si el
  usuario no lo pidió explícitamente.
- No ejecutar comandos que consuman credenciales o generen costos sin aprobación.

---

## 5. Cuándo detenerse y pedir aprobación

El agente debe detenerse antes de continuar cuando:

1. Hay una decisión de arquitectura, producto, seguridad o dinero sin resolver.
2. El cambio afecta producción, datos reales o credenciales.
3. El plan original ya no alcanza y hay que ampliar el alcance.
4. Se necesita un comando destructivo o irreversible.
5. Faltan permisos, accesos o información.
6. Hay instrucciones contradictorias entre el usuario, `CONTRIBUTING.md`, este archivo o los ADRs.
7. Un test o validación falla dos veces seguidas por la misma causa.
8. Se detecta un secreto, PII real o una vulnerabilidad.
9. El agente no puede explicar el impacto del cambio.

En esos casos, el agente no improvisa: describe la situación, las opciones y la recomendación.

---

## 6. Decisiones y documentación

- Toda decisión durable se registra en un ADR dentro de `docs/adr/`.
- Toda decisión abierta se registra en `docs/decisions-pending.md`.
- Si el agente toma una decisión menor durante la ejecución, la informa en el progreso y la deja
  en el resumen.
- Si una decisión cambia un ADR existente, se crea un nuevo ADR que supersede al anterior; no se
  reescribe la historia.
- Si se agrega un documento nuevo, se enlaza desde `docs/README.md`.
- Si cambia un contrato, se actualizan `docs/contracts/` y el ADR correspondiente.
- Si cambia el mapa de repositorios, se actualiza `docs/architecture/repo-map.md`.

---

## 7. Reglas específicas de este repositorio

- La documentación vive en `docs/`.
- Los ADRs viven en `docs/adr/`.
- Los contratos viven en `docs/contracts/`.
- El plan de trabajo vivo vive en `docs/action-plan.md`.
- El roadmap vive en `docs/roadmap.md`.
- El estado de decisiones abiertas vive en `docs/decisions-pending.md`.
- Las reglas de contribución humanas viven en `CONTRIBUTING.md`.
- Este archivo complementa a `CONTRIBUTING.md`; no lo reemplaza.
- El repositorio está en **fase de documentación**. No hay código ejecutable todavía.
- No se deben crear servicios, repositorios ni infraestructura sin actualizar primero el mapa de
  repositorios y el plan de acción.
- Los documentos marcados como `[PROPUESTO]` no son decisiones cerradas.

---

## 8. Seguridad

- No leer ni escribir `.env`, secretos, tokens, claves privadas o credenciales.
- No pegar secretos en documentos, issues, PRs, commits ni logs.
- No usar datos personales reales. Solo datos sintéticos.
- No ejecutar código de origen desconocido.
- No agregar dependencias sin justificar y revisar licencia/seguridad.
- No desactivar controles de seguridad para “hacer que funcione”.
- Reportar vulnerabilidades de forma privada, no en un issue público.

---

## 9. Definition of Done para un cambio asistido por IA

Un cambio está terminado cuando:

- [ ] El plan fue anunciado y el alcance respetado.
- [ ] Los archivos tocados coinciden con el plan, o el cambio fue anunciado.
- [ ] La documentación relevante está actualizada.
- [ ] Las decisiones durables están en un ADR.
- [ ] Las decisiones abiertas están en `docs/decisions-pending.md`.
- [ ] Las validaciones disponibles pasan o el fallo está reportado.
- [ ] No hay secretos ni PII real.
- [ ] El resumen final incluye cambios, comandos, decisiones y próximos pasos.
- [ ] El humano revisó y aprobó el cambio.

---

## 10. Relación con otros documentos

- `CONTRIBUTING.md`: flujo humano de ramas, commits, PRs, releases y seguridad.
- `docs/vision.md`: propósito y principios del producto.
- `docs/scope.md`: alcance del MVP.
- `docs/action-plan.md`: plan de trabajo vivo.
- `docs/roadmap.md`: fases y criterios de salida.
- `docs/architecture/`: arquitectura, repositorios, límites y flujos.
- `docs/contracts/`: OpenAPI y eventos.
- `docs/adr/`: decisiones de arquitectura.
- `docs/operations/`: plataforma local, CI/CD, seguridad y observabilidad.
- `docs/governance/`: organización, repositorios y trabajo multi-repo.
