# Progress — TrackFlow Agent Memory Bank

## Estado actual

Configuración inicial completada:

- [x] **Fork del monorepo:** el repositorio `victorhugonb/proyecto-de-empresa-vh` es un fork del template del programa 4Geeks Academy (AI Engineering Track).
- [x] **Rama de trabajo:** `feature/agent-memory-bank`, activa en el repositorio.
- [x] **BRIEFING.md integrado en la raíz:** [BRIEFING.md](../BRIEFING.md) contiene el contexto de la empresa TrackFlow y sustituye al placeholder `CONTEXT.md` del template.
- [x] **Banco de memoria inicializado:** carpeta `memory-bank/` con:
  - [projectbrief.md](./projectbrief.md): negocio, objetivos, restricciones y alcance.
  - [techContext.md](./techContext.md): stack, estructura obligatoria de aplicaciones y convenciones.
  - [progress.md](./progress.md): este archivo.

## Pendiente de confirmar

- Nombre del CEO (Thomas Harry o Thomas Espinoza) y lista de transportistas en [BRIEFING.md](../BRIEFING.md). Ver detalle en [projectbrief.md](./projectbrief.md#puntos-a-confirmar-en-briefingmd).
- Stack concreto de los frontends (`uis/website`, `uis/backoffice`) y de la base de datos. No está definido en el briefing.

## Próximos pasos

1. **Crear `AGENTS.md` en la raíz** con las instrucciones generales para agentes sobre el monorepo, apoyándose en `memory-bank/`.
2. **Crear reglas en `.agents/rules/`** con las convenciones de estructura (`/uis/website`, `/uis/backoffice`, `/services`) y de documentación por carpeta.
3. **Crear una skill interna en `.agents/skills/`** que guíe a los agentes en el uso del banco de memoria.
