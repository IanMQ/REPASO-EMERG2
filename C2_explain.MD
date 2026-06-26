# 📝 Justificación para tu PPT
## Justificación - Domain Layer

"El diagrama de clases del Domain Layer representa el Bounded Context 'Gestión de Exposiciones Virtuales' para el sistema Digital Heritage Explorer de Google Arts & Culture, desarrollado con Spring Boot y siguiendo los principios de Domain-Driven Design (DDD) con CQRS.

Bounded Context Seleccionado: Se ha identificado 'Gestión de Exposiciones Virtuales' como el Bounded Context con mayor relación con la creación y administración de exposiciones, ya que es el núcleo del sistema y contiene las reglas de negocio más críticas del dominio.

Aggregate Root: Se ha seleccionado ExposicionVirtual como el Aggregate Root principal porque es la entidad que garantiza la consistencia transaccional de todo el proceso de gestión de exposiciones. Encapsula las reglas de negocio más importantes:

Una exposición solo puede publicarse si tiene al menos una sección

Una exposición publicada no puede modificarse

El estado de la exposición sigue un ciclo de vida definido (BORRADOR → EN_REVISION → PUBLICADA → ARCHIVADA)

Entities: Seccion es una entidad hija que tiene identidad propia (SeccionId) y puede existir dentro del contexto de una exposición. Cada sección contiene una lista de elementos multimedia.

Value Objects: Se utilizan Value Objects para conceptos del dominio sin identidad propia:

ExposicionId, CuradorId, InstitucionId, SeccionId — identificadores tipados

Multimedia — encapsula el tipo, URL, título, descripción y geolocalización del contenido

Enums: EstadoExposicion define el ciclo de vida (BORRADOR → EN_REVISION → PUBLICADA → ARCHIVADA) y TipoMultimedia clasifica los tipos de contenido (IMAGEN, AUDIO, VIDEO, MODELO_3D, TEXTO).

Commands y Queries (CQRS): Se separan las operaciones de escritura (CrearExposicionCommand, AgregarSeccionCommand, PublicarExposicionCommand, ArchivarExposicionCommand) de las de lectura (ObtenerExposicionQuery, ListarExposicionesPorCuradorQuery, ListarExposicionesPorEstadoQuery).

Domain Events: ExposicionCreadaEvent, ExposicionPublicadaEvent, ExposicionArchivadaEvent, SeccionAgregadaEvent permiten trazabilidad y comunicación asíncrona entre módulos del Modular Monolith.

Repository: ExposicionVirtualRepository es una interfaz que abstrae la persistencia del Aggregate Root.

Nomenclatura: Se utilizó nomenclatura en inglés con Upper-Camel-Case para clases y lower-Camel-Case para atributos y métodos, siguiendo las convenciones de Java y Spring Boot."

## Justificación - Application Layer

"El diagrama de clases del Application Layer representa el orquestamiento de los casos de uso del Bounded Context 'Gestión de Exposiciones Virtuales', siguiendo el patrón CQRS para separar responsabilidades de lectura y escritura.

Command Handlers: Procesan los Commands de escritura (CrearExposicionHandler, AgregarSeccionHandler, PublicarExposicionHandler, ArchivarExposicionHandler). Cada Handler recibe un Command, recupera o crea el Aggregate, ejecuta métodos del dominio, persiste a través del Repository y publica eventos de dominio.

Query Handlers: Procesan las Queries de lectura (ObtenerExposicionHandler, ListarExposicionesPorCuradorHandler, ListarExposicionesPorEstadoHandler). Recuperan datos del Repository y los transforman a DTOs para su consumo por la capa de presentación.

DTOs: ExposicionVirtualDTO, SeccionDTO y MultimediaDTO aíslan la capa de presentación del dominio, transformando Aggregates en objetos planos serializables.

Domain Event Publisher: Publica eventos de dominio para comunicación interna entre módulos del Modular Monolith.

Event Listeners: ExposicionEventListener escucha eventos de dominio y ejecuta acciones secundarias como notificaciones al curador y comunicación con el sistema de recomendación.

Application Service: ExposicionApplicationService orquesta casos de uso complejos que involucran validaciones y múltiples pasos.

Infrastructure Interfaces: RecommendationService, NotificationService y ValidationService abstraen la comunicación con otros módulos y sistemas externos.

Esta arquitectura sigue el patrón CQRS y es compatible con el enfoque de Modular Monolith, donde cada módulo es cohesivo y se comunica mediante eventos de dominio internos."