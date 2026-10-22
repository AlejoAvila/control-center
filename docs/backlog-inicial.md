# Backlog inicial propuesto

Este documento es un resumen para orientar el refinamiento. GitHub Issues y GitHub Projects serán el registro del trabajo activo una vez configurados. Los elementos que aparecen aquí no están completados ni crean automáticamente issues.

## Épicas candidatas

### Épica E1 — Fundamentos del producto y gestión de entornos
**Objetivo:** establecer la base, registrar entornos y conocer la conectividad al sistema administrado.

Historias candidatas:
- Como administrador, quiero registrar un entorno con nombre y tipo, para identificar qué plataforma estoy administrando.
- Como administrador, quiero seleccionar el entorno activo y verlo siempre en pantalla, para evitar operar accidentalmente sobre el entorno incorrecto.
- Como operador, quiero saber si el Control Center puede conectarse al entorno, para diferenciar una caída de servicio de una falla de comunicación.

### Épica E2 — Inventario y salud de servicios
**Objetivo:** obtener una vista fiable del estado de cada servicio soportado.

Historias candidatas:
- Como operador, quiero ver el inventario de servicios del entorno, para saber qué componentes están presentes.
- Como operador, quiero distinguir runtime, salud funcional y última comprobación, para detectar servicios degradados y datos obsoletos.
- Como operador, quiero consultar el detalle y dependencias relevantes de un servicio, para comprender su situación.

### Épica E3 — Diagnóstico operativo
**Objetivo:** facilitar la investigación de problemas con información del entorno y sus servicios.

Historias candidatas:
- Como operador, quiero consultar logs de un servicio y acotar el rango temporal cuando la fuente lo soporte, para investigar un incidente.
- Como operador, quiero consultar eventos e historial, para entender qué cambió antes de una falla.
- Como operador, quiero ver métricas con su fuente y rango temporal, para interpretar la situación correctamente.

### Épica E4 — Operaciones controladas
**Objetivo:** permitir acciones explícitas, autorizadas y trazables.

Historias candidatas:
- Como administrador autorizado, quiero reiniciar un servicio que lo permita, para recuperar una instancia con problemas.
- Como administrador, quiero ver el impacto y confirmar una operación disruptiva, para evitar errores operativos.
- Como responsable, quiero consultar el resultado y auditoría de una operación, para comprobar qué ocurrió.

### Épica E5 — Backups y recuperación
**Objetivo:** coordinar respaldos compatibles y evidenciar que son verificables y recuperables.

Historias candidatas:
- Como operador, quiero iniciar un backup mediante un procedimiento validado, para proteger los datos del entorno.
- Como operador, quiero seguir el estado y resultado del backup, para detectar fallas.
- Como responsable, quiero comprobar la integridad del backup y registrar una restauración de prueba, para disponer de evidencia de recuperación.

### Épica E6 — Seguridad, identidad y auditoría
**Objetivo:** limitar y registrar el acceso a los entornos y operaciones.

Historias candidatas:
- Como administrador, quiero asignar permisos diferenciados de lectura y operación, para aplicar mínimo privilegio.
- Como responsable de seguridad, quiero que las acciones de impacto queden auditadas sin registrar secretos, para disponer de trazabilidad.

### Épica E7 — Actualizaciones controladas y extensibilidad
**Objetivo:** preparar cambios versionados y añadir componentes mediante integraciones verificadas.

Historias candidatas:
- Como administrador, quiero conocer la versión desplegada y la candidata a actualización cuando la fuente sea fiable, para planificar cambios.
- Como responsable técnico, quiero incorporar adaptadores comprobables, para ampliar el soporte sin acoplar la UI a cada runtime.

## Recomendación para el primer sprint

No comprometer todavía todas las historias anteriores. Tras revisar la arquitectura, delimitar el primer entorno y decidir el mecanismo de integración, seleccionar un sprint pequeño que entregue una prueba reproducible de lectura del inventario y del estado de servicios, con timestamp y manejo explícito de desconexión.

El sprint debe priorizar el objetivo y los criterios de aceptación, no la cantidad de issues. Su duración, estimación y fechas se deben acordar al planificar.

## Pendientes de refinamiento

- Seleccionar primer runtime y entorno de prueba.
- Confirmar fuentes de health checks, inventario y logs.
- Definir si se requiere agente remoto.
- Definir identidad y permisos iniciales.
- Confirmar estrategia de datos y configuración.
- Crear issues reales y vincularlos a un GitHub Project una vez inspeccionados los recursos existentes.
