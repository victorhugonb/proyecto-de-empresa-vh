---
name: pre-commit-qa
description: Verifica arquitectura, banco de memoria y estado del código de TrackFlow antes de cada commit. Usar cuando el usuario pida preparar, revisar o validar cambios antes de commitear o abrir un PR en el monorepo.
---

# Skill: pre-commit-qa

## 1. Objetivo

Validar, antes de cada commit del monorepo de TrackFlow, tres cosas:

1. Que los cambios respetan la arquitectura de `/uis/website`, `/uis/backoffice` y `/services`.
2. Que el banco de memoria (`memory-bank/`) refleja el estado real del trabajo.
3. Que el código del workspace afectado pasa sus validaciones.

Esta skill no implementa funcionalidades. Solo verifica y reporta.

## 2. Inputs

| Input | Fuente | Uso |
| --- | --- | --- |
| Archivos modificados | `git status` y `git diff` | Determinan qué workspaces se validan y si hay cambios fuera de alcance |
| Workspace afectado | Rutas del diff: `/uis/website`, `/uis/backoffice`, `/services`, o ninguno | Define qué scripts se ejecutan |
| Contexto activo | `memory-bank/projectbrief.md`, `memory-bank/techContext.md`, `memory-bank/progress.md` | Fija objetivos, restricciones y estado esperado |
| Reglas | [AGENTS.md](../../../AGENTS.md), [monorepo-architecture.md](../../rules/monorepo-architecture.md), [BRIEFING.md](../../../BRIEFING.md) | Criterios de cumplimiento |

Si falta cualquiera de estos inputs, la skill se detiene y lo indica. No continúa con supuestos.

## 3. Pasos de ejecución

Ejecutar en orden. Si un paso falla, registrar el fallo y continuar con los pasos que no dependan de él. El resultado final es "No Pasa" si cualquier criterio de la sección 4 falla.

### Paso 1: Leer el contexto
1. Leer [BRIEFING.md](../../../BRIEFING.md), `memory-bank/projectbrief.md`, `memory-bank/techContext.md` y `memory-bank/progress.md`.
2. Leer [AGENTS.md](../../../AGENTS.md) y [monorepo-architecture.md](../../rules/monorepo-architecture.md).

### Paso 2: Inventariar cambios
1. Ejecutar `git status` y `git diff --stat`.
2. Clasificar cada ruta modificada en: `/uis/website`, `/uis/backoffice`, `/services`, `memory-bank/`, `.agents/`, `docs/`, o **fuera de alcance**.
3. Marcar como fuera de alcance cualquier archivo protegido de [AGENTS.md](../../../AGENTS.md) (sección 4) que no tenga aprobación expresa en la conversación.

### Paso 3: Verificar arquitectura
1. Confirmar que no existen carpetas paralelas de frontend o backend: `frontend/`, `web/`, `app/`, `api/`, `backend/` o `admin/` en la raíz.
2. Confirmar que todo código de interfaz está bajo `/uis/website` o `/uis/backoffice`, y todo código de API o worker bajo `/services`.
3. Confirmar que el código compartido entre `uis/` y `services/` vive en `packages/` y no está duplicado.
4. Confirmar que cada aplicación o servicio nuevo tiene su README.

### Paso 4: Verificar reglas de negocio
Comprobar en el código y la documentación del diff:
- El inventario se modela por SKU y por almacén (Los Ángeles y Zaragoza).
- Los servicios de atención, seguimiento y operaciones no dependen de procesos manuales.
- Los datos de Estados Unidos y España pueden filtrarse por país.
- El idioma base es español.
- El informe semanal ejecutivo se programa para los lunes a las 7:00.
- No hay secretos, tokens ni valores reales en el diff, incluidos `.env*`.
- Si hay archivos nuevos en `data/raw/`, su origen y reglas de PII están documentados.

### Paso 5: Ejecutar validaciones del workspace afectado
1. Por cada workspace tocado, leer su manifiesto (`package.json` o `pyproject.toml`) y usar solo los scripts definidos ahí. No inventar comandos.
2. Ejecutar lint, pruebas y build cuando existan y apliquen al cambio.
3. Si un workspace no define scripts de validación, registrarlo en el resultado. No añadir tooling nuevo.
4. Si no hay workspace afectado (solo documentación o memoria), omitir este paso y registrarlo.

### Paso 6: Verificar memoria del proyecto
1. Comprobar que `memory-bank/progress.md` refleja lo que realmente cambió: lo completado, lo pendiente y lo verificado.
2. Si el diff cambia arquitectura, estructura de carpetas o stack, comprobar que `memory-bank/techContext.md` está actualizado.
3. Si el diff toma una decisión de arquitectura, comprobar que existe un ADR en `docs/`.

### Paso 7: Revisar higiene del diff
- Sin archivos temporales, logs, cachés, artefactos de build ni `node_modules`/`__pycache__` en el diff.
- Sin cambios fuera del alcance de la tarea.
- Mensaje de commit propuesto como semántico (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`) y con el cambio de negocio o técnico.

## 4. Output esperado y criterios de aceptación

El resultado es una tabla con un veredicto por criterio. Cada criterio es **Pasa** o **No Pasa**. No se usan otros valores.

| # | Criterio | Pasa si… |
| --- | --- | --- |
| 1 | Rutas del monorepo | Todo el código de interfaz está en `/uis/website` o `/uis/backoffice`, y todo el código de API o worker en `/services`. |
| 2 | Sin árboles paralelos | No existe ni se crea `frontend/`, `web/`, `app/`, `api/`, `backend/` o `admin/` en la raíz, ni apps en `uis/` que dupliquen `website` o `backoffice`. |
| 3 | Código compartido | Lo que usan `uis/` y `services/` está en `packages/`, sin duplicados. |
| 4 | Servicio central | Los endpoints nuevos se añaden a la app FastAPI central de `services/`, y no se crean microservicios nuevos. |
| 5 | Reglas de negocio | El diff cumple las reglas del paso 4 de esta skill (SKU por almacén, 24/7, país, español, informe del lunes a las 7:00). |
| 6 | Sin secretos | El diff no contiene credenciales, tokens ni valores reales en `.env*`. |
| 7 | Protegidos | No se ha modificado ningún elemento protegido sin aprobación expresa en la conversación. |
| 8 | Validaciones | Lint, pruebas y build de cada workspace afectado pasan, o se ha registrado que el workspace no define scripts. |
| 9 | Progreso actualizado | `memory-bank/progress.md` refleja el estado real del cambio. |
| 10 | Arquitectura documentada | Si cambió la arquitectura, `memory-bank/techContext.md` y el ADR en `docs/` están actualizados. |
| 11 | Higiene del diff | No hay archivos temporales, logs, cachés, artefactos ni cambios fuera de alcance. |
| 12 | Mensaje de commit | El mensaje propuesto es semántico y describe el cambio. |

### Veredicto final
- **Pasa:** los 12 criterios son "Pasa".
- **No Pasa:** cualquier criterio es "No Pasa". Listar cada criterio fallido, la ruta o el comando afectado, y la acción necesaria para corregirlo.

### Formato del reporte
```text
Workspaces afectados: <lista o "ninguno">
Veredicto final: Pasa | No Pasa

| # | Criterio | Resultado | Detalle |
| --- | --- | --- | --- |
| 1 | ... | Pasa / No Pasa | ... |

Acciones pendientes: <lista o "ninguna">
```
