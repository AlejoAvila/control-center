# Hoja de ruta inicial

La hoja de ruta es una propuesta basada en dependencias; el trabajo activo y los estados reales deben mantenerse en GitHub Issues/Projects. No asumir que el paso del tiempo o la documentación generada completan una fase.

## Fase 0 — Descubrimiento y definición

- [ ] Validar usuarios, escenarios y límites del producto.
- [ ] Revisar el baseline real de Alfresco Community y las interfaces soportadas.
- [ ] Elegir el primer entorno objetivo para la integración.
- [ ] Acordar modelo de amenazas inicial y restricciones de permisos.
- [ ] Preparar GitHub Projects, etiquetas, épicas e historias iniciales.

**Salida:** alcance del MVP revisado y backlog priorizado con criterios de aceptación.

## Fase 1 — Fundamento verificable

- [ ] Seleccionar stack con ADR, basado en requisitos, experiencia, soporte, costo y mantenibilidad.
- [ ] Establecer estructura de aplicación, configuración segura, autenticación inicial y estrategia de pruebas.
- [ ] Conectar a un entorno controlado de prueba mediante interfaces soportadas.
- [ ] Demostrar lectura de inventario y estado con timestamp y manejo de desconexión.

**Salida:** prueba reproducible de lectura para el primer entorno soportado.

## Fase 2 — Supervisión y diagnóstico

- [ ] Dashboard e inventario.
- [ ] Detalle de servicio y dependencias relevantes.
- [ ] Logs filtrables y eventos disponibles.
- [ ] Métricas cuando exista una fuente validada.
- [ ] Manejo de datos desconocidos, degradados y obsoletos.

**Salida:** operador puede identificar estado y diagnosticar un fallo de alcance definido.

## Fase 3 — Operaciones controladas

- [ ] Definir operaciones admitidas por tipo de servicio y runtime.
- [ ] Implementar autorización, confirmación y auditoría.
- [ ] Gestionar ejecución asíncrona, timeout y fallo parcial.
- [ ] Verificar salud después de cada operación.

**Salida:** acciones permitidas funcionan en un entorno de prueba y su evidencia queda registrada.

## Fase 4 — Backup y recuperación

- [ ] Integrar el procedimiento validado para un perfil de despliegue.
- [ ] Mostrar progreso, ubicación, retención y resultado.
- [ ] Verificar integridad y ejecutar una restauración en ambiente controlado.

**Salida:** evidencia documentada de backup y restauración para el perfil específico; no se infiere soporte universal.

## Fase 5 — Consolidación y expansión

- [ ] Actualizaciones controladas con prevalidaciones y estrategia de rollback.
- [ ] Revisión de seguridad, carga, auditoría, observabilidad y operación del propio producto.
- [ ] Evaluar adaptadores adicionales (Docker/Compose u otros runtimes).
- [ ] Evaluar integración de más componentes, incluido Flowable, cuando su papel y capacidades estén confirmados.

**Salida:** nuevas capacidades soportadas mediante criterios y pruebas explícitas.

## Regla de cambio

Cada cambio de alcance importante debe actualizar el backlog y, si afecta a arquitectura transversal, el repositorio de dirección. Registrar riesgos, dependencias y evidencias.
