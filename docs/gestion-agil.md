# Gestión ágil del proyecto

## Principios

- Mantener el proceso lo bastante ligero para un producto desarrollado con asistencia de Codex.
- GitHub Issues es el registro del trabajo individual; GitHub Projects es la vista para priorizar y planificar.
- Los documentos versionados contienen decisiones, contexto y convenciones duraderas, no copias completas del estado de todos los issues.
- El backlog se ordena por valor, riesgo, dependencias y capacidad de validar resultados.
- Cada tarea debe tener un resultado verificable.

## Elementos de trabajo

- **Épica:** capacidad amplia que agrupa historias relacionadas. Mantener un issue principal o una convención equivalente y enlazar historias.
- **Historia de usuario:** necesidad y valor para un rol concreto, con criterios de aceptación comprobables.
- **Tarea técnica:** trabajo necesario para cumplir una historia o reducir un riesgo técnico.
- **Defecto:** desviación reproducible respecto de un comportamiento esperado.
- **Investigación:** trabajo de descubrimiento cuyo resultado debe ser evidencia y una recomendación, no necesariamente código.
- **Sprint:** período con objetivo y conjunto de historias seleccionadas; la duración se acuerda según la capacidad real.

## Plantilla recomendada para historias

- Contexto y problema.
- Historia: “Como [rol], quiero [capacidad], para [beneficio]”.
- Criterios de aceptación en lenguaje comprobable.
- Fuera de alcance.
- Dependencias y riesgos.
- Evidencia de validación esperada.
- Estimación cuando haya suficiente información.

## Flujo sugerido

`Backlog → Listo → En curso → En revisión → En validación → Terminado`

Puede añadirse `Bloqueado` para problemas que impidan avanzar. Mantener los estados de GitHub Project coherentes con esta convención y evitar duplicar estados en etiquetas.

## Criterios para estar listo

Una historia está lista para un sprint cuando el resultado esperado se comprende, tiene criterios de aceptación, las dependencias críticas son conocidas y su tamaño permite completarla razonablemente. Si aún no puede estimarse, crear una investigación acotada.

## Criterios de terminado

- Criterios de aceptación satisfechos.
- Pruebas apropiadas ejecutadas y resultado registrado.
- Seguridad y errores revisados según el alcance.
- Documentación actualizada si cambió el comportamiento o una decisión.
- Cambios revisados mediante el flujo Git acordado.
- Issue actualizado con evidencia y estado real.

## Sprints y ceremonias

Por defecto, probar un sprint corto (por ejemplo, una o dos semanas) cuando haya suficiente trabajo refinado. Elegir la duración real al planificar el primero; no inventar fechas.

Cada sprint debe tener objetivo, historias, entregables, dependencias y revisión. Al cierre, anotar qué se completó, qué se trasladó y qué mejora de proceso conviene aplicar.

## Títulos, labels y relaciones

- Prefijos sugeridos para títulos de épicas: `Épica: ...`.
- Prefijos opcionales para historias: `HU: ...`; para tareas `Tarea: ...`; para defectos `Bug: ...`; para investigación `Spike: ...`.
- Labels iniciales candidatas: `tipo:épica`, `tipo:historia`, `tipo:tarea`, `tipo:bug`, `tipo:investigación`; `prioridad:P0`, `prioridad:P1`, `prioridad:P2`; y áreas como `área:arquitectura`, `área:monitorización`, `área:operaciones`, `área:backup`, `área:seguridad`.
- Antes de crear labels, inspeccionar las existentes y reutilizarlas donde coincidan.
- Enlazar historias y tareas con su épica, y pull requests con los issues que resuelven.

## GitHub Projects

Configurar un proyecto para este repositorio si las funciones y permisos disponibles lo permiten. Campos candidatos: Estado, Prioridad, Tipo, Sprint/Iteración, Épica o agrupación, y Área. Preferir campos nativos y automatizaciones simples. Verificar cualquier capacidad de Projects antes de depender de ella.

Si GitHub Projects no puede configurarse desde las herramientas disponibles, documentar los pasos manuales pendientes; no afirmar que el tablero existe hasta verificarlo.
