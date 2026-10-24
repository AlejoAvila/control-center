# Backlog inicial

Este documento resume la dirección del backlog. El trabajo activo se registra en GitHub Issues y debe organizarse en GitHub Projects cuando el tablero esté disponible. Las propuestas documentales no se consideran terminadas hasta contar con evidencia.

La investigación de las Issues #8–#11 y la propuesta revisada para #12 están en [Investigación técnica inicial](investigacion-tecnica-inicial.md). El informe conserva la evidencia histórica de `acs-local` y la distingue de la documentación y comprobación de solo lectura del laboratorio vigente `acs-independent` (24 de octubre). Las Issues permanecen abiertas para revisión del propietario.

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

**Objetivo:** demostrar diagnóstico de solo lectura del stack ACS en `kind-acs-independent`, resolviendo componentes distribuidos entre releases Helm y ECK hacia sus workloads, Pods y contenedores; presentar estados separados y consultar logs recientes por servicio.

Trabajo candidato:

- **#8:** proponer una PoC de catálogo Kubernetes explícita para Repository, Search, PostgreSQL, ActiveMQ, Share, transformadores, Elasticsearch/ECK y Traefik; resolver cada servicio mediante namespace, release/controlador, Service/selectores y contenedores.
- **#9:** separar conectividad, runtime, probes, endpoints, estado Elasticsearch, TLS/autenticación y frescura; no resumirlos en un semáforo global.
- **#10:** consultar logs acotados del servicio/contenedor seleccionado con timestamp, procedencia y límites temporales/de volumen; no agregar todos los servicios automáticamente.
- **#11:** queda fuera del sprint; primero se necesita un runbook ECM de backup/restore para `acs-independent` y una prueba integral aislada.

La demo debe validar el contexto `kind-acs-independent`, mostrar el catálogo multiservicio, señales de salud separadas y logs recientes por servicio bajo identidad Kubernetes restringida. El 24 de octubre se comprobó el nodo Ready, cuatro releases Helm deployed, ECK Elasticsearch Ready/yellow de un nodo, Repository 2/2 y otros pods 1/1 Ready, PVC Bound y probes Repository `Success - Tested` en lecturas repetidas. No hay Metrics API. El evento de pull-secret para Search requiere seguimiento. No incluye UI, acceso a Secrets ni escrituras. Se necesitan permisos de lectura en `acs-secure`, `elastic-system` y `traefik`; no aplicar Roles al laboratorio sin decisión separada. Los criterios y límites están en [Investigación técnica inicial](investigacion-tecnica-inicial.md).

La duración propuesta sigue siendo una semana, pero el alcance anterior de single-release/namespace debe ampliarse por la separación entre releases y ECK. Confirmar capacidad e identidad de lectura antes de asignar; no hay fecha ni Iteration. No marcar trabajo como terminado por estar documentado.

## Estado verificado de GitHub Projects

El proyecto privado [Control Center](https://github.com/users/AlejoAvila/projects/5) ya existía como proyecto personal y contiene las 12 Issues #1–#12 del repositorio. No se creó un tablero duplicado. Los 12 elementos permanecen en `Backlog`; la incorporación y clasificación no implica que estén implementados ni seleccionados para un sprint.

El flujo `Status` existente coincide con `docs/gestion-agil.md`: `Backlog`, `Ready`, `In progress`, `In review`, `In validation`, `Blocked` y `Done`. También existen `Priority` (P0–P3), `Size` (XS–XL) y campos nativos de GitHub como `Parent issue` y `Milestone`. Se añadieron los campos `Tipo de trabajo` (Épica, Historia, Tarea técnica, Defecto, Investigación) y `Área` (Producto, Arquitectura, Kubernetes, Monitorización, Operaciones, Backup, Seguridad, Gestión ágil); las Issues existentes quedaron clasificadas de acuerdo con sus títulos y alcance.

No hay un campo de Iteración/Sprint configurado ni milestones. `gh` 2.45.0 no ofrece un tipo Iteration en `project field-create`; la fecha de inicio y la capacidad del primer sprint tampoco se han acordado. Definir la fecha/capacidad y luego configurar la iteración desde GitHub Projects antes de asignar trabajo. No se han creado historias de implementación porque las propuestas siguen pendientes de revisión.

## Pendientes de gestión

- Acordar fecha de inicio y capacidad del sprint; configurar entonces una Iteration y asignar solo las historias aprobadas.
- Revisar si hacen falta etiquetas adicionales; se conservaron las etiquetas existentes y no se duplicaron sus funciones con nuevas etiquetas.
- Revisar y aprobar las historias propuestas en el informe antes de crear Issues de implementación; #8–#12 siguen abiertas.
