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

## Sprint 1 semanal: planificación propuesta para aprobación

**Base:** investigación revalidada en `c9d89dd`; entorno único `kind-acs-independent` con cuatro releases Helm (`acs-secure-repository`, `acs-search-community`, `acs-repository-text`, `traefik`), ECK y namespaces `acs-secure`, `elastic-system`, `traefik`. Una semana de trabajo; sin fechas ni Iteration hasta aprobar capacidad y planificación. Las prioridades y tamaños son provisionales, no puntos ni compromiso de fechas.

**Objetivo:** dejar una base verificable para diagnóstico seguro de solo lectura: identificar con seguridad el entorno, mapear componentes Helm/ECK a workloads/Pods/contenedores y separar salud/conectividad, manteniendo el acceso mínimo. Logs por servicio quedan como objetivo adicional condicionado a la capacidad y a una identidad disponible.

### Alcance comprometido propuesto

| Issue | Tipo / épica | Prioridad / tamaño | Criterios de salida | Dependencias |
|---|---|---|---|---|
| [#13 — Configurar de forma segura el entorno `kind-acs-independent`](https://github.com/AlejoAvila/control-center/issues/13) | Historia / E1 (#1) | P1 / S | Verificar contexto e identidad antes de leer; permitir explícitamente los tres namespaces; falla cerrada; configuración/credenciales fuera del repo; errores diferenciados. | Identidad de lectura disponible del propietario; antecede a #15. |
| [#14 — Definir RBAC mínimo de lectura para ACS, ECK y Traefik](https://github.com/AlejoAvila/control-center/issues/14) | Tarea técnica / E6 (#6) | P1 / S | Política/manifiestos revisables por namespace; recursos/verbs justificados; `pods/log` solo para esa capacidad; `can-i` como plan; sin Secrets, exec, escritura ni permisos globales. | Identidad restringida debe ser proporcionada/habilitada separadamente. No aplicar Roles en este sprint. |
| [#15 — Inventariar servicios distribuidos entre Helm y ECK](https://github.com/AlejoAvila/control-center/issues/15) | Historia / E2 (#2) | P1 / M | Catálogo Repository/TLS, PostgreSQL, ActiveMQ, Share, transformadores, Search, Elasticsearch/ECK, operador ECK y Traefik; vínculo namespace/release/controlador/Service/selector/Pod/contenedor con evidencia; admite sufijos dinámicos. | #13 y #14; alimenta #16 y #17. |
| [#16 — Presentar salud y conectividad por componente](https://github.com/AlejoAvila/control-center/issues/16) | Historia / E2 (#2) | P1 / M | Separar API, runtime/Ready/probes, endpoints, salud del CR ES y evidencia TLS/auth; mostrar fuente/hora; diferenciar unknown/stale de sano y 401/403/timeout/TLS/recurso ausente; resolver `No test` frente a `Success - Tested`. | Catálogo #15; configuración #13 y permisos mínimos de #14. Umbral de freshness queda pendiente de decisión, sin inventarlo. |

### Objetivo adicional (stretch)

| Issue | Tipo / épica | Prioridad / tamaño | Criterios de salida | Dependencias |
|---|---|---|---|---|
| [#17 — Consultar logs recientes por servicio y contenedor](https://github.com/AlejoAvila/control-center/issues/17) | Historia / E3 (#3) | P2 para secuenciación del sprint / M | Consultar solo el servicio/contenedor elegido usando `pods/log`, límites y origen (namespace/Pod/contenedor/timestamp); fallos diferenciados; no persistencia central por defecto. | #13–#15 y permiso `get pods/log` validado en #14; entra solo con identidad de lectura lista y capacidad restante. |

**Dependencias funcionales:** #13 → #14 (identidad/política efectiva) → #15 → #16; #17 depende de #13, #14 y #15. La política RBAC se prepara y revisa, pero su aplicación requiere autorización/ventana aparte. No modificar ni reiniciar ECM, no aplicar Roles, no ejecutar pruebas de interfaz, no implementar backup/métricas/UI. La evidencia de estado operativo existente es una captura previa de solo lectura y no sustituye validación de implementación.

**Capacidad:** suma relativa comprometida 2S + 2M; adicional 1M. La propuesta es para una semana, pero requiere que el propietario confirme capacidad e identidad disponible antes de marcar historias como seleccionadas/Ready. La Issue #17 no forma parte del compromiso base. No se han configurado fechas ni Iteration.

## Estado verificado de GitHub Projects

El proyecto privado [Control Center](https://github.com/users/AlejoAvila/projects/5) ya existía como proyecto personal y contiene las Issues #1–#17 del repositorio. No se creó un tablero duplicado. Los elementos #1–#17 permanecen en `Backlog`; la incorporación y clasificación no implica que estén implementados ni seleccionados para un sprint.

El flujo `Status` existente coincide con `docs/gestion-agil.md`: `Backlog`, `Ready`, `In progress`, `In review`, `In validation`, `Blocked` y `Done`. También existen `Priority` (P0–P3), `Size` (XS–XL) y campos nativos de GitHub como `Parent issue` y `Milestone`. El proyecto ya dispone de los campos `Tipo de trabajo` (Épica, Historia, Tarea técnica, Defecto, Investigación) y `Área` (Producto, Arquitectura, Kubernetes, Monitorización, Operaciones, Backup, Seguridad, Gestión ágil); las Issues #1–#17 quedaron clasificadas de acuerdo con sus títulos y alcance. Las cinco nuevas (#13–#17) se añadieron al tablero en `Backlog`, con tipo y área, y se vincularon como sub-issues a sus épicas E1/E6/E2/E3; GitHub rechazó por API la actualización de `Priority` y `Size`, aunque ambos campos existen. La prioridad provisional y el tamaño relativo están anotados en cada Issue y en la tabla de planificación, pero los campos de proyecto siguen pendientes de ajuste manual/verificación.

No hay un campo de Iteración/Sprint configurado ni milestones. `gh` 2.45.0 no ofrece un tipo Iteration en `project field-create`; la fecha de inicio y la capacidad del primer sprint tampoco se han acordado. La planificación aún requiere aprobación del propietario. Los campos `Priority` y `Size` de las cinco Issues nuevas están pendientes de ajuste/verificación desde la interfaz del Project; sus propuestas aparecen en este documento y en los cuerpos de Issues. No se configuraron fechas ni Iteration según la instrucción.

## Pendientes de gestión

- Acordar fecha de inicio y capacidad del sprint; configurar entonces una Iteration y asignar solo las historias aprobadas.
- Revisar si hacen falta etiquetas adicionales; se conservaron las etiquetas existentes y no se duplicaron sus funciones con nuevas etiquetas.
- Revisar y aprobar la planificación de Sprint 1; #8–#12 permanecen abiertas para revisión y las Issues de implementación #13–#17 también están abiertas.
