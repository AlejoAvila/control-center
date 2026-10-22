# Visión del producto

## Problema

Administrar una plataforma de contenedores y sus componentes técnicos exige conocer el estado real de cada servicio, diagnosticar fallos y ejecutar operaciones de forma segura. Una consola útil debe combinar el estado del runtime con comprobaciones de salud de la aplicación y ofrecer trazabilidad de las acciones administrativas.

## Visión

Vantia Platform Control Center será una consola web centralizada para observar y gestionar entornos de servicios de Vantia. Comenzará con una integración validada con Alfresco Community y podrá incorporar más componentes y runtimes mediante interfaces explícitas.

## Usuarios iniciales

- **Administrador de plataforma:** registra entornos, revisa el estado, configura accesos y ejecuta operaciones autorizadas.
- **Operador técnico:** vigila servicios, investiga incidencias mediante logs, métricas y eventos, y ejecuta acciones dentro de sus permisos.
- **Responsable de operación:** consulta historial, resultados de trabajos, respaldos y evidencia operativa.

Estos perfiles son hipótesis iniciales que deberán confirmarse con escenarios de uso.

## Valor esperado

- Visibilidad consolidada del estado y la salud.
- Diagnóstico operativo en un punto de acceso.
- Operaciones privilegiadas controladas, auditables y con retroalimentación.
- Ejecución y seguimiento de respaldos, con verificación de integridad y restauración.
- Menor dependencia de scripts particulares y de detalles de una instalación concreta.

## Capacidades previstas

1. Inventario y selección de entornos.
2. Estado global y detalle por servicio.
3. Logs, eventos y métricas.
4. Operaciones controladas de ciclo de vida.
5. Trabajos asíncronos e historial.
6. Respaldo, validación y recuperación.
7. Administración de identidad, permisos y auditoría.
8. Actualización controlada de componentes.
9. Extensión a otros productos y runtimes validados.

## Alcance inicial recomendado

Definir y probar una integración inicial sobre el entorno de Alfresco Community documentado en `alfresco-community-platform`. La primera capacidad demostrable debe cubrir inventario y estado fiable; el orden final se revisará durante el refinamiento del backlog.

## No objetivos iniciales

- Reemplazar Kubernetes, Docker, Helm ni las herramientas de despliegue existentes.
- Reimplementar la plataforma ECM o su lógica de negocio.
- Soportar universalmente cualquier versión, producto o runtime sin validación.
- Exponer interfaces privilegiadas del runtime directamente al navegador.
- Permitir ejecución arbitraria de comandos desde la interfaz.
- Tratar un snapshot de volumen como respaldo consistente de toda la plataforma sin pruebas.
- Implementar de inmediato todos los módulos sugeridos de navegación.
- Seleccionar de antemano un framework frontend/backend sin comparar requisitos.

## Medidas de éxito a definir

En el descubrimiento se deben fijar medidas verificables, por ejemplo: proporción de servicios con estado válido, latencia de actualización del estado, tiempo para identificar una falla, tasa de éxito de operaciones autorizadas, integridad de auditoría y porcentaje de restauraciones de prueba satisfactorias. No fijar objetivos numéricos sin una línea base.
