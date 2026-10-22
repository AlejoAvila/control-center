# Vantia Platform Control Center

## Propósito

Vantia Platform Control Center será una aplicación web independiente para supervisar y administrar, de forma segura y trazable, servicios que componen las plataformas de Vantia. El primer objetivo es gestionar entornos basados en Alfresco Community; la incorporación de Flowable y otros componentes se evaluará después.

**Estado actual:** definición inicial del producto y preparación del trabajo. La documentación no demuestra que la aplicación esté implementada, desplegada ni validada.

## Objetivos iniciales

- Consultar inventario, salud, disponibilidad y estado operativo de servicios.
- Inspeccionar detalles, logs, métricas y eventos.
- Ejecutar operaciones autorizadas de ciclo de vida cuando el entorno lo permita.
- Coordinar y consultar trabajos de respaldo, con verificación y restauración comprobadas.
- Preparar una arquitectura extensible para entornos de contenedores compatibles.

## Límites del proyecto

Este repositorio contiene el producto de control y gestión operativa. No reemplaza ni absorbe el proyecto de plataforma ECM.

- [Alfresco Community Platform](https://github.com/AlejoAvila/alfresco-community-platform): despliegue, configuración, validación, persistencia y procedimientos de operación/recuperación de Alfresco Community.
- [Flowable Community Platform](https://github.com/AlejoAvila/flowable-community-platform): evaluación y validación del motor BPM y sus capacidades.
- [Dirección de soluciones](https://github.com/AlejoAvila/solution-platform-direction): estrategia y arquitectura transversal de Vantia.

## Documentación

- [Visión del producto](docs/vision-del-producto.md)
- [Requisitos iniciales](docs/requisitos.md)
- [Arquitectura conceptual](docs/arquitectura.md)
- [Modelo de seguridad](docs/modelo-de-seguridad.md)
- [Hoja de ruta](docs/roadmap.md)
- [Gestión ágil](docs/gestion-agil.md)
- [Backlog inicial](docs/backlog-inicial.md)
- [Decisiones arquitectónicas](docs/decisiones/)

## Gestión del trabajo

El backlog oficial debe gestionarse mediante GitHub Issues y GitHub Projects cuando estén configurados. La documentación versionada explica la dirección y enlaza al trabajo activo; no debe convertirse en una copia desactualizada de cada issue.

La metodología será ágil y pragmática: épicas, historias de usuario, tareas, defectos, sprints, revisiones y criterios de terminado. No se considerará una tarea completada sin la evidencia correspondiente.

## Principios de trabajo

1. Open source y autogestionado primero cuando cumpla los requisitos y el costo total resulte conveniente.
2. Integrar mediante interfaces documentadas y soportadas, no mediante tablas internas de componentes.
3. Diseñar para portabilidad, pero declarar soporte solo para combinaciones verificadas.
4. Seguridad, mínimo privilegio, auditoría y recuperación son requisitos de diseño.
5. No dar por probadas funciones por estar documentadas o planeadas.
