## COMMAND HANDLERS

Aspecto	Explicación
¿Qué es?	Procesa un Command, ejecuta la lógica de aplicación y delega al dominio.
Responsabilidad	1. Recibe el Command
2. Recupera o crea el Aggregate
3. Ejecuta métodos del dominio
4. Persiste el Aggregate
5. Publica eventos
Cuándo usarlo	Para cada Command que modifica el estado del sistema.
Cuándo NO usarlo	Para consultas (usa Query Handlers).

## QUERY HANDLERS
Aspecto	Explicación
¿Qué es?	Procesa una Query, recupera datos y los retorna sin modificar el estado.
Responsabilidad	1. Recibe la Query
2. Recupera datos del Repository
3. Transforma a DTO
4. Retorna los datos
Cuándo usarlo	Para cada Query que consulta información.
Cuándo NO usarlo	Para operaciones de escritura (usa Command Handlers).

## DTOS (Data Transfer Objects)
Aspecto	Explicación
¿Qué es?	Objeto que transporta datos entre capas (Application → Presentation).
Responsabilidad	1. Transferir datos sin exponer el dominio
2. Transformar desde/hacia el dominio
3. Reducir el acoplamiento
Cuándo usarlo	Siempre que se expongan datos a la capa de presentación.
Cuándo NO usarlo	Si la capa de presentación tiene acceso directo al dominio (no recomendado).

## DOMAIN EVENT PUBLISHER
Aspecto	Explicación
¿Qué es?	Publica eventos de dominio al Message Broker para comunicación asíncrona.
Responsabilidad	1. Publicar eventos a RabbitMQ/Kafka
2. Manejar retry y errores
Cuándo usarlo	Siempre que se necesite comunicación asíncrona entre microservicios.
Cuándo NO usarlo	En sistemas monolíticos sin necesidad de eventos.

## DOMAIN EVENT LISTENER
Aspecto	Explicación
¿Qué es?	Escucha eventos de dominio y ejecuta acciones secundarias.
Responsabilidad	1. Reaccionar a eventos
2. Ejecutar acciones secundarias (notificaciones, logs)
Cuándo usarlo	Cuando necesitas reaccionar a eventos sin modificar el dominio.
Cuándo NO usarlo	Si la reacción es parte del dominio (usar métodos del Aggregate).

## APPLICATION SERVICE (Opcional)
Aspecto	Explicación
¿Qué es?	Orquesta casos de uso complejos que involucran múltiples pasos.
Responsabilidad	1. Coordinar múltiples operaciones
2. Manejar transacciones complejas
3. Integrar con servicios externos
Cuándo usarlo	Cuando un caso de uso involucra múltiples Aggregates o pasos complejos.
Cuándo NO usarlo	Para operaciones simples (usa Command Handlers directamente).

## EXTERNAL SERVICE
Aspecto	Explicación
¿Qué es?	Interfaz para integración con sistemas externos.
Responsabilidad	1. Abstraer la comunicación con sistemas externos
2. Manejar conversión de datos
Cuándo usarlo	Cuando se necesita comunicarse con sistemas externos.
Cuándo NO usarlo	Para operaciones internas del dominio.


## 📊 Matriz de decisión - Application Layer (Versión Mínima)

|Elemento	|¿Es obligatorio?	|¿Cuándo incluirlo?	|¿Cuándo NO incluirlo?|
|-|-|-|-|
|Command Handlers|	✅ Sí|	Para cada Command de escritura	|No hay operaciones de escritura|
|Query Handlers|	✅ Sí	|Para cada Query de lectura	|No hay operaciones de lectura|
|DTOs|	✅ Sí|	Para exponer datos a la capa de presentación	|Sistema muy simple sin capa de presentación|
|Event Publisher|	✅ Sí	|Hay comunicación asíncrona entre microservicios	|Sistema monolítico sin eventos|
|Event Listener|	❌ Opcional	|Se necesitan acciones secundarias a eventos	|No hay acciones secundarias|
|Application Service	|❌ Opcional|	Casos de uso complejos con múltiples pasos	|Operaciones simples (solo Handler)|
|External Service	|❌ Opcional|	Se integra con sistemas externos|	No hay integraciones externas|

## 📝 Justificación para tu PPT (Application Layer - Versión Mínima)

"El diagrama de clases de la capa de Aplicación representa el orquestamiento de los casos de uso del Bounded Context, siguiendo el patrón CQRS para separar responsabilidades de lectura y escritura.

Command Handlers: Procesan los Commands de escritura (Crear, Aprobar, Rechazar). Cada Handler recibe un Command, recupera o crea el Aggregate, ejecuta métodos del dominio, persiste a través del Repository y publica eventos de dominio. Esta separación mantiene el dominio limpio y desacoplado.

Query Handlers: Procesan las Queries de lectura (Obtener, Listar). Recuperan datos del Repository y los transforman a DTOs para su consumo por la capa de presentación. No modifican el estado del sistema.

DTOs: SampleAggregateDTO aísla la capa de presentación del dominio, transformando Aggregates en objetos planos serializables.

Domain Event Publisher: Publica eventos de dominio al Message Broker (RabbitMQ/Kafka) para comunicación asíncrona entre microservicios, permitiendo trazabilidad y desacoplamiento.

Event Listeners: Escuchan eventos de dominio y ejecutan acciones secundarias como envío de notificaciones o registros de auditoría.

Application Service (Opcional): Orquesta casos de uso complejos que involucran múltiples pasos o integraciones con servicios externos como SITEDS.

External Service: Abstrae la comunicación con sistemas externos mediante interfaces que permiten cambiar implementaciones sin afectar la lógica de negocio.

Esta arquitectura sigue el patrón CQRS, separando claramente las responsabilidades de lectura y escritura, y mantiene el dominio desacoplado de la infraestructura mediante el uso de interfaces y DTOs."

## Diferencias clave: Domain Layer vs Application Layer

|Aspecto	|Domain Layer	|Application Layer|
|-|-|-|
|Propósito|	Contiene la lógica de negocio pura	|Orquesta casos de uso|
|Dependencias|	No depende de nada externo	|Depende de Domain y de infraestructura|
|Elementos|	Aggregates, VOs, Enums, Events, Repository interfaces	|Handlers, DTOs, Services, Event Publishers, Listeners|
|Contiene reglas de negocio	|✅ Sí	|❌ No (solo orquesta)|
|Testabilidad	|Muy fácil (sin dependencias externas)|	Más complejo (requiere mocks)|