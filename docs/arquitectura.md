# Arquitectura conceptual

**Estado:** propuesta inicial; no es una arquitectura implementada ni validada.

## Contexto técnico de referencia

La documentación de `alfresco-community-platform` plantea ACS Community con componentes separados y un despliegue orientado a contenedores/Kubernetes. Registra `kind` como laboratorio local, K3s como default económico inicial on-premise y alternativas para cloud. La versión exacta de ACS, el chart y la combinación completa de componentes deben revisarse en la documentación vigente del proyecto antes de implementar la integración.

## Componentes lógicos candidatos

- **Interfaz web:** dashboard, selección de entorno, inventario, detalle de servicio, logs, métricas, trabajos e historial.
- **API del Control Center:** aplica autorización, valida solicitudes y entrega contratos estables a la interfaz.
- **Inventario de entornos y servicios:** conserva identificadores, capacidades declaradas y estado de conexión; no guarda secretos directamente como texto.
- **Motor de comprobaciones:** recoge datos de runtime y salud del servicio, registrando timestamp y origen.
- **Capa de adaptadores:** traduce las operaciones del producto a los mecanismos de administración concretos y soportados.
- **Coordinador de trabajos:** gestiona operaciones asíncronas como backups y actualizaciones, incluidos estados, timeouts y resultados.
- **Auditoría y eventos:** mantiene trazabilidad de operaciones y eventos relevantes.
- **Almacenamiento de configuración y estado:** su motor y esquema se decidirán mediante evaluación, evitando mezclarlo con las bases internas de Alfresco.

## Flujo conceptual

1. El administrador se autentica y selecciona un entorno.
2. La API verifica que puede consultar ese entorno y la acción solicitada.
3. El adaptador obtiene el estado mediante una interfaz soportada.
4. El producto distingue estado de ejecución, salud de aplicación, conectividad y antigüedad del dato.
5. Para acciones de escritura, el backend valida la autorización, los pre-requisitos y el nivel de impacto.
6. Los trabajos largos se ejecutan de manera asíncrona cuando corresponda y registran su progreso y resultado.
7. La auditoría registra la operación sin incluir credenciales ni secretos.

## Adaptadores y conectividad

Evaluar Kubernetes como primera integración si coincide con el entorno ECM que se puede probar. Definir permisos RBAC específicos para lectura y para las operaciones autorizadas. No asumir acceso de administrador al clúster.

Docker Engine/Compose quedan como candidatos a integrar después. No exponer un socket de Docker directamente al navegador ni crear un endpoint de ejecución arbitraria de comandos.

La opción de un agente/conector remoto está por validar. Comparar facilidad de despliegue, conectividad saliente/entrante, rotación de credenciales, aislamiento, actualización y auditoría.

## Salud y estado

Mantener estados distintos y semánticas documentadas, por ejemplo:
- runtime running/stopped/unknown;
- aplicación healthy/degraded/unhealthy/unknown;
- conexión disponible/no disponible;
- dato reciente/obsoleto.

Los nombres finales, transiciones y umbrales deberán definirse con base en fuentes disponibles. No declarar healthy si solo se ha comprobado que el contenedor existe.

## Backups

El Control Center debe orquestar y mostrar resultados de un procedimiento validado por tipo de despliegue. Debe diferenciar PostgreSQL, Content Store, búsqueda y otras persistencias relevantes. La estrategia de snapshot o reconstrucción de índice debe coincidir con la definida por la plataforma ECM. No inventar comandos ni asumir consistencia transaccional de una captura de volumen.

## Preguntas arquitectónicas pendientes

- Entorno inicial y método de conexión.
- Frameworks y lenguaje para frontend/backend.
- Necesidad y despliegue de un agente.
- Identidad, autorización por entorno y almacenamiento de credenciales.
- Fuente de métricas.
- Persistencia del catálogo, configuración, trabajos y auditoría.
- Ejecución y concurrencia de trabajos.
- Diseño de backup y restore por combinación soportada.
- Estrategia de despliegue y recuperación del propio Control Center.
