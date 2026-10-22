# AGENTS.md — Vantia Platform Control Center

## Misión

Construir una aplicación web independiente para supervisar y administrar de manera segura y trazable los entornos de Vantia. El primer alcance se centra en servicios de Alfresco Community. El proyecto debe mantenerse extensible sin asumir compatibilidad universal ni cerrar prematuramente decisiones técnicas.

## Idioma y comunicación

- Mantén la conversación, requisitos y documentación en español técnico claro.
- Conserva identificadores, comandos, nombres de APIs, propiedades y código en su formato literal cuando sea necesario.
- Explica las decisiones, alternativas, evidencias, riesgos y pendientes de manera explícita.

## Límites y fuentes de contexto

- Este repositorio es responsable del producto Control Center, no de la instalación genérica de Alfresco.
- Consulta el repositorio `alfresco-community-platform` y su documentación relevante antes de decidir cómo operar sus servicios.
- No modifiques otros repositorios salvo instrucción explícita.
- La dirección transversal se gobierna desde `solution-platform-direction`; comunica allí cualquier decisión que cambie la arquitectura de Vantia.
- No asumir que Flowable es el motor BPM definitivo; su evaluación sigue abierta.

## Reglas de ingeniería

- Antes de cambiar archivos, inspecciona el estado del repositorio, rama, remoto y diff. Conserva cambios existentes.
- No inventes APIs, capacidades, versiones, licencias, compatibilidades ni resultados de prueba.
- Distingue lo confirmado oficialmente, lo verificado en artefactos, lo probado, lo propuesto y lo pendiente.
- Prioriza interfaces soportadas. No integres mediante tablas internas de terceros.
- Nunca expongas directamente al navegador interfaces privilegiadas de Docker o Kubernetes.
- No permitas comandos arbitrarios ni concedas permisos de runtime más amplios de los necesarios.
- Aplica mínimo privilegio, autenticación, autorización, auditoría y validación de entradas.
- Los reinicios, paradas, actualizaciones, backups y restauraciones requieren control de impacto, manejo de errores y registro de resultados.
- No guardes secretos reales, claves, tokens, certificados privados o archivos de entorno con valores sensibles en Git.
- No declares un backup recuperable sin evidencia de restauración satisfactoria.
- Un contenedor en ejecución no demuestra que la aplicación esté saludable.
- No declares producción lista por el mero hecho de superar una prueba local.

## Método de trabajo

- Usar una metodología ágil pragmática, gestionada principalmente con GitHub Issues y Projects.
- Mantener épicas, historias, tareas técnicas, defectos, sprints, criterios de aceptación y evidencia trazables.
- Inspeccionar Issues, Projects, labels y milestones existentes antes de crear nuevos elementos; evitar duplicados.
- Definir objetivo de sprint y criterios de salida antes de implementarlo.
- Para decisiones significativas, registrar un ADR con contexto, alternativas, criterios, decisión, consecuencias y pendientes.
- Trabajar incrementalmente y revisar diffs antes de confirmar cambios.
- No hacer despliegues, operaciones destructivas ni cambios de infraestructura sin que la tarea los solicite y el impacto sea claro.

## Criterios de finalización

Reportar qué cambió, cómo se validó, evidencia obtenida, riesgos y pendientes. Mantener coherentes código, documentación y GitHub. No marcar tareas como terminadas si solo están documentadas o propuestas.
