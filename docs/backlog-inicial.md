# Backlog inicial

Este documento resume la dirección del backlog. El trabajo activo se registra en GitHub Issues y debe organizarse en GitHub Projects cuando el tablero esté disponible. Las propuestas documentales no se consideran terminadas hasta contar con evidencia.

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

## Primer sprint: recomendación provisional

No comprometer todavía todas las historias anteriores. Primero completar la investigación de #8 y #9, resolver las dependencias técnicas mínimas y acordar el entorno de prueba. El objetivo candidato del primer sprint es demostrar lectura reproducible del inventario y del estado, con marcas temporales y manejo explícito de desconexión.

El sprint, sus historias, duración y fechas deberán acordarse al refinar el backlog. No se deben inventar fechas ni marcar tareas como completadas por el simple hecho de estar documentadas.

## Pendientes de gestión

- Configurar y verificar un GitHub Project para el seguimiento por estado, prioridad, épica e iteración. La documentación del repositorio no demuestra que exista un tablero.
- Revisar etiquetas y campos disponibles, reutilizando los existentes cuando sea posible.
- Refinar historias y criterios de aceptación en Issues después de los resultados de los spikes.
- Sincronizar de forma segura el repositorio remoto con la carpeta local de Windows tras inspeccionar su contenido. No sobrescribir archivos locales ni forzar el historial remoto.
