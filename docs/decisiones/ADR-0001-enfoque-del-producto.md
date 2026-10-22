# ADR-0001: Límites iniciales de Vantia Platform Control Center

- **Estado:** Acordado como dirección inicial; arquitectura de ejecución pendiente de validar.
- **Fecha:** 2026-10-22.

## Contexto

Vantia necesita una consola independiente para supervisar y operar componentes de sus plataformas. El repositorio `alfresco-community-platform` gestiona el despliegue y validación de Alfresco Community, y documenta una arquitectura compuesta por varios servicios persistentes y no persistentes.

## Decisión

1. El Control Center tendrá un repositorio y ciclo de vida independientes.
2. Alfresco Community será el primer objetivo.
3. La aplicación separará su modelo de producto de los detalles particulares de cada runtime mediante contratos/adaptadores cuando la evidencia lo justifique.
4. La supervisión distinguirá estado del runtime, salud funcional, conectividad y antigüedad del dato.
5. Las operaciones privilegiadas tendrán autorización del lado servidor, validación, permisos mínimos, auditoría y manejo explícito de errores.
6. Los backups se coordinarán con procedimientos validados del proyecto ECM. No se considerará un snapshot aislado equivalente a un backup completo y recuperable sin pruebas.
7. El trabajo se gestionará ágilmente mediante GitHub Issues y Projects cuando su configuración esté disponible, complementados por documentación versionada.

## Consecuencias

- La integración inicial y el runtime objetivo deben elegirse antes de implementar adaptadores.
- Los mecanismos de conexión a entornos remotos y la necesidad de un agente quedan por validar.
- La autenticación, autorización, almacenamiento de configuración y persistencia del producto deben definirse antes de habilitar operaciones reales.
- La compatibilidad se declara solo para combinaciones probadas.
- Las épicas e historias iniciales son candidatas hasta que se creen y prioricen en GitHub.

## Alternativas consideradas

- Acoplar directamente el producto a nombres y comandos de una instalación: rechazado por portabilidad y mantenimiento.
- Dar acceso privilegiado directo desde navegador al runtime: rechazado por seguridad.
- Desarrollar todas las funciones operativas en el primer incremento: rechazado por riesgo y amplitud de alcance.
- Diseñar un agente universal desde el inicio: pendiente de evaluación; no se ha demostrado que sea necesario.

## Pendiente de validación

Primer entorno soportado, interfaces oficiales, modelo de permisos, fuentes de métricas y logs, operación de backups, framework de aplicación y despliegue del propio Control Center.
