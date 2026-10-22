# Investigación técnica inicial

**Fecha de revisión documental:** 2026-10-22
**Issues de origen:** [#8](https://github.com/AlejoAvila/control-center/issues/8), [#9](https://github.com/AlejoAvila/control-center/issues/9), [#10](https://github.com/AlejoAvila/control-center/issues/10), [#11](https://github.com/AlejoAvila/control-center/issues/11) y refinamiento de [#12](https://github.com/AlejoAvila/control-center/issues/12).
**Alcance:** investigación y propuesta documental. No se implementó Control Center ni se modificó `alfresco-community-platform`. Las Issues permanecen abiertas; los resúmenes y la propuesta se enlazan en sus conversaciones para revisión del propietario.

## Resumen de evidencia

Se consultó `AGENTS.md`, `README.md`, arquitectura, baseline tecnológico, roadmap, preflight, configuración Helm propia, matriz de compatibilidad y runbook de backup/restauración del repositorio ECM en `main` (árbol `c67cc152655dfd557652737df06dedc66b29f478`, sin cambios al repetir la consulta). Se revisaron también las Issues #1–#12 del repositorio Control Center. El contexto Kubernetes activo ahora es `kind-acs-secure-prototype`, creado hace 23 minutos durante la verificación; contiene el namespace `acs-secure`, un Elasticsearch 8.17.10 verde (1 nodo) y ECK 3.5.0, pero no un despliegue de ACS ni un release Helm. El cluster `acs-local` también sigue existiendo y su contexto explícito `kind-acs-local` tiene el nodo Ready, namespace `alfresco` Active y el release `acs-community` desplegado. Allí se observaron 11/11 pods ECM Running/Ready, Services ClusterIP y EndpointSlices. Por tanto, el perfil Alfresco inicial viable es `kind-acs-local`, pero no es el contexto por defecto y debe seleccionarse explícitamente; no inferir el entorno desde el contexto actual.

Herramientas observadas el 22-oct: Docker Engine `29.9.0`, kind `v0.33.0`, Helm `v3.22.0` y kubectl cliente `v1.34.11`. `kind get clusters` devolvió `acs-local` y `acs-secure-prototype`. Helm no encontró releases en el contexto activo; con `--kube-context kind-acs-local`, `acs-community` aparece `deployed`, chart `10.7.0`, app `26.2.0`, revisión 2. El perfil activo nuevo no debe confundirse con el entorno ECM inicial.

La probe activa del Repository, consultada a través del proxy API en `kind-acs-local`, devolvió `readyProbe: Success - Tested`; el Pod Repository también reportó Ready. No se encontraron eventos Warning en `alfresco`. Hay contadores acumulados de reinicios (8–26); son históricos y no invalidan por sí solos Ready actual, pero requieren revisión operativa separada. No apareció `metrics.k8s.io` en ninguno de los dos contextos ni una fuente de métricas de ACS documentada.

La ejecución documentada del 11 de octubre usó chart `10.7.0`, ACS `26.2.0`, Search Community `5.7.0`, Elasticsearch `8.17.10`, PostgreSQL `17.9` y Repository de una réplica. La inspección actual confirma esas versiones en workloads, no compatibilidad formal del conjunto completo. El perfil de laboratorio y sus excepciones no deben trasladarse a producción.

### Niveles de evidencia

| Nivel | Resultado de esta investigación |
|---|---|
| Documentado | El runbook ECM del 11-oct describe backup frío de PostgreSQL y Content Store; la arquitectura fija el perfil Community con Elasticsearch/Search Community. El chart y documentación Kubernetes describen APIs, probes y consulta de logs. |
| Comprobado mediante consulta | Ambos clústeres kind existen; `acs-local` conserva el release y los 11 pods; el contexto por defecto es otro prototipo con ECK/Elasticsearch y sin ACS. Se listaron servicios, endpoints y estados sin leer Secrets ni contenido. |
| Comprobado mediante prueba | La solicitud de solo lectura a la probe del Repository en `acs-local` respondió `readyProbe: Success - Tested`. Es una verificación funcional puntual de esa probe, no una prueba end-to-end de ACS ni una PoC del adaptador. |
| Pendiente de validar | Permisos mínimos con una identidad no administradora, PoC cliente, mapa de servicios, límites de logs en operación, métricas de aplicación, restore integral, reindexación y RPO/RTO. |

## #8 — Entorno e interfaz de administración

### Evidencia revisada

- `docs/operations/kind-local-cluster.md` registra `acs-local`, kind `v0.33.0`, Kubernetes `v1.34.11` y cliente `kubectl v1.34.11`, con validaciones ejecutadas el 10 de octubre.
- `docs/operations/acs-community-preflight.md` documenta una inspección de solo lectura y render del chart `10.7.0`; no desplegó ACS en esa preflight.
- `docs/operations/backup-restore-kind.md` registra que el 11 de octubre existía el release `acs-community` desplegado en el namespace `alfresco`, chart `10.7.0`, ACS `26.2.0`; al final se observaron 11 pods Ready, cuatro PVC Bound y endpoints de Repository y Search disponibles.
- Comprobación en vivo 2026-10-22: `kind-acs-local` sigue Ready y conserva 11/11 pods ECM Ready, Repository 1/1 y PostgreSQL/Elasticsearch StatefulSets 1/1; Services y EndpointSlices presentes. El contexto activo `kind-acs-secure-prototype` no contiene ACS, por lo que el cliente debe seleccionar `kind-acs-local` explícitamente.
- GET en vivo a la probe del Repository: `{"entry":{"message":"readyProbe: Success - Tested"}}`. No se hicieron solicitudes de contenido ni autenticación de usuario final.
- `kubectl get apiservices -o name` no mostró APIService de métricas; la consulta del listado de Pods tampoco encontró backend de métricas en los namespaces existentes.
- No hubo eventos Warning para `alfresco` en la consulta ejecutada. Se observaron contadores de reinicios acumulados; no se leyeron logs para atribuir causas.

### Recomendación

Adoptar **Kubernetes API como primer adaptador candidato de solo lectura** para el perfil local `kind-acs-local`. La comprobación actual confirma que el clúster existe, está accesible y que Alfresco está desplegado allí; sin embargo, el contexto por defecto apunta al prototipo `kind-acs-secure-prototype`, que no tiene ACS. La configuración del entorno debe guardar y mostrar el contexto explícito, validar su identidad y namespace permitidos y fallar de forma segura ante un contexto no esperado. No diseñar Control Center como ejecutor genérico de comandos ni requerir acceso de administrador al clúster.

Una integración por namespace puede consultar Pods y estados de contenedor, Services y sus selectores, EndpointSlices, y controladores Deployment/StatefulSet para comparar réplicas deseadas y disponibles. El API server accesible demuestra conectividad del plano de administración; no demuestra que el plano de datos, Alfresco o todos los nodos estén disponibles. La probe actual del Repository aporta una señal funcional de readiness del Repository en el momento de la consulta, pero no sustituye pruebas de login, escritura/lectura de contenido, búsqueda ni dependencia completa del stack. El estado de nodos requeriría permisos a nivel de clúster y debe ser una capacidad separada, opcional y justificada.

Alternativas consideradas: Kubernetes API directa con RBAC namespace-scope (preferida para la PoC por ofrecer estado de recursos, probes, EndpointSlices y `pods/log` en el entorno); invocar scripts operativos (no se hallaron scripts versionados equivalentes y aumenta el riesgo de shell genérico); depender de observabilidad centralizada (no hay fuente instalada/documentada). Se recomienda registrar una ADR de adaptador Kubernetes después de validar la PoC y permisos; esta investigación no aprueba la decisión arquitectónica definitiva.

Permisos iniciales a evaluar como `Role` acotado al namespace `alfresco`: `get/list/watch` sobre `pods`, `services`, `endpointslices`, `deployments`, `statefulsets` y, si se necesitan para diagnóstico, `events`; `get` sobre `pods/log` para la capacidad de logs. Verificar los verbos exactos mediante `kubectl auth can-i` y una prueba de cliente. Excluir `secrets`, `pods/exec`, creación, actualización, eliminación y roles globales. Las operaciones de escritura y backup quedan fuera de este adaptador de consulta.

### Límites y siguiente evidencia

Kubernetes puede describir runtime, estado de Pod, réplicas, endpoints y probes configurados. No aporta por sí mismo salud funcional completa de ACS. El chart desplegado define una probe de Repository en `GET /alfresco/api/-default-/public/alfresco/versions/1/probes/-ready-`; la lectura actual devolvió `readyProbe: Success - Tested`. Revalidar ruta y semántica ante cambios de versión y no usar esa señal como indicador de todos los servicios del stack.

En WSL2, el contexto local puede desaparecer si la distribución, Docker o el cluster kind no están activos. Distinguir timeout/transporte no disponible, `401` (autenticación), `403` (autorización) y respuesta válida del API. Los dos primeros errores de acceso no deben aparecer como servicio caído. Para este producto, «entorno desconectado» significa que no se obtuvo respuesta válida del endpoint configurado dentro del timeout, con causa de transporte diferenciada de fallo de autenticación/permisos.

**PoC propuesta, no ejecutada:** cliente de solo lectura, con kubeconfig/identidad fuera de Git, consulta explícita del contexto y namespace permitidos; lista recursos acotados, muestra origen y hora de consulta; verifica acceso permitido/denegado y manejo de timeout; consulta la probe del Repository. Guardar salida saneada y versiones, sin Secrets, logs de negocio, datos de contenido ni `exec` dentro de contenedores.

## #9 — Estado y salud

Kubernetes diferencia estado del contenedor (Waiting/Running/Terminated), fase y condiciones del Pod, disponibilidad declarada por sus probes y estados del Deployment/StatefulSet. Si no hay readiness probe configurada, Kubernetes puede considerar ready al contenedor sin haber probado la función de negocio. `Ready` significa que el Pod puede atender tráfico según la señal configurada; no equivale a una transacción ECM exitosa.

| Dimensión | Fuente | Regla propuesta | Límite/error |
|---|---|---|---|
| Conectividad de administración | Respuesta del API server | `disponible` ante respuesta autenticada y autorizada a la lectura objetivo; `no disponible` ante timeout/error de transporte | `401/403` = problema de credencial/permisos, no indisponibilidad del servicio. |
| Runtime | `Pod.status.containerStatuses[*].state` | Presentar Running/Waiting/Terminated por contenedor | Running no declara salud de la aplicación. Si no hay estado reciente accesible: `desconocido`. |
| Estado del Pod | `Pod.status.phase`, `conditions`, `reason`, `restartCount` | Mostrar fase y condición Ready por separado; conservar reinicios y motivo | `Running` puede coexistir con Ready=False. Condiciones `Unknown` o ausentes se muestran como desconocidas, no saludables. |
| Réplicas/workload | `Deployment.status` o `StatefulSet.status` | Comparar réplicas deseadas, actuales y disponibles; estado completo solo si disponibles ≥ deseadas | No aplicar esta fórmula a Jobs u otros objetos. |
| Endpoint del servicio | Service selector y EndpointSlice | Mostrar endpoints listos/no listos asociados al selector | La asociación debe venir de selector/configuración inspeccionable, no de adivinar nombres. |
| Salud funcional ACS Repository | Probe ACS `-ready-` del chart, si está expuesta y autorizada | `saludable` solo ante respuesta satisfactoria de esa probe; timeout/error = no disponible/desconocido según causa | No representa salud de Share, PostgreSQL, Search o transformadores. Validar la versión real. |
| Frescura | Timestamp local de cada intento, resultado y origen | Mostrar `última comprobación` y edad; si excede el máximo configurable, etiquetar `obsoleto` sin conservar un healthy vigente | Definir el umbral operativo mediante frecuencia de consulta/SLO acordados; no fijarlo por intuición en esta investigación. |

La UI y el modelo deben conservar dimensiones independientes; no colapsarlas en un único semáforo que oculte si se comprobó runtime, probe funcional o solo conectividad. La máquina de estados y el valor máximo de antigüedad quedan pendientes de acuerdo tras observar una frecuencia de sondeo real.

## #10 — Logs y métricas

### Logs por servicio

La Issue #10 establece que consultar registros **por servicio** es requisito básico. Kubernetes no define el «servicio de producto» de Control Center: registrar un mapa permitido desde componente lógico a Service/workload y selector verificados. Resolver los Pods asociados por etiquetas/selectores y solicitar `pods/log` por contenedor; incluir en cada línea origen (namespace, Pod y contenedor) y timestamp. No aceptar selectores arbitrarios aportados por el navegador.

La API/kubectl de Kubernetes permite elegir contenedor, consultar `sinceSeconds`/`sinceTime`, `tailLines` y `limitBytes`, solicitar timestamps, prefijar el origen, seguir la salida y recuperar la instancia anterior cuando aún existe. Esto da límites de tiempo, líneas/bytes y seguimiento; **no ofrece paginación histórica general ni filtros server-side normalizados de severidad o texto**. Severidad no es una propiedad uniforme de stdout/stderr. Búsqueda/severidad requerirían interpretación acotada en cliente/backend o una fuente centralizada que se evalúe por separado.

La guía oficial del chart ACS indica que el chart no incluye agregación centralizada y que los logs de Pods pueden perderse al terminarse el Pod. Kubernetes también documenta que `kubectl logs` lee el archivo actual del kubelet; la rotación y evicción limitan el histórico recuperable. Para el MVP: consulta acotada de registros recientes por servicio, límites explícitos, timestamps/origen, timeout, autorización, ausencia de logs y redacción/aviso de secretos potenciales. No prometer retención histórica ni búsqueda sobre Pods ya eliminados.

### Métricas

La documentación y configuración ECM revisadas no describen una instalación de Prometheus ni un backend centralizado de métricas. El árbol del repositorio no contiene manifiestos de observabilidad en las rutas revisadas. La consulta actual a `kubectl get apiservices` no encontró APIService `metrics.k8s.io`; tampoco se observó un backend de métricas en los namespaces/pods listados. No se comprobaron exporters ni métricas funcionales de ACS. CPU/memoria observadas precisarían una fuente de métricas desplegada; requests/limits son configuración solicitada, no consumo real. Aplazar gráficas y no instalar un stack de observabilidad en esta fase.

| Fuente/capacidad | Disponibilidad por evidencia | Campos/filtros | Permiso / dependencia | Validación |
|---|---|---|---|---|
| Logs del contenedor vía `pods/log` | Soportado por Kubernetes y usado por la guía ACS; ACS tuvo Pods en la captura del 11-oct | Servicio mapeado, Pod, contenedor, timestamps, tiempo desde/hasta acotado, tail/bytes, follow opcional | `get` `pods/log`; `get/list/watch` Pods y Service/workload | Capacidad del mecanismo confirmada documentalmente; RBAC real y retención actual pendientes. |
| Severidad y texto | No es un filtro normalizado en la fuente directa | Posible filtrado limitado de líneas recibidas; no garantiza nivel semántico | Ningún backend extra si se procesa localmente; controlar CPU/volumen | Pendiente de acordar el alcance de MVP. |
| Métricas Kubernetes | No encontrada en los documentos ni en los APIService de ambos contextos consultados | No declarar CPU/RAM observados a partir de requests | `metrics.k8s.io` y proveedor correspondiente, si se confirma instalado | Consulta en vivo sin APIService de métricas; no se instaló ningún componente. |
| Métricas de aplicación ACS | No hallada fuente configurada en los documentos examinados | Depende de endpoint/exporter y versión | Instrumentación adicional y permisos por validar | No disponible como requisito confirmado. |

## #11 — Backup y restauración

### Procedimiento y evidencia del repositorio ECM

El árbol remoto inspeccionado contiene `docs/operations/backup-restore-kind.md`; no contiene scripts `.sh`/`.ps1` de backup/restore. El runbook es procedimiento documental con comandos inline, no automatización versionada reutilizable. Se ejecutó el 11-oct en el perfil kind documentado:

| Componente | Procedimiento/evidencia documentada | Estado y brecha |
|---|---|---|
| PostgreSQL | Dump lógico custom-format desde PostgreSQL `17.9`; `pg_restore --list` y restauración en base vacía temporal aislada. | Dump fue legible y restaurado: código cero, 70 tablas y comprobación SQL. No restaurado en un ACS nuevo ni validada semántica ECM/login/búsqueda. |
| Content Store | Copia fría de `contentstore` y `contentstore.deleted` en tar, con Repository a cero; inventario/hash del origen comparado con extracción. | Coincidieron 274 archivos en la prueba registrada. No demuestra por sí solo que la aplicación completa se recupere. |
| Consistencia | PostgreSQL y Content Store se capturaron dentro de la misma pausa fría de Repository y se trataron como un conjunto. | Evidencia positiva para ese runbook y esa ejecución del 11-oct; no para copias en caliente o snapshots genéricos de PVC. |
| Elasticsearch/Search Community | No se incluye snapshot del índice en el conjunto; el runbook señala reconstrucción como posibilidad mediante configuración vigente y reindexación posterior. | Tiempo de reindexación/RTO y recuperación funcional no medidos. La documentación genérica ACS 25.2 habla de índices Solr; no extrapolar esas instrucciones al Search Community + Elasticsearch del perfil 26.2. |
| ActiveMQ, Helm/configuración, Secrets | No incluidos en el backup probado. Versiones/identificadores se documentan; Secrets deben recuperarse desde el gestor de contraseñas. | Reconstrucción total del entorno no demostrada; completar valores seguros, requisitos/orden de restauración y verificación. |
| Destino/retención | Conjunto guardado con permisos locales restringidos dentro del filesystem WSL2. | No es copia fuera del equipo; retención, cifrado, copia externa y supervisión automática no están definidos/validados. |
| Restauración integral | No hubo restauración de release, login, carga/lectura de contenido, indexación/búsqueda ni validación de transformaciones. | Backup de datos parcialmente validado; recuperación del servicio completa **no validada**. |

La documentación oficial ACS Community 25.2 requiere que base de datos y `dir.root` procedan del mismo conjunto; su procedimiento de hot backup se refiere explícitamente a Solr 6. La propia guía acs-deployment indica que backup y logging se integran desde las soluciones operativas del usuario. La arquitectura ECM fija Elasticsearch/Search Community, por lo que hay que seguir su runbook específico y no trasladar sin validación los pasos de Solr.

### Contrato preliminar para Control Center

Control Center debe solicitar un perfil aprobado al procedimiento validado del entorno; no ejecutar `kubectl exec`, shell ni SQL arbitrarios desde una API genérica. Parámetros visibles y permitidos: `environmentId`, `backupProfileId`, destino registrado, política de retención aprobada e identificador de solicitud/idempotencia. No aceptar destinos, rutas o comandos libres.

Registro del trabajo: `jobId`, actor/entorno/perfil, estado (`queued`, `running`, `succeeded`, `failed`, `unknown`), `createdAt`, `startedAt`, `finishedAt`, etapa por componente, progreso solo si la herramienta lo mide, destino lógico redactado, checksum/manifiesto si se produce, resumen de errores seguro y referencia a la evidencia. El resultado del backup debe mantenerse distinto del resultado de una restauración. No marcar recuperable al terminar el proceso: requiere restauración aislada, coherencia DB/Content Store, validación de contenido/metadatos sintéticos, reindexación y comprobación funcional documentada.

## #12 — Refinamiento y sprint semanal propuesto

Se conservan las siete épicas existentes y las investigaciones #8–#11; sus objetivos amplios no requieren más Issue duplicada en esta revisión. La investigación documental y la comprobación actual de #8–#11 quedan disponibles para revisión del propietario. #12 sigue siendo trabajo de refinamiento; Issues no cerradas ni modificadas.

**Objetivo del sprint (una semana):** demostrar una consulta reproducible de solo lectura por namespace que presenta conectividad de administración, runtime/Pod, readiness de ACS Repository y logs recientes por servicio con permisos mínimos.

**Historia propuesta, pendiente de crear tras revisión:** *HU: Consultar estado operativo y logs recientes por servicio en el entorno Kubernetes inicial.* Como operador, quiero consultar el estado y los logs acotados de un servicio seleccionado, para diagnosticar problemas sin conceder permisos de escritura al Control Center.

**Criterios propuestos:** (1) catálogo de servicios con su origen Kubernetes; (2) runtime, estado del Pod, readiness y conectividad como señales separadas con timestamp; (3) selección de servicio devuelve logs de sus Pods/contenedores con origen, ventana temporal y límite de volumen; (4) timeout, `401/403`, ausencia de Pods/logs y dato obsoleto se distinguen; (5) el adaptador no lee Secrets, no escribe recursos, no ejecuta shell y no acepta selectores arbitrarios.

| Trabajo candidato (Issue nueva, aún no creada) | Criterio de aceptación y evidencia | Dependencias/riesgos | Fuera de alcance |
|---|---|---|---|
| Tarea: PoC de inventario de solo lectura para la HU propuesta | Lista únicamente Pods, Services, EndpointSlices, Deployments y StatefulSets de `alfresco`; muestra contexto explícito `kind-acs-local`, origen y timestamp; diferencia timeout de `401/403`. Evidencia: salida saneada reproducible y matriz `kubectl auth can-i` ejecutada con identidad de prueba. | Credencial no administradora; revisión del Role por propietario antes de aplicarlo. Riesgo: el contexto activo puede ser el prototipo sin ACS; debe impedir consulta accidental del clúster equivocado. | No modificar cluster/Role en esta fase salvo que el propietario apruebe como alcance del sprint; no Secrets, escrituras, `exec`, UI ni acceso cluster-wide. |
| Tarea: contrato y modelo de estado por dimensión | Presenta conectividad, runtime/Pod, readiness del workload y probe ACS por separado, con origen y timestamp; estados desconocidos/no disponibles no se convierten en saludables. Evidencia: ejemplos de respuesta para Ready, Waiting/NotReady, timeout y permiso denegado. | Tabla de #9; fijar frescura luego de acordar intervalo/SLO. Riesgo: `readyProbe` solo cubre Repository, no el stack completo. | Sin semáforo agregado ni umbral inventado; no probar transacciones de contenido ni búsqueda todavía. |
| Tarea: consulta limitada de logs recientes por servicio | Un servicio permitido resuelve a sus Pods/contenedores; devuelve timestamps y origen y limita `sinceTime`/`tailLines`/`limitBytes`; errores de permiso, ausencia y timeout distinguibles. Evidencia: consulta saneada con servicio seleccionado, volumen y ventana documentados. | Role de solo lectura incluye `get pods/log`; validar límites y redacción de secretos potenciales. Riesgo: no hay paginación histórica ni retención tras eliminar Pods. | Sin severidad server-side, agregación obligatoria, retención histórica, lectura de logs de negocio para esta investigación, `exec` ni backend observability nuevo. |

**Capacidad/planificación:** una semana de trabajo de Codex para las tres tareas candidatas, sujeto a confirmación del propietario; no hay estimación de puntos ni fecha de inicio. El propietario revisa la evidencia al cierre y decide si cierra las Issues. No asignar estas tareas al sprint ni crear sus Issues hasta aprobar el plan.

**Demostrable al cierre:** inventario y estados actuales, consulta de probe del Repository y consulta limitada de logs de un servicio con procedencia/timestamp bajo una identidad namespace-scope. Las métricas quedan fuera porque su API no está instalada. No incluye interfaz de usuario, operaciones privilegiadas, automatización de backup ni restauraciones. Crear/validar un Role cambia la configuración del entorno; debe planificarse explícitamente. #8–#10 permanecen abiertos para revisión. Las Issues de implementación se proponen, pero no se crearon para evitar publicar cambios en GitHub antes de la aceptación del propietario. #11 no se selecciona para el primer sprint: sus brechas de restore integral quedan para una fase posterior.

**Criterios de salida propuestos:** criterios de aceptación verificados con evidencia saneada; ninguna credencial ni contenido de negocio en Git; permisos mínimos probados (si el Role queda aprobado); fallos de timeout y autorización diferenciados; documentación y Issues enlazadas a la evidencia. El propietario revisa antes de cerrar cualquier Issue. No se asigna fecha de calendario ni se confirma capacidad hasta su aprobación.

## Riesgos y decisiones pendientes

1. Seleccionar explícitamente `kind-acs-local` para el perfil ECM; el contexto activo puede apuntar a otro clúster. Revalidar tras cambios de WSL, Docker, kind o despliegue.
2. Confirmar alcance real de la probe y endpoints por versión desplegada.
3. Definir mapa estable de servicio lógico a Service/workload/selectores del chart.
4. Aprobar frecuencia de polling y máximo de frescura antes de definir valores.
5. Verificar API de métricas actual; no hay una fuente centralizada documentada.
6. Definir mecanismo de identidad y rotación de credenciales Kubernetes; entregar únicamente Role namespace-scope al producto.
7. Completar restore integral en `alfresco-community-platform` en fase separada; no modificar ese repositorio en esta tarea.
8. Decidir destino externo, retención, cifrado, RPO/RTO y método fiable de reconstrucción del índice antes de llamar recuperable al respaldo.

## Fuentes

### Repositorios examinados el 2026-10-22

- [AGENTS.md de alfresco-community-platform](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/AGENTS.md)
- [Arquitectura](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/overview.md), [baseline](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/technology-baseline.md), [roadmap](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/project/roadmap.md)
- [Preflight ACS Community](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/acs-community-preflight.md), [estado kind](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/kind-local-cluster.md), [runbook backup/restore kind](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/backup-restore-kind.md), [valores comunes](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-community/values-common.yaml), [valores kind](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-community/values-kind.yaml), [matriz de compatibilidad](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/compatibility-matrix.md)

### Referencias oficiales externas

- [Kubernetes: Pod lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/), [probes](https://kubernetes.io/docs/concepts/workloads/pods/probes/), [RBAC/seguridad](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/)
- [Kubernetes: `kubectl logs`](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/), [arquitectura de logging](https://kubernetes.io/docs/concepts/cluster-administration/logging/), [API concepts](https://kubernetes.io/docs/reference/using-api/api-concepts/)
- [ACS Deployment: Helm overview](https://alfresco.github.io/acs-deployment/docs/helm/README.html), [REST API probes del Repository chart](https://github.com/Alfresco/alfresco-helm-charts/blob/main/charts/alfresco-repository/README.md)
- [ACS Community 25.2: backup/restore](https://docs.hyland.com/r/Alfresco/Alfresco-Content-Services-Community-Edition/25.2/Alfresco-Content-Services-Community-Edition/Administer/Backup-and-migrate/Back-up-and-restore)
