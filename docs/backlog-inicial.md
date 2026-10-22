# Backlog inicial

Este documento resume la dirección del backlog. El trabajo activo se registra en GitHub Issues y debe organizarse en GitHub Projects cuando el tablero esté disponible. Las propuestas documentales no se consideran terminadas hasta contar con evidencia.

La investigación de las Issues #8–#11 está resumida en [Investigación técnica inicial](investigacion-tecnica-inicial.md). El informe distingue la última evidencia documental ECM del 11 de octubre de la comprobación en vivo de solo lectura del 22 de octubre. Las Issues permanecen abiertas para revisión del propietario.

## Épicas creadas en GitHub

- [E1 — Fundamentos del producto y gestión de entornos](https://github.com/AlejoAvila/control-center/issues/1)
- [E2 — Inventario y salud de servicios](https://github.com/AlejoAvila/control-center/issues/2)
- [E3 — Diagnóstico operativo](https://github.com/AlejoAvila/control-center/issues/3)
- [E4 — Operaciones controladas](https://github.com/AlejoAvila/control-center/issues/4)
- [E5 — Backups y recuperación](https://github.com/AlejoAvila/control-center/issues/5)
- [E6 — Seguridad, identidad y auditoría](https://github.com/AlejoAvila/control-center/issues/6)
- [E7 — Actualizaciones controladas y extensibilidad](https://github.com/AlejoAvila/control-center/issues/7)

Las épicas están abiertas y no implican que el alcance esté cerrado ni que la implementación haya comenzado.

## Investigación inicial creada en GitHub

- [#8 — Validar el entorno inicial y las interfaces de administración disponibles](https://github.com/AlejoAvila/control-center/issues/8) — vinculada a E1.
- [#9 — Definir semántica de estado, salud y dato obsoleto](https://github.com/AlejoAvila/control-center/issues/9) — vinculada a E2.
- [#10 — Identificar fuentes de logs y métricas para el primer entorno](https://github.com/AlejoAvila/control-center/issues/10) — vinculada a E3.
- [#11 — Definir el contrato de integración para backup y restauración de Alfresco](https://github.com/AlejoAvila/control-center/issues/11) — vinculada a E5.
- [#12 — Preparar el backlog refinado y el primer sprint](https://github.com/AlejoAvila/control-center/issues/12) — trabajo de refinamiento.

## Desglose funcional candidato

### E1 — Fundamentos y entornos
- Registrar un entorno mediante configuración explícita.
- Mostrar el entorno activo de forma inequívoca.
- Detectar conectividad y diferenciar desconexión de caída de un servicio.

### E2 — Inventario y salud
- Mostrar los servicios presentes en el entorno.
- Diferenciar estado de runtime, salud funcional y última comprobación.
- Consultar detalles y dependencias conocidas.

### E3 — Diagnóstico operativo
- Consultar logs y aplicar filtros soportados.
- Consultar eventos e historial.
- Mostrar métricas solo cuando exista una fuente validada.

### E4 — Operaciones controladas
- Reiniciar, iniciar o detener servicios únicamente cuando la integración lo permita.
- Mostrar impacto y requerir confirmación apropiada.
- Registrar el resultado y verificar la salud posterior.

### E5 — Backups y recuperación
- Invocar un procedimiento de backup validado.
- Seguir progreso, resultado y errores.
- Verificar integridad y registrar restauración de prueba.

### E6 — Seguridad, identidad y auditoría
- Definir permisos de lectura y operación.
- Gestionar credenciales de manera segura.
- Auditar acciones de impacto sin registrar secretos.

### E7 — Actualizaciones y extensibilidad
- Mostrar versiones cuando exista una fuente fiable.
- Actualizar de forma controlada con prevalidación y recuperación.
- Incorporar nuevos adaptadores con pruebas explícitas.

## Primer sprint semanal: propuesta para revisión

**Objetivo:** demostrar una consulta reproducible de solo lectura por namespace que presenta conectividad de administración, runtime/Pod, readiness de ACS Repository y logs recientes por servicio con permisos mínimos.

Trabajo candidato:

- **#8:** definir y probar un cliente Kubernetes de lectura con Role acotado a `alfresco` para Pods, Services, EndpointSlices y workloads.
- **#9:** incorporar al contrato del adaptador la distinción entre conectividad, runtime/Pod, probe del Repository y antigüedad del dato.
- **#10:** demostrar consulta reciente de logs por servicio seleccionado, con origen, timestamps y límites de volumen/tiempo.
- **#11:** refinar y dejar listo el contrato de trabajos de backup; no automatizar backup ni ejecutar restauración en este sprint.

La demo debe mostrar inventario, estado, respuesta de probe y logs recientes de un servicio bajo identidad acotada. La investigación en vivo del 22 de octubre confirmó el clúster, once pods ECM Ready y `readyProbe: Success - Tested`; no encontró Metrics API. Métricas quedan fuera. No incluye UI ni operaciones privilegiadas; la creación del Role de prueba requiere planificar el cambio dentro del sprint. Los criterios detallados, dependencias y limitaciones están en [Investigación técnica inicial](investigacion-tecnica-inicial.md).

La duración propuesta es una semana sin fechas de calendario. Confirmar capacidad y criterios finales al planificar; no marcar trabajo como terminado por estar documentado.

## Estado verificado de GitHub Projects

El proyecto privado [Control Center](https://github.com/users/AlejoAvila/projects/5) ya existía como proyecto personal y contiene las 12 Issues #1–#12 del repositorio. No se creó un tablero duplicado. Los 12 elementos permanecen en `Backlog`; la incorporación y clasificación no implica que estén implementados ni seleccionados para un sprint.

El flujo `Status` existente coincide con `docs/gestion-agil.md`: `Backlog`, `Ready`, `In progress`, `In review`, `In validation`, `Blocked` y `Done`. También existen `Priority` (P0–P3), `Size` (XS–XL) y campos nativos de GitHub como `Parent issue` y `Milestone`. Se añadieron los campos `Tipo de trabajo` (Épica, Historia, Tarea técnica, Defecto, Investigación) y `Área` (Producto, Arquitectura, Kubernetes, Monitorización, Operaciones, Backup, Seguridad, Gestión ágil); las Issues existentes quedaron clasificadas de acuerdo con sus títulos y alcance.

No hay un campo de Iteración/Sprint configurado ni milestones. `gh` 2.45.0 no ofrece un tipo Iteration en `project field-create`; la fecha de inicio y la capacidad del primer sprint tampoco se han acordado. Definir la fecha/capacidad y luego configurar la iteración desde GitHub Projects antes de asignar trabajo. No se han creado historias de implementación porque las propuestas siguen pendientes de revisión.

## Pendientes de gestión

- Acordar fecha de inicio y capacidad del sprint; configurar entonces una Iteration y asignar solo las historias aprobadas.
- Revisar si hacen falta etiquetas adicionales; se conservaron las etiquetas existentes y no se duplicaron sus funciones con nuevas etiquetas.
- Revisar y aprobar las historias propuestas en el informe antes de crear Issues de implementación; #8–#12 siguen abiertas.
