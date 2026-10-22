# Requisitos iniciales

**Estado:** propuesta para refinamiento; no equivale a alcance comprometido del MVP.

## Requisitos funcionales candidatos

| ID | Requisito | Prioridad inicial | Estado |
|---|---|---|---|
| RF-01 | Registrar y seleccionar entornos administrados y mostrar claramente el entorno activo | Alta | Propuesto |
| RF-02 | Mostrar inventario de servicios y estado del runtime | Alta | Propuesto |
| RF-03 | Consultar health checks, última comprobación y distinguir estado degradado o datos obsoletos | Alta | Propuesto |
| RF-04 | Ver el detalle de un servicio y sus dependencias conocidas | Alta | Propuesto |
| RF-05 | Consultar y filtrar logs de los servicios soportados | Alta | Propuesto |
| RF-06 | Consultar métricas disponibles con fuente y ventana temporal claras | Media | Por validar |
| RF-07 | Ejecutar reinicio, inicio o parada cuando el adaptador y permisos lo permitan | Media | Propuesto |
| RF-08 | Confirmar operaciones de impacto, mostrar progreso y registrar resultado | Alta para operaciones | Propuesto |
| RF-09 | Mantener historial auditable de acciones administrativas | Alta | Propuesto |
| RF-10 | Ejecutar y seguir trabajos de backup compatibles con el entorno | Alta para etapa de backup | Propuesto |
| RF-11 | Verificar integridad y documentar pruebas de restauración | Alta para etapa de recuperación | Propuesto |
| RF-12 | Administrar roles, permisos y credenciales/conectores sin exponer secretos | Alta antes de operaciones reales | Propuesto |
| RF-13 | Actualizar servicios de forma controlada con prevalidación y estrategia de recuperación | Futuro | Propuesto |
| RF-14 | Extender el inventario mediante adaptadores para otros componentes y runtimes verificados | Futuro | Propuesto |

## Requisitos no funcionales candidatos

- **Seguridad:** autenticación, autorización por entorno y acción, mínimo privilegio, gestión de secretos, auditoría y protección contra CSRF/SSRF/inyección según el stack elegido.
- **Aislamiento:** no exponer sockets o credenciales privilegiadas al navegador; separar la interfaz del plano de administración.
- **Fiabilidad:** diferenciar dato desconocido, desconectado, obsoleto, degradado y saludable; no inferir salud únicamente desde el estado del contenedor.
- **Trazabilidad:** registrar actor, objetivo, hora, operación, parámetros no sensibles, estado, duración y resultado.
- **Portabilidad:** evitar dependencias innecesarias de un proveedor cloud, host o nombre de contenedor.
- **Operabilidad:** instalación, configuración, actualización, backup, logging y diagnóstico documentados.
- **Usabilidad:** mostrar contexto de entorno, estado, actualización más reciente y consecuencias de una acción.
- **Mantenibilidad:** contratos de integración y adaptadores comprobables mediante pruebas.
- **Recuperación:** no declarar respaldos válidos sin comprobarlos y realizar pruebas de restauración acordes al alcance.

## Preguntas abiertas

1. ¿Qué entorno de despliegue se soportará primero: Kubernetes mediante el clúster local ya documentado u otro perfil?
2. ¿Cómo se registran entornos remotos y cómo se aprovisionan credenciales con mínimo privilegio?
3. ¿Hace falta un agente/conector separado o basta con integración directa a APIs soportadas en la primera fase?
4. ¿Qué fuentes de métricas existen realmente en el baseline ECM?
5. ¿Qué mecanismo de backup y restauración está disponible y validado hoy?
6. ¿Qué roles y niveles de confirmación requieren las operaciones?
7. ¿Cuál será el alcance del MVP tras el refinamiento?

## Criterio de prioridad

Priorizar por valor operativo, riesgo, dependencias, esfuerzo de validación y posibilidad de demostrar el resultado. Los requisitos deben convertirse en historias con criterios de aceptación comprobables.
