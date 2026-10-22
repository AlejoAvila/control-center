# Modelo de seguridad propuesto

**Estado:** principios y riesgos iniciales; mecanismos concretos pendientes de diseño y prueba.

## Principios

1. Mínimo privilegio por usuario, entorno, componente y operación.
2. Separación entre permisos de lectura y de modificación.
3. No exponer secretos ni interfaces privilegiadas de contenedores al navegador.
4. Validar cada solicitud en el backend incluso si la interfaz oculta la acción.
5. Auditar acciones de impacto y su resultado.
6. Proteger el canal de comunicación con cada entorno y gestionar rotación/revocación de credenciales.
7. Mostrar claramente el entorno objetivo antes de confirmar una operación disruptiva.
8. Manejar los registros para que no incluyan tokens, contraseñas u otros datos sensibles.

## Riesgos a estudiar

- Acceso directo o indirecto al socket/API de Docker con privilegios excesivos.
- Credenciales Kubernetes con permisos de clúster global cuando bastan permisos acotados.
- Confusión del usuario entre entornos (por ejemplo, producción y laboratorio).
- SSRF o acceso a endpoints internos al registrar entornos.
- Inyección de parámetros en comandos, filtros de logs o integraciones.
- CSRF, XSS, robo de sesión y debilidades de control de acceso.
- Repetición accidental de operaciones, doble envío o reintentos inseguros.
- Exposición de logs, variables de entorno o contenido de respaldo.
- Corrupción o falta de consistencia durante una operación de backup/restore.

## Requisitos mínimos para operaciones privilegiadas

- Autenticación y autorización verificadas en el servidor.
- Lista explícita de operaciones permitidas; no usar una API genérica de comandos.
- Validación del entorno, servicio, parámetros y precondiciones.
- Confirmación acorde al impacto.
- Identificador de operación y registro de actor, objetivo, tiempo, estado y resultado.
- Manejo explícito de timeout, fallo parcial y verificación posterior.
- Secretos almacenados con mecanismo adecuado y nunca impresos en logs.
- Permisos revocables y procedimiento operativo de rotación.

## Pendiente

Seleccionar mecanismo de identidad, modelo de roles, origen y almacenamiento de credenciales, transporte seguro, política de retención de auditoría y modelo de separación entre el Control Center y los entornos administrados.
