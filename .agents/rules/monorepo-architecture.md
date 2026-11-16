# Regla: arquitectura del monorepo TrackFlow

**Alcance de aplicación:** Siempre activa (*Always On*). Aplica a todo el monorepo, en cualquier tarea, workspace o archivo.

Esta regla complementa [AGENTS.md](../../AGENTS.md). Si hay conflicto, prevalece [BRIEFING.md](../../BRIEFING.md) como fuente de verdad del negocio.

## 1. Contexto de negocio que el código debe respetar

TrackFlow es una empresa de entrega de última milla y gestión de almacenes, con operaciones en Estados Unidos (Los Ángeles) y España (Zaragoza). Cada cambio debe responder a un problema real descrito en [BRIEFING.md](../../BRIEFING.md).

- **Almacenes:** Los Ángeles y Zaragoza usan sistemas distintos. El inventario se modela por **SKU y por almacén**. Cada almacén debe poder consultar el stock del otro.
- **Última milla:** hay **8 transportistas** (UPS, FedEx, DHL, MRW, SEUR y dos locales). La asignación, el seguimiento y el rendimiento deben poder compararse entre transportistas.
- **Logística inversa:** las devoluciones son el **18–25 %** del volumen. Las reglas de aprobación son configurables **por cliente**. La inspección asistida por IA clasifica el estado del producto a partir de fotos.
- **Atención al cliente:** atiende a **marcas (B2B)** y **consumidores finales (B2C)**. Debe funcionar **24/7**.
- **Comercial:** los contratos son **anuales**. Las alertas de renovación son a **90 y 30 días**.
- **Personas:** los marcos laborales de **Estados Unidos y España** se tratan por separado.
- **Dirección:** el informe semanal ejecutivo se genera automáticamente cada **lunes a las 7:00**. Los KPIs comparan ambos países.

## 2. Idioma y localización

- **Idioma base: español.** Documentación, mensajes de interfaz y nombres de dominio de negocio van en español cuando el briefing los usa así.
- **Inglés:** soporte opcional en atención al cliente y en Personas. No sustituye al español como base.
- **Dos países:** cualquier dato de cliente, transportista o empleado debe poder filtrarse por país (`US` / `ES`).

## 3. Dónde vive cada aplicación

| Aplicación | Ruta obligatoria | Responsabilidad |
| --- | --- | --- |
| Sitio público | `/uis/website` | Presencia pública de la empresa |
| App interna (backoffice) | `/uis/backoffice` | Autenticación, personas, operaciones, comunicación interna |
| APIs y backend | `/services` | Una app FastAPI central con routers por dominio |

**Prohibido** crear árboles paralelos de frontend o backend. Esto incluye carpetas como `/frontend`, `/web`, `/app`, `/api`, `/backend` o `/admin` en la raíz, y nuevas apps dentro de `uis/` que dupliquen `website` o `backoffice`.

Dentro de `services/`:

- Una única app FastAPI central (por ejemplo `services/api/`).
- Routers por dominio: inventario, transportistas, devoluciones, atención, comercial, personas, tecnología y dirección.
- Workers separados solo cuando sea imprescindible.
- No crear microservicios nuevos sin aprobación expresa del desarrollador.

Si una funcionalidad no encaja en ninguna de las tres rutas, se consulta antes de crear una carpeta nueva.

## 4. Stack y decisiones de arquitectura

- **Backend:** FastAPI (Python) en `services/`.
- **Frontends:** aplicaciones web en `uis/`.
- **Orquestación local:** `docker-compose.yml` en la raíz. No se duplica en subcarpetas.
- **Automatización:** n8n y orquestación en `workflows/`.
- **Código compartido:** TypeScript en `packages/shared/` (`@repo/shared-types`). Si `uis/` y `services/` necesitan la misma interfaz, se extrae a `packages/`. No se duplica.
- **Bases de datos:** hoy hay dos proveedores cloud distintos. Elegir la tecnología de frontend o de base de datos nueva requiere un ADR en `docs/`, porque el briefing no la fija.

## 5. Flujo de datos

`data/raw` → `data/pipelines` → `data/process` → consumido por `services/`, `uis/` o `agents/`. `data/eval` mide la calidad de modelos y pipelines.

- No escribir en `data/raw/` sin documentar el origen del dataset.
- Los datos con PII se documentan con sus reglas de tratamiento antes de subirlos.

## 6. Restricciones no negociables

- **Disponibilidad:** los servicios de atención, seguimiento y operaciones no pueden depender de procesos manuales para funcionar.
- **Telemetría:** los logs y métricas de los dos países se envían a un único punto. No añadir un sistema de logging paralelo por aplicación.
- **Secretos:** nunca se commitean credenciales, tokens ni valores reales en `.env*`.
- **Protegidos:** `BRIEFING.md`, `.github/`, `package.json` raíz, `docker-compose.yml`, `.gitignore` y `.env*` solo se modifican con aprobación expresa del desarrollador.

## 7. Documentación

- Cada aplicación, servicio, agente o pipeline nuevo tiene su README con objetivo, tecnología y forma de ejecución.
- La documentación transversal (arquitectura, ADR, seguridad, observabilidad) va en `docs/`.
- Las decisiones de arquitectura se registran como ADR antes de implementarse.
- Los comentarios en el código solo se usan cuando aclaran algo que no es obvio.
