# Project Brief — TrackFlow

Fuente: [BRIEFING.md](../BRIEFING.md) (raíz del repositorio).

## Negocio y contexto

TrackFlow es una empresa de **entrega de última milla y gestión de almacenes** fundada en 2009 en Los Ángeles (Estados Unidos). Opera en dos mercados, **Estados Unidos y España**, con almacenes en **Los Ángeles y Zaragoza**. Tiene unos **130 empleados** y una facturación anual de unos **9 millones de euros**.

Su propuesta: las marcas de e-commerce fabrican y venden bien, pero no llevan el producto a la puerta del cliente. TrackFlow almacena su inventario, prepara y empaqueta pedidos, los envía a través de una red de **8 transportistas** y gestiona las devoluciones. La operación logística completa, desde el pedido hasta la entrega o devolución, es su responsabilidad.

## Organización

- **CEO:** Thomas (fundador), con base en Los Ángeles.
- **CTO:** Andrés Kim, en la oficina tecnológica de Zaragoza, donde está la mayor parte del equipo técnico.
- **TrackFlow Tech:** unidad interna creada por el CEO para construir los sistemas, integraciones y automatizaciones inteligentes de la empresa. El equipo de IA forma parte de esta unidad.

Áreas, responsables y tamaño:

| Área | Responsable | Equipo |
| --- | --- | --- |
| Operaciones de Almacén | Ana Whitfield | ~70 operarios + 2 jefes de almacén |
| Última Milla y Gestión de Transportistas | Carlos Vega | 6 coordinadores logísticos |
| Logística Inversa | Sofía Ramos | 5 personas |
| Experiencia del Cliente | Valentina Cruz | 15 agentes (LA y Zaragoza) |
| Comercial y Relación con Clientes | Miguel Torres | 4 account managers + 4 de desarrollo de negocio |
| Personas | Rafael Sanz | 3 personas (Zaragoza) |
| Tecnología | Andrés Kim (CTO) | 7 personas (Zaragoza) |
| Dirección Ejecutiva | Thomas (CEO) | — |

## Problema que resuelve el proyecto

TrackFlow es más lenta, comete más errores y es menos rentable de lo que debería. Los problemas concretos que documenta el briefing:

- **Almacenes:** Los Ángeles y Zaragoza usan sistemas distintos (uno comercial, otro una hoja de cálculo avanzada). No hay inventario en tiempo real global, los pedidos se transcriben a mano desde correos y el picking se hace con listas en papel.
- **Última milla:** la asignación de transportista es manual, no hay datos históricos de rendimiento (entregas a tiempo, incidencias, coste por kilo).
- **Logística inversa:** las devoluciones son el 18–25 % del volumen y cada una pasa por revisión manual. La inspección del producto es subjetiva.
- **Experiencia del cliente:** consultas respondidas una a una, sin sistema de tickets ni base de conocimiento. El 80 % podría automatizarse. No hay cobertura fuera de horario.
- **Comercial:** cuentas en hojas de cálculo y correo, sin CRM. Los informes a clientes son manuales.
- **Personas:** la incorporación depende de quien la explique; los accesos tardan días; no hay indicadores por país.
- **Tecnología:** arquitectura heredada sin documentar, sin telemetría centralizada. Los fallos se detectan por WhatsApp y desplegar una funcionalidad tarda entre una y dos semanas.
- **Dirección:** el informe semanal consolidado se monta a mano cada domingo y llega con uno o dos días de antigüedad.

## Objetivos principales

Construir la infraestructura para gestionar a escala un negocio logístico en dos países:

1. **Inventario unificado:** API que devuelva el stock en tiempo real de cualquier SKU en cualquiera de los dos almacenes, y pipeline de ingesta de pedidos desde correo.
2. **Selección y seguimiento de transportistas:** motor que recomiende el transportista óptimo (destino, peso, urgencia), endpoint de seguimiento unificado y portal público de seguimiento para destinatarios.
3. **Devoluciones:** motor de aprobación automática con reglas por cliente, flujo de recogida automatizado e inspección asistida por IA (clasificación del estado del producto a partir de fotos).
4. **Atención al cliente:** agente de primera línea para consultas de seguimiento y estado de devoluciones, base de conocimiento semántica (RAG), sistema de tickets unificado y análisis de sentimiento. Soporte español + inglés (opcional, recomendado).
5. **Comercial:** integración con CRM, informes PDF automáticos, puntuación de riesgo de renovación y alertas a 90 y 30 días.
6. **Personas:** incorporación estructurada, solicitudes de acceso automatizadas, portal de RR. HH. y agente que responde políticas a partir de la documentación oficial.
7. **Tecnología:** telemetría y logging centralizados, pipeline de datos para los dashboards, monitorización con alertas y automatización de operaciones (backups, health checks).
8. **Dirección:** dashboard ejecutivo con KPIs en tiempo real de ambos países, informe semanal automático cada lunes a las 7:00, vistas comparativas por país y asistente de IA en lenguaje natural.

## Restricciones de negocio, identidad y alcance

- **Identidad:** el proyecto se desarrolla dentro de TrackFlow Tech. El documento es de uso interno para el programa 4Geeks Academy (AI Engineering Track).
- **Dos países, dos idiomas, dos marcos regulatorios:** Estados Unidos y España con sistemas distintos que deben unificarse. Personas trabaja con marcos laborales diferentes en cada país.
- **Operación 24/7:** los clientes esperan sus paquetes a partir de las 18:00. El agente de atención, el portal de seguimiento y el dashboard de operaciones deben estar siempre disponibles.
- **Datos reales de operación:** inventario por SKU, eventos de envíos, rendimiento de transportistas y clasificación de devoluciones son datos estructurados y medibles.
- **Idioma:** español como idioma base; inglés como soporte multilingüe opcional.
- **Alcance:** el proyecto cubre las áreas listadas arriba. Cada entrega se mapea a los hitos del programa (Web, Programming, Backend, Telemetry, RAG, Agents, Workflows, Real-time).

## Puntos a confirmar en BRIEFING.md

Detectados al leer el documento; no se han resuelto por cuenta propia:

- **Nombre del CEO:** aparece como "Thomas Harry" en la presentación y como "Thomas Espinoza" en la sección de Dirección Ejecutiva.
- **Transportistas:** se mencionan 8 transportistas. La lista de Última Milla incluye UPS, FedEx, DHL (EE. UU.), MRW, SEUR, DHL (España) y dos transportistas locales, que son más de 8 si DHL cuenta una vez por país.
