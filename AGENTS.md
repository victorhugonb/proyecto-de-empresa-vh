# AGENTS.md — Monorepo TrackFlow

Instrucciones para agentes de IA que trabajan en este repositorio. Son obligatorias y tienen prioridad sobre las preferencias generales de estilo.

TrackFlow es una empresa de entrega de última milla y gestión de almacenes con operaciones en Estados Unidos (Los Ángeles) y España (Zaragoza). Este repositorio lo construye **TrackFlow Tech**, la unidad interna de tecnología. Cada cambio debe poder rastrearse hasta un problema operativo real descrito en [BRIEFING.md](./BRIEFING.md).

## 1. Protocolo de inicio de sesión (lectura obligatoria)

Antes de escribir o modificar cualquier archivo de código, el agente **debe leer** estos cuatro documentos, en este orden:

1. [BRIEFING.md](./BRIEFING.md): contexto de la empresa, áreas, problemas y necesidades.
2. [memory-bank/projectbrief.md](./memory-bank/projectbrief.md): objetivos, restricciones de negocio, identidad y alcance.
3. [memory-bank/techContext.md](./memory-bank/techContext.md): stack, estructura obligatoria de aplicaciones y convenciones.
4. [memory-bank/progress.md](./memory-bank/progress.md): estado actual, puntos pendientes y próximos pasos.

Si alguno de estos archivos no existe o contradice a otro, el agente se detiene y lo indica al desarrollador antes de continuar.

Si la tarea es solo de lectura o de consulta, no es necesario modificar nada, pero la lectura de los cuatro documentos sigue siendo recomendable.

## 2. Convenciones de estructura del monorepo

Las aplicaciones tienen una ubicación fija:

| Aplicación | Ruta obligatoria | Qué contiene |
| --- | --- | --- |
| Sitio público (web corporativa) | `/uis/website` | Presencia pública de TrackFlow |
| App interna (backoffice) | `/uis/backoffice` | Autenticación, personas, operaciones, comunicación interna |
| APIs y backend | `/services` | Una app FastAPI central con routers por dominio, y workers solo si deben correr aparte |

Reglas:

- **Prohibido** crear árboles paralelos de frontend o backend, por ejemplo `/frontend`, `/web`, `/app`, `/api`, `/backend` o `/admin` en la raíz, o nuevas apps dentro de `uis/` que dupliquen `website` o `backoffice`.
- Todo lo que tiene interfaz de usuario va en `uis/`. Todo lo que expone API o corre en segundo plano va en `services/`.
- Dentro de `services/` se usa una única app FastAPI central (por ejemplo `services/api/`). No crear microservicios nuevos sin aprobación expresa.
- Los dominios de los routers son: inventario, transportistas, devoluciones, atención al cliente, comercial, personas, tecnología y dirección.
- Código compartido entre `uis/` y `services/` se extrae a `packages/` (por ejemplo `@repo/shared-types`). No se duplica.
- Cada aplicación o servicio nuevo incluye su propio README con objetivo, tecnología y forma de ejecución.
- Las carpetas `data/`, `agents/`, `skills/`, `mcps/`, `workflows/`, `packages/`, `shared/`, `docs/`, `infra/`, `scripts/` e `internal/` se usan según su responsabilidad definida en [README.md](./README.md).

Reglas de negocio que el código debe respetar (según [BRIEFING.md](./BRIEFING.md) y [memory-bank/techContext.md](./memory-bank/techContext.md)):

- El inventario se modela por **SKU y por almacén** (Los Ángeles y Zaragoza). Cada almacén debe poder consultar el stock del otro.
- Los servicios de atención, seguimiento y operaciones están disponibles **24/7**.
- Los datos de los dos países deben poder compararse. Los dos marcos laborales (Estados Unidos y España) deben tratarse por separado en Personas.
- Idioma base: **español**. El inglés es soporte opcional en atención al cliente y personas.
- El informe semanal ejecutivo se genera automáticamente cada **lunes a las 7:00**.
- Los datos sensibles y de PII no se commitean en `data/raw/` sin documentar su origen y reglas de tratamiento.

## 3. Flujo obligatorio de entrega antes de cada commit

Antes de cada commit, el agente ejecuta estos cuatro pasos en orden. No se salta ninguno.

**Paso 1: Verificar restricciones de negocio y arquitectura.**
Comprobar que los cambios cumplen [BRIEFING.md](./BRIEFING.md), la estructura de la sección 2 y las reglas de `.agents/rules/`. Si `.agents/rules/` todavía no existe, aplicar las restricciones de `memory-bank/techContext.md` y registrar en el resumen que las reglas están pendientes.

**Paso 2: Ejecutar validaciones de los workspaces afectados.**
Ejecutar el linter, las pruebas y el build de cada workspace que se haya tocado: `/uis/website`, `/uis/backoffice` o `/services`.

- Usar los scripts definidos en el manifiesto de cada workspace (por ejemplo `package.json` o `pyproject.toml`). No inventar comandos.
- Ejecutar solo los comandos que apliquen al cambio. Si el cambio toca un solo workspace, validar solo ese.
- Si un workspace no define scripts de validación, indicarlo explícitamente en el resumen. No añadir tooling nuevo si la tarea no lo pide.
- Un fallo de validación bloquea el commit hasta corregirlo.

**Paso 3: Actualizar la memoria del proyecto.**
- Actualizar [memory-bank/progress.md](./memory-bank/progress.md) con el estado real: qué se completó, qué queda pendiente y qué se ha verificado.
- Si el cambio modifica la arquitectura, la estructura de carpetas o el stack, actualizar también [memory-bank/techContext.md](./memory-bank/techContext.md).
- Si el cambio toma una decisión de arquitectura, registrarla como ADR en `docs/`.

**Paso 4: Revisar `git status` y `git diff`.**
- Confirmar que no hay archivos temporales, logs, cachés, artefactos de build ni secretos en el diff.
- Confirmar que no hay cambios fuera del alcance de la tarea, en particular en los archivos protegidos de la sección 4.
- Preparar un commit semántico (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`), con un mensaje que describa el cambio de negocio o técnico.

## 4. Archivos y carpetas protegidos

Los siguientes elementos **no se modifican sin confirmación explícita del desarrollador** en la conversación actual. Si la tarea parece requerir cambiarlos, el agente se detiene y pregunta antes de editar.

| Elemento | Motivo |
| --- | --- |
| [BRIEFING.md](./BRIEFING.md) | Fuente de verdad del contexto de la empresa. |
| `.github/` (workflows de CI/CD y plantillas) | Afecta a la integración y al despliegue de todo el equipo. |
| `package.json` de la raíz | Dependencias y scripts compartidos del monorepo. |
| `docker-compose.yml` | Orquestación local de `services/` y de las bases de datos. |
| `.gitignore` | Define qué entra en el repositorio y qué no. |
| `.env*` (incluidos `.env.example` si contienen valores reales) | Variables de entorno y credenciales. Nunca se commitean secretos. |
| Carpetas de producto que no corresponden a la tarea actual | Por ejemplo, editar `uis/website` durante una tarea de `uis/backoffice`, o `services/` durante una tarea de `uis/`. |

Reglas adicionales:

- No modificar `memory-bank/` fuera de la actualización de la sección 3, paso 3, salvo que el desarrollador lo pida.
- No exponer secretos, tokens ni datos personales en código, logs, documentación ni commits.
- Si una tarea necesita tocar un elemento protegido, el agente describe el cambio propuesto y espera aprobación expresa.
