# Tech Context — Monorepo TrackFlow

Fuentes: [BRIEFING.md](../BRIEFING.md), [README.md](../README.md), [uis/README.md](../uis/README.md) y [services/README.md](../services/README.md).

## Stack tecnológico

| Capa | Tecnología | Fuente |
| --- | --- | --- |
| Backend / API central | **FastAPI** (Python), una única app central en `services/` | [services/README.md](../services/README.md), [README.md](../README.md) |
| Frontends | Aplicaciones web en `uis/` (sitio público y backoffice) | [uis/README.md](../uis/README.md) |
| Orquestación local | `docker-compose.yml` en la raíz | [README.md](../README.md) |
| Automatización | n8n y orquestación en `workflows/` | [README.md](../README.md) |
| Paquetes compartidos | TypeScript: `packages/shared/` → `@repo/shared-types` | [README.md](../README.md) |
| Bases de datos | Dos proveedores cloud distintos (estado heredado del briefing) | [BRIEFING.md](../BRIEFING.md) |

El briefing no fija una tecnología concreta para los frontends ni para la base de datos. Esas decisiones quedan pendientes y deben registrarse en `docs/` como ADR antes de implementarse.

## Estructura obligatoria de aplicaciones

Cada aplicación tiene una ubicación fija. No crear carpetas paralelas para estas responsabilidades.

| Aplicación | Ruta obligatoria | Descripción |
| --- | --- | --- |
| Sitio público (web corporativa) | `/uis/website` | Presencia pública de la empresa |
| App interna (backoffice) | `/uis/backoffice` | Administración interna: autenticación, personas, operaciones, comunicación interna |
| APIs y backend | `/services` | API central FastAPI con routers por dominio, más workers solo si necesitan ejecutarse aparte |

Reglas de estructura:

- Todo lo que tenga interfaz de usuario va en `uis/`. Lo que expone API o corre en segundo plano va en `services/`.
- `services/` tiene una app FastAPI central (por ejemplo `api/`) con routers por dominio: inventario, transportistas, devoluciones, atención, comercial, personas, tecnología y dirección. No dividir en microservicios al inicio.
- Un worker se extrae a un proceso separado solo cuando sea necesario.
- Cada aplicación nueva documenta su objetivo, tecnología y forma de ejecución en su README.
- Otras carpetas del monorepo (`data/`, `agents/`, `skills/`, `mcps/`, `workflows/`, `packages/`, `shared/`, `docs/`, `infra/`, `scripts/`, `internal/`) se usan según la tabla de responsabilidades del [README.md](../README.md).

## Flujo de datos

`data/raw` → `data/pipelines` → `data/process` → consumido por `services/`, `uis/` o `agents/`. `data/eval` mide la calidad de los modelos y pipelines.

## Restricciones técnicas

- **Servicios 24/7:** el agente de atención, el portal de seguimiento y el dashboard de operaciones deben estar siempre disponibles.
- **Telemetría centralizada:** logs y métricas de los dos países en un único punto. Hoy no existe.
- **Dos países:** los datos deben poder compararse entre Estados Unidos y España. Los almacenes de Los Ángeles y Zaragoza deben poder consultar el inventario del otro.
- **Idiomas:** español como base; inglés como soporte opcional en atención al cliente y personas.
- **Tiempo de despliegue:** el objetivo implícito del briefing es reducir el despliegue de funcionalidades nuevas, hoy de una a dos semanas.
- **Informe semanal:** generado automáticamente cada lunes a las 7:00.
- **Dato de SKU:** el inventario se modela a nivel de SKU por almacén.
- **Datos sensibles:** `data/raw/` debe documentar el origen de cada dataset y sus reglas de PII, según [README.md](../README.md).

## Convenciones del repositorio

- **Una responsabilidad por carpeta de primer nivel.** Si tiene interfaz va en `uis/`; si expone API o corre en background, en `services/`.
- **Un README por carpeta** antes de programar en ella, y documentar cada nueva aplicación, servicio, agente o pipeline en su propia subcarpeta con README.
- **Contexto de empresa único:** el documento de referencia es `BRIEFING.md` en la raíz. El README del template indica reemplazar `CONTEXT.md` por el briefing de la empresa asignada; el resto de agentes y prompts deben usar ese mismo dominio.
- **Documentación cross-cutting** (arquitectura, ADR, seguridad, observabilidad) en `docs/`.
- **Sin `AGENTS.md` raíz todavía.** Está previsto en los próximos pasos (ver [progress.md](./progress.md)).
- **Comentarios en el código** solo cuando aporten una aclaración necesaria.
