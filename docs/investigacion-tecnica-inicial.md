# Investigación técnica inicial

**Fecha de revisión:** 2026-10-24
**Issues de origen:** [#8](https://github.com/AlejoAvila/control-center/issues/8), [#9](https://github.com/AlejoAvila/control-center/issues/9), [#10](https://github.com/AlejoAvila/control-center/issues/10), [#11](https://github.com/AlejoAvila/control-center/issues/11) y refinamiento de [#12](https://github.com/AlejoAvila/control-center/issues/12).
**Alcance:** revalidación documental y consultas de solo lectura en `acs-independent`. No se implementó Control Center, no se modificó `alfresco-community-platform`, no se consultaron Secrets/logs de negocio y no se ejecutaron pruebas de interfaz. Las Issues permanecen abiertas.

## Resumen de evidencia

Se consultó `AGENTS.md`, arquitectura, baseline, roadmap, preflight, values, ADR-0004 y los runbooks actuales de `acs-independent` en `alfresco-community-platform` (`main`, árbol `c67cc152655dfd557652737df06dedc66b29f478`). La fuente documenta ACS Community 26.2.0 en WSL2/Ubuntu 24.04.5, con ECK/Elasticsearch y releases Helm desacoplados. El árbol ECM no cambió desde la revisión anterior. También se revisaron las Issues #1–#12 de Control Center.

**Consulta en vivo, 2026-10-24, exclusivamente `kind-acs-independent`:** kind muestra `acs-independent`; el nodo `acs-independent-control-plane` está Ready en Kubernetes 1.34.11. Namespace `acs-secure`, `elastic-system` y `traefik` están Active. Los cuatro releases Helm aparecen deployed: `acs-secure-repository` (ACS chart 10.7.0 / app 26.2.0), `acs-search-community` (chart 0.2.0 / app 5.7.0), `acs-repository-text` (chart auxiliar 0.1.0) y `traefik` (chart 41.6.0 / app 3.7.13). ECK 3.5.0 es instalado fuera de Helm; Elasticsearch CR `acs-es` está Ready, versión 8.17.10, salud `yellow`, un nodo. Hay 10 Pods en `acs-secure`: Repository 2/2 contenedores Ready; los otros nueve 1/1; cero reinicios en esa captura. Los cuatro PVC están Bound. Services y EndpointSlices tienen endpoints para los componentes consultados; hay Ingress HTTP para Repository y Share.

Docker Engine 29.9.0, kind 0.33.0, kubectl 1.34.11 y Helm 3.22.0 constan en las herramientas/runtime del laboratorio. El estado `yellow` de Elasticsearch es coherente con un nodo y réplicas sin asignar según el runbook; la fuente documenta seis primarios activos y cero primarios sin asignar. Se observó un evento Warning `FailedToRetrieveImagePullSecret` en el Pod de Search: el Pod sigue Ready y el contenedor activo, pero la recuperación del secreto de pull debe revisarse antes de que una recreación dependa de descargar esa imagen.

Lecturas GET repetidas de `-ready-` y `-live-` del Repository devolvieron `Success - Tested`; una lectura inicial durante la misma revisión devolvió `readyProbe: Success - No test`. Kubernetes reporta Repository 2/2 Ready. Esta variación queda anotada para aclarar semántica/estabilidad de la respuesta, sin convertirla en prueba de salud de todos los componentes. No se encontró Metrics API. El servicio systemd `acs-share-forward.service` aparece enabled/active; no se abrió Share ni se probó la UI.

### Niveles de evidencia

| Nivel | Resultado |
|---|---|
| Documentado | El runbook actualizado describe composición, releases, TLS, pruebas técnicas previas y límites del laboratorio. El runbook de backup del 11-oct solo se refiere al clúster retirado `acs-local`. |
| Comprobado mediante consulta | Solo `acs-independent`: versiones, releases, workloads, Services/selectores, Pods/containers Ready, PVC, Ingress, evento Warning y estado del CR de Elasticsearch. |
| Comprobado mediante prueba | GET de las probes Repository readiness/liveness: lecturas finales devuelven `Success - Tested`; el canario Search/Repository, pruebas sintéticas de contenido/formato, TLS y permisos están registrados en el runbook de reconstrucción. No se repitieron; no se ejecutó prueba de interfaz. |
| Pendiente de validar | RBAC Kubernetes de Control Center, estabilidad de la respuesta de probe, recolección de logs sin exposición, restore completo de la arquitectura actual, snapshots/reindexación de Elasticsearch, recuperación de CA/keystores/Secrets y RPO/RTO. |

La evidencia de `kind-acs-local` del informe anterior (22-oct) y el respaldo parcial de ese entorno (11-oct) se conservan únicamente como historia. `acs-local` fue retirado el 24-oct; no sustenta ninguna recomendación vigente ni valida backups para `acs-independent`.

## #8 — Entorno e interfaz de administración

### Evidencia revisada

- El runbook vigente `docs/operations/acs-independent-kind.md` identifica la modalidad B como laboratorio actual `acs-independent`, namespace `acs-secure`; ECK/ES y Search se despliegan fuera del release principal ACS.
- El runbook de reconstrucción registra las pruebas técnicas automatizadas ya ejecutadas (contenido/canario, formatos, TLS y permisos de las identidades Elasticsearch). No equivale a validación de producción; la aceptación de navegador corresponde al propietario y no se repitió aquí.
- Consulta de solo lectura 2026-10-24 en `kind-acs-independent`: nodo Ready, cuatro releases Helm deployed, CR `acs-es` Ready/yellow, diez Pods de `acs-secure` con contenedores Ready, cuatro PVC Bound y Services/EndpointSlices con endpoints.
- Repository tiene dos contenedores en un Pod (`alfresco-repository` y `repository-text-tls`); endpoints GET finales `-ready-` y `-live-` devolvieron `Success - Tested`. Una lectura inicial de readiness respondió `Success - No test`, por lo que conviene observar la estabilidad de la respuesta.
- Los únicos Ingress observados son Repository y Share, clase Traefik, HTTP/80; no hay Ingress para Elasticsearch. `acs-share-forward.service` aparece enabled/active; no se hizo petición a Share ni prueba de interfaz.
- No se encontró APIService de métricas. Se observó `FailedToRetrieveImagePullSecret` para el Pod de Search, pese a estar Running/Ready; revisar disponibilidad del material de pull de forma segura antes de recrear el Pod. No se consultó el Secret.

### Recomendación

Adoptar **Kubernetes API como primer adaptador candidato de solo lectura** contra `kind-acs-independent`. El clúster local de un nodo opera sobre WSL2/kind, no representa HA ni producción. La configuración debe fijar el contexto `kind-acs-independent`, cluster/namespace permitidos y verificar identidad antes de leer; una referencia ambigua o un cluster inesperado debe fallar de forma segura. No diseñar Control Center como ejecutor genérico de comandos ni requerir acceso de administrador.

La integración debe correlacionar Releases, workload labels, Services/selectors y EndpointSlices: Repository/DB/MQ/Share/transformaciones viven bajo `acs-secure-repository`; Search usa otro release; el Service de texto es un release auxiliar que apunta al Pod Repository; Elasticsearch es un CR administrado por ECK fuera de Helm; Traefik vive en otro namespace. El catálogo no debe asumir que un release equivale a un servicio, ni que todos los Pods tienen label Helm. El estado de nodo requeriría permisos a nivel clúster y sería una capacidad separada.

El API server disponible solo prueba conectividad de administración. Pods/Services/Endpoints y probes dan señales por componente, no salud global. Repository `-ready-` y `-live-` se pueden consultar en GET; actualmente devolvieron `Success - Tested`, pero una lectura inicial respondió `No test`. Aun una probe satisfactoria no sustituye transacciones ECM ni prueba a Search, DB, Share o transformaciones.

Alternativas consideradas: Kubernetes API con RBAC acotado (cubre los recursos que conectan varios releases y `pods/log`); scripts operativos genéricos (no son el contrato de Control Center y ampliarían riesgo de ejecución); observabilidad centralizada (no existe en el entorno). Se recomienda registrar una ADR tras una PoC que pruebe el catálogo multiservicio, TLS y permisos Kubernetes; la selección no se declara aprobada por esta investigación.

Permisos Kubernetes candidatos: Roles/RoleBindings solo en `acs-secure`, `elastic-system` y `traefik`; `get/list/watch` de Pods, Services, EndpointSlices, Deployments/StatefulSets, Ingress y Events según las vistas activadas; `get` de `pods/log`; y lectura del CR `elasticsearches.elasticsearch.k8s.elastic.co` en `acs-secure` para fase/salud ECK. No se requiere permiso sobre Secrets para el catálogo/logs, ni nodos, CRD cluster-wide, `pods/exec`, creación, actualización o eliminación. Una RoleBinding por namespace evita que el cliente necesite privilegio global. Los permisos K8s de Control Center aún no están probados con identidad restringida; las pruebas de permisos del runbook son de usuarios ES y no las sustituyen.

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

| Componente | Señal disponible en `acs-independent` | Interpretación/límite |
|---|---|---|
| Repository | Deployment/Pod 1/1 Ready; contenedores Repository y proxy TLS 2/2; GET readiness y liveness `Success - Tested` (una primera lectura readiness dijo `No test`). | Readiness y probe puntuales; no prueban todos los flujos de ACS. Aclarar la variación de respuesta. |
| Search Community | Pod 1/1 Ready; canario Repository/Search y contenido sintético constan PASS en el runbook. | El Pod Ready no sustituye la validación del indexador. Warning de pull-secret, aunque imagen/pod actual está activo. |
| Elasticsearch | CR ECK `Ready`, salud `yellow`, 1 nodo; runbook describe seis primarios activos, cero primarios sin asignar y réplicas no asignadas esperadas. | `yellow` no significa por sí solo fallo; consultar CR/API de forma autenticada para distinguir primarios y réplicas. |
| PostgreSQL, ActiveMQ y transformadores | Pods/containers 1/1 Ready; PVC Bound en DB y broker; pruebas sintéticas de formatos registradas en runbook. | No se ejecutaron consultas SQL, AMQ ni pruebas de transformación en esta revalidación. |
| Share/Traefik | Pods Ready, Services/Ingress presentes; forward systemd enabled/active. | No se hizo llamada HTTP ni prueba visual desde Windows; ingress del laboratorio usa HTTP/80 y loopback. |

Para el adaptador, runtime/Ready/eventos/endpoints son señales de plataforma; resultados canario/probes son señales funcionales específicas; errores de TLS/auth se muestran por separado. Ninguna señal única resume salud de todo el stack.

## #10 — Logs y métricas

### Logs por servicio

La Issue #10 establece que consultar registros **por servicio** es requisito básico. Kubernetes no define el «servicio de producto» de Control Center: registrar un mapa permitido desde componente lógico a Service/workload y selector verificados. Resolver los Pods asociados por etiquetas/selectores y solicitar `pods/log` por contenedor; incluir en cada línea origen (namespace, Pod y contenedor) y timestamp. No aceptar selectores arbitrarios aportados por el navegador.

La API/kubectl de Kubernetes permite elegir contenedor, consultar `sinceSeconds`/`sinceTime`, `tailLines` y `limitBytes`, solicitar timestamps, prefijar el origen, seguir la salida y recuperar la instancia anterior cuando aún existe. Esto da límites de tiempo, líneas/bytes y seguimiento; **no ofrece paginación histórica general ni filtros server-side normalizados de severidad o texto**. Severidad no es una propiedad uniforme de stdout/stderr. Búsqueda/severidad requerirían interpretación acotada en cliente/backend o una fuente centralizada que se evalúe por separado.

La guía oficial del chart ACS indica que el chart no incluye agregación centralizada y que los logs de Pods pueden perderse al terminarse el Pod. Kubernetes también documenta que `kubectl logs` lee el archivo actual del kubelet; la rotación y evicción limitan el histórico recuperable. Para el MVP: consulta acotada de registros recientes por servicio, límites explícitos, timestamps/origen, timeout, autorización, ausencia de logs y redacción/aviso de secretos potenciales. No prometer retención histórica ni búsqueda sobre Pods ya eliminados.

### Mapa de servicio lógico a fuentes de logs (captura actual)

Los nombres de Pod llevan sufijos dinámicos. Resolver desde el Service y su selector a Pods/Endpoints y después al workload/contenedor; no derivar un único selector de Helm release ni aceptar selectores del navegador. Los ejemplos corresponden a `acs-independent`, namespace `acs-secure`, salvo indicación.

| Servicio lógico | Release/controlador | Service/puerto | Contenedor de logs |
|---|---|---|---|
| ACS Repository | `acs-secure-repository` · Deployment `acs-secure-repository-alfresco-repository` | `acs-secure-repository-alfresco-repository:80` | `alfresco-repository` |
| Endpoint TLS de texto Repository | Chart auxiliar `acs-repository-text`; apunta al Pod del Repository | `acs-repository-text:8443` | Sidecar `repository-text-tls` del mismo Pod Repository |
| Alfresco Search Community | Release separado `acs-search-community` · Deployment `acs-search-community-alfresco-search-community` | `acs-search-community-alfresco-search-community:8080` | `alfresco-search-community` |
| Elasticsearch | ECK CR `acs-es`, no Helm · StatefulSet `acs-es-es-default` | `acs-es-es-http:9200` (también existen servicios interno/transport) | `elasticsearch` |
| PostgreSQL | `acs-secure-repository` · StatefulSet `acs-secure-repository-postgresql-acs` | `acs-secure-repository-postgresql-acs:5432` | `postgres` |
| ActiveMQ | `acs-secure-repository` · Deployment `acs-secure-repository-activemq` | `acs-secure-repository-activemq-broker` (broker) y `...-web-console:8161` | `activemq` |
| Share | `acs-secure-repository` · Deployment `acs-secure-repository-share` | `acs-secure-repository-share:80` | `alfresco-share` |
| Tika / PDF Renderer / LibreOffice / ImageMagick | `acs-secure-repository` · Deployment dedicado `acs-secure-repository-{tika,pdfrenderer,libreoffice,imagemagick}` | Service con nombre correspondiente, puerto 80 | `tika`, `pdfrenderer`, `libreoffice`, `imagemagick` respectivamente |
| Entrada local | Release `traefik` en namespace `traefik` · Deployment `traefik` | Service `traefik:80` y dos Ingress en `acs-secure` | `traefik` |
| Operador Elasticsearch | Manifiestos ECK 3.5.0, no release Helm · StatefulSet `elastic-operator` en `elastic-system` | `elastic-webhook-server:443` (webhook) | `manager` |

Los logs se obtendrían por `pods/log` con contenedor explícito y `timestamps`, `sinceTime/sinceSeconds`, `tailLines` y `limitBytes`; Repository tiene dos contenedores y ambos deben mapearse a fuentes diferentes. Una consulta de “Repository” no debe mezclar automáticamente el sidecar TLS. No se leyeron logs en esta revalidación.

### TLS y límites de identidad

El perfil documentado configura HTTPS/autenticación entre Repository y Elasticsearch, HTTPS/autenticación entre Search y Elasticsearch, y TLS con CA/truststore y verificación de hostname entre Search y el endpoint de texto `acs-repository-text`. Repository y Search usan identidades ES separadas. El runbook de reconstrucción registra respuestas válidas con credenciales, rechazo sin credenciales, rechazo de CA/hostname no confiable y controles de permisos de índice; son evidencia documentada de pruebas previas, no repetidas aquí. El endpoint de entrada Share/Repository del laboratorio es HTTP local/loopback, no TLS público. Mantener errores TLS distintos de 401/403 y de timeout.

Para observar el stack completo, el cliente Kubernetes necesitaría permisos mínimos en tres namespaces (`acs-secure`, `elastic-system`, `traefik`) y lectura del CR de Elasticsearch; no leer Secrets ni acceder a la API de Docker. Las identidades de producto Search/Repository hacia ES no deben confundirse con la identidad Kubernetes de Control Center.

### Métricas

La documentación y configuración ECM revisadas no describen una instalación de Prometheus ni un backend centralizado de métricas. El árbol del repositorio no contiene manifiestos de observabilidad en las rutas revisadas. La consulta actual a `kubectl get apiservices` no encontró APIService `metrics.k8s.io`; tampoco se observó un backend de métricas en los namespaces/pods listados. No se comprobaron exporters ni métricas funcionales de ACS. CPU/memoria observadas precisarían una fuente de métricas desplegada; requests/limits son configuración solicitada, no consumo real. Aplazar gráficas y no instalar un stack de observabilidad en esta fase.

| Fuente/capacidad | Disponibilidad por evidencia | Campos/filtros | Permiso / dependencia | Validación |
|---|---|---|---|---|
| Logs del contenedor vía `pods/log` | Soportado por Kubernetes y usado por la guía ACS; ACS tuvo Pods en la captura del 11-oct | Servicio mapeado, Pod, contenedor, timestamps, tiempo desde/hasta acotado, tail/bytes, follow opcional | `get` `pods/log`; `get/list/watch` Pods y Service/workload | Capacidad del mecanismo confirmada documentalmente; RBAC real y retención actual pendientes. |
| Severidad y texto | No es un filtro normalizado en la fuente directa | Posible filtrado limitado de líneas recibidas; no garantiza nivel semántico | Ningún backend extra si se procesa localmente; controlar CPU/volumen | Pendiente de acordar el alcance de MVP. |
| Métricas Kubernetes | No encontrada en la documentación ni en APIService de `acs-independent` | No declarar CPU/RAM observados a partir de requests | `metrics.k8s.io` y proveedor correspondiente, si se confirma instalado | Consulta en vivo sin APIService de métricas; no se instaló ningún componente. |
| Métricas de aplicación ACS | No hallada fuente configurada en los documentos examinados | Depende de endpoint/exporter y versión | Instrumentación adicional y permisos por validar | No disponible como requisito confirmado. |

## #11 — Backup y restauración

### Procedimiento y evidencia por arquitectura

El runbook actual `docs/operations/acs-independent-kind.md` y ADR-0004 dicen que backup/restore quedan para otra fase; el runbook de reconstrucción confirma que no se hicieron. `docs/operations/backup-restore-kind.md` es una prueba histórica del laboratorio `acs-local`, retirado el 24-oct: dump/restore aislado PostgreSQL y copia/hash del Content Store sí pasaron allí, pero **no prueban recuperación de `acs-independent`**. No se encontraron scripts reutilizables de backup/restore para la modalidad independiente; hay comandos de bootstrap/validación, no un contrato de respaldo integral.

| Componente/activo en `acs-independent` | Método o dependencia identificada | Estado/evidencia y brecha |
|---|---|---|
| PostgreSQL 17.9 (StatefulSet/PVC 8Gi) | Dump del motor y restauración aislada son alternativas históricas probadas en otro laboratorio; el procedimiento debe coordinarse con el Repository y el Content Store. | Sin backup/restauración de esta instancia. Definir versión/herramienta, consistencia, checksum, aislamiento y recuperación end-to-end. |
| Content Store (PVC Repository 20Gi, RWO) | Debe formar un conjunto coherente con PostgreSQL; preservar `contentstore`, archivos borrados si aplica, permisos/ACL/xattrs y subPath reales. | Sin copia actual ni prueba de recuperación en el nuevo nodo. PVC Bound no protege de pérdida del clúster/WSL/disco. |
| Elasticsearch 8.17.10 (ECK `acs-es`, PVC 4Gi) | Decidir snapshot autenticado/TLS o reconstrucción del índice Search desde Repository; guardar también CR/configuración para restaurar. | Ningún snapshot, reindexación o RTO probados en este entorno. Estado `yellow` de un nodo no cambia los requisitos de backup. |
| Search Community 5.7.0 (release Helm separado) | Chart/values fijados; Search indexa Repository y usa identidad Elasticsearch separada. | Sin estado durable/rollback documentado como respaldo ni ejercicio de recreación tras restore. |
| ActiveMQ (Deployment/PVC 2Gi) | Determinar si mensajes encolados requieren backup o si basta pausar productores/consumidores y reinicializar según operación segura. | No respaldado ni restaurado; semántica/duración de colas pendiente. |
| Secretos, TLS y cifrado | ECK crea CA/certs; truststores Java, contraseñas, identidades ES y keystore de cifrado de metadatos tienen referencias separadas. Valores no están en Git y deben preservarse en gestor seguro. | No hay prueba de restauración de identidades/CA/keystores. Perder la clave de metadatos puede impedir leer información cifrada; rotación/regeneración no sustituye conservar la clave asociada a los datos. |
| Releases y configuración | `acs-secure-repository`, `acs-search-community`, `acs-repository-text`, `traefik`, CR/manifiestos ECK, valores versionados, overlays y versiones fijadas. | Git conserva artefactos de configuración, no credenciales ni el estado persistido. Reconciliar versiones/charts y orden de reinstalación en un runbook. |
| Share forward / WSL | Unidad systemd de usuario para acceso local desde Windows a Share. | No es dato ECM, pero debe recrearse/validarse como configuración de acceso local; no representa disponibilidad remota. |
| Destino/retención y recuperación integral | Un nodo kind, volúmenes locales WSL; Repository una réplica. | No hay copia externa, política, cifrado/retención operativa, RPO/RTO ni restore integral aprobados o probados. No llamar recuperable a esta instalación. |

Trabajo requerido en el repo ECM: redactar primero un runbook independiente que coordine quietud del Repository y captura del par PostgreSQL/Content Store; definir destino separado; preservar de manera segura materiales de identidad/TLS/metadata; decidir snapshot Elasticsearch mediante la API nativa o reconstrucción y validar tiempos; fijar orden de ECK/ES, releases Repository/Search/text/Traefik y credenciales; finalmente restaurar en laboratorio aislado y comprobar login, metadatos, contenido sintético, transformaciones, búsqueda/reindexación, permisos, TLS y resultados. No modificar ese repositorio dentro de esta tarea ni trasladar a Control Center procedimientos paralelos. ACS Community 26.2 requiere coherencia de base de datos/`dir.root` y documenta el uso de snapshot/restore nativo para Elasticsearch/OpenSearch. La estrategia de la plataforma (snapshot o rebuild) aún necesita decisión y evidencia; no extrapolar instrucciones de Solr a esta composición.

### Contrato preliminar para Control Center

Control Center debe solicitar un perfil aprobado al procedimiento validado del entorno; no ejecutar `kubectl exec`, shell ni SQL arbitrarios desde una API genérica. El adaptador debe conocer que el ciclo operativo distribuye ACS, Search y endpoint TLS en releases diferentes y ES bajo ECK; no reportar un job exitoso por solo observar un release. Parámetros permitidos: `environmentId`, `backupProfileId`, destino registrado, política de retención aprobada e identificador de solicitud/idempotencia. No aceptar destinos, rutas o comandos libres.

Registro del trabajo: `jobId`, actor/entorno/perfil, estado (`queued`, `running`, `succeeded`, `failed`, `unknown`), `createdAt`, `startedAt`, `finishedAt`, etapa por componente, progreso solo si la herramienta lo mide, destino lógico redactado, checksum/manifiesto si se produce, resumen de errores seguro y referencia a la evidencia. El resultado del backup debe mantenerse distinto del resultado de una restauración. No marcar recuperable al terminar el proceso: requiere restauración aislada, coherencia DB/Content Store, validación de contenido/metadatos sintéticos, reindexación y comprobación funcional documentada.

## #12 — Refinamiento y sprint semanal propuesto

Las siete épicas siguen siendo capacidades coherentes y se conservan; #8–#11 continúan abiertas con sus resultados documentados. La recomendación de Kubernetes API de solo lectura sigue siendo válida, pero la versión del sprint que apuntaba a un único namespace/release y a `kind-acs-local` **requiere modificación**. La base actual `acs-independent` separa ACS, Search y endpoint TLS por releases, mantiene Elasticsearch bajo ECK y divide la observación entre `acs-secure`, `elastic-system` y `traefik`.

**Objetivo semanal revisado:** demostrar el catálogo y diagnóstico de solo lectura del stack ACS en `kind-acs-independent`, resolviendo cada servicio lógico al release/controlador, Service/EndpointSlice, Pod y contenedor correctos; mostrar señales independientes de salud/conectividad; y consultar logs acotados del servicio seleccionado.

**Historia candidata, pendiente de revisión/creación:** *HU: Diagnosticar componentes de ACS Community distribuidos entre releases Helm y ECK.* Como operador, quiero ver el estado de cada componente y consultar sus logs por servicio, para localizar fallos sin acceder a Secrets ni otorgar permisos de escritura.

**Criterios de aceptación:** (1) fija y verifica cluster/contexto `kind-acs-independent` y namespaces permitidos; falla cerrado ante identidad distinta; (2) catálogo configurado para Repository/proxy TLS, PostgreSQL, ActiveMQ, Share, transformadores, Search, Elasticsearch/ECK, ECK operator y Traefik, resolviendo selectores/workloads/Pods/containers con evidencia; (3) mantiene separados runtime, condiciones/probes, endpoints, salud del CR Elasticsearch, TLS/auth, timeout y frescura; (4) consulta `pods/log` del servicio/contenedor seleccionado con timestamp y límites; no exige combinar todos los logs; (5) distingue RBAC 403, autenticación 401, timeout, Pod/log inexistente, error TLS y fuente no disponible; (6) no lee Secrets, escribe recursos, ejecuta shell ni acepta selectores arbitrarios.

| Trabajo candidato (Issue nueva, pendiente) | Criterio y evidencia de aceptación | Dependencias/riesgos | Fuera de alcance |
|---|---|---|---|
| Tarea: contrato de catálogo y mapeo multiservicio | Tabla declarativa lógica→namespace/release/controlador/Service/selector/Pod/contenedor; cubre recursos ACS, Search, ECK y Traefik; se compara con captura saneada. | El Service de texto apunta al Pod Repository; ECK no tiene release Helm; sufijos de Pods cambian. No inferir servicio por nombre/release solamente. | No inventario dinámico arbitrario, no nodos cluster-wide, no crear servicios ni modificar ECM. |
| Tarea: PoC de salud/conectividad con permisos mínimos | Consulta Pods, Services, EndpointSlices, Deployments/StatefulSets, Ingress, events y CR ECK permitidos; separa probes Repository, CR salud Elasticsearch, Ready y fallo de acceso. Evidencia: `can-i`/respuestas con identidad restringida y casos 401/403/timeout/TLS. | Role/RoleBinding de lectura en `acs-secure`, `elastic-system`, `traefik`; identidad que el propietario habilite. La verificación de permisos del runbook para Elasticsearch no prueba RBAC Kubernetes. Variación inicial `No test` de readiness requiere aclarar semántica. | No aplicar Role ni tocar cluster en esta investigación; ningún Secret, escritura, `exec` o cambio TLS. |
| Tarea: consulta acotada de logs por servicio | Seleccionar cada servicio del catálogo y derivar Pods/containers desde el selector; consulta `pods/log` con timestamp, `sinceTime`, `tailLines`/`limitBytes`, procedencia y fallos diferenciados. Evidencia: resultado saneado por servicio, sin contenido de negocio almacenado. | `get` `pods/log` en namespaces permitidos; redacción/límites; Repository tiene dos contenedores; logs kubelet no son históricos y Search mostró warning de pull-secret. | No agregador central, retención histórica, filtro de severidad server-side, lectura de logs de negocio para esta investigación ni UI. |

**Capacidad y dependencias del sprint:** una semana, sin fecha ni puntos; sujeto a que el propietario confirme capacidad. Requiere una identidad Kubernetes restringida ya disponible o aprobación separada para prepararla, y un mapa de servicio revisado. Sin ello, la PoC RBAC/lectura autenticada queda bloqueada. El propietario revisa evidencia antes de cerrar las Issues. No asignar items ni marcar `Ready` hasta acordar capacidad e Iteration.

**Salida esperada:** catálogo de servicios, señales diferenciadas y logs recientes por servicio con origen/timestamp en `acs-independent`; prueba técnica sin navegador. Metrics queda fuera porque no hay API/fuente; backup/restore se difiere al runbook ECM y fase separada. No desplegar, reiniciar, actualizar, modificar TLS ni hacer UI tests. La ADR Kubernetes se redactará tras la PoC, no se aprueba con esta investigación.

## Riesgos y decisiones pendientes

1. Aclarar la variación de respuesta `No test`/`Tested` de readiness y acordar el significado de cada probe.
2. Definir identidad y Role/RoleBindings de solo lectura de Control Center para los tres namespaces; las pruebas de permisos ES no son RBAC Kubernetes.
3. Revisar el mapeo multiservicio entre release, Service, selector, workload, Pod y contenedor, incluido `repository-text-tls`.
4. Resolver de forma segura el evento `FailedToRetrieveImagePullSecret` de Search sin exponer el Secret.
5. Acordar frecuencia y máximo de frescura; no inventar umbral.
6. Mantener métricas fuera hasta que exista una fuente aprobada.
7. Crear un runbook de backup/restore específico para `acs-independent` en `alfresco-community-platform`; validar DB/Content Store, identidad/TLS, ECK/ES y Search en restauración aislada.
8. Definir destino independiente, retención, cifrado, RPO/RTO y estrategia/reindexación del índice antes de llamar recuperable al entorno.

## Fuentes

### Repositorios examinados el 2026-10-24

- [AGENTS.md de alfresco-community-platform](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/AGENTS.md)
- [Arquitectura](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/overview.md), [baseline](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/technology-baseline.md), [roadmap](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/project/roadmap.md)
- [Runbook acs-independent](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/acs-independent-kind.md), [reconstrucción y pruebas técnicas](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/rebuild-independent-kind.md), [ADR-0004](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/decisions/ADR-0004-independent-elasticsearch-lab.md)
- [Values independientes ACS](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-community/values-independent-kind.yaml), [values de seguridad](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-community/values-secure-lab.yaml), [values Search Community](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-search-community/values-secure-lab.yaml), [endpoint TLS de texto](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/helm/acs-community/values-repository-text-tls.yaml), [CR Elasticsearch](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/infrastructure/elasticsearch/elasticsearch-kind-independent.yaml)
- [Runbook histórico backup/restore de kind](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/operations/backup-restore-kind.md), [baseline](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/technology-baseline.md), [matriz de compatibilidad](https://github.com/AlejoAvila/alfresco-community-platform/blob/main/docs/architecture/compatibility-matrix.md)

### Referencias oficiales externas

- [Kubernetes: Pod lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/), [probes](https://kubernetes.io/docs/concepts/workloads/pods/probes/), [RBAC/seguridad](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/)
- [Kubernetes: `kubectl logs`](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/), [arquitectura de logging](https://kubernetes.io/docs/concepts/cluster-administration/logging/), [API concepts](https://kubernetes.io/docs/reference/using-api/api-concepts/)
- [ACS Deployment: Helm overview](https://alfresco.github.io/acs-deployment/docs/helm/README.html), [REST API probes del Repository chart](https://github.com/Alfresco/alfresco-helm-charts/blob/main/charts/alfresco-repository/README.md)
- [ACS Community 26.2: backup/restore del Repository](https://docs.hyland.com/r/Alfresco/Alfresco-Content-Services-Community-Edition/26.2/Alfresco-Content-Services-Community-Edition/Administer/Backup-and-migrate/Back-up-and-restore/Back-up-and-restore-repository?contentId=ANQOJqAJ5gH4IFSP2nvXRw), [backup caliente y estrategia Elasticsearch/OpenSearch](https://docs.hyland.com/r/Alfresco/Alfresco-Content-Services-Community-Edition/26.2/Alfresco-Content-Services-Community-Edition/Administer/Backup-and-migrate/Back-up-and-restore/Back-up-and-restore-repository/Perform-hot-backup?contentId=avzto6kJRIyFcMTG23Lw3g)
