# 📝 Justificación para tu PPT
## Justificación - Domain Layer

"El diagrama de clases del Domain Layer representa el Bounded Context 'Gestión de Tours Virtuales' para la plataforma México360 Patrimonio Vivo, desarrollada con Spring Boot y siguiendo los principios de Domain-Driven Design (DDD) con CQRS.

Bounded Context Seleccionado: Se ha identificado 'Gestión de Tours Virtuales' como el Bounded Context con mayor relación con los Commands, Queries y Domain Events de la plataforma, ya que es el núcleo del negocio y contiene las reglas de negocio más críticas para la creación y publicación de recorridos virtuales.

Aggregate Root: Se ha seleccionado TourVirtual como el Aggregate Root principal porque es la entidad que garantiza la consistencia transaccional de todo el proceso de gestión de tours. Encapsula las reglas de negocio más importantes:

Un tour solo puede publicarse si tiene al menos una sección con puntos interactivos

Un tour publicado no puede modificarse directamente (requiere despublicación)

El estado del tour sigue un ciclo de vida definido (BORRADOR → EN_REVISION → PUBLICADO → ARCHIVADO)

Cada tour está asociado a un sitio patrimonial y una institución responsable

Entities: Seccion y PuntoInteractivo son entidades hijas que tienen identidad propia y pueden existir dentro del contexto de un tour. Cada sección contiene múltiples puntos interactivos, y cada punto interactivo puede contener multimedia.

Value Objects: Se utilizan Value Objects para conceptos del dominio sin identidad propia:

TourId, SeccionId, PuntoInteractivoId, InstitucionId, SitioId — identificadores tipados

UbicacionGeografica — encapsula coordenadas, altitud y dirección para geolocalización

Multimedia — encapsula tipo, URL, título, descripción y duración del contenido multimedia

Enums: EstadoTour define el ciclo de vida (BORRADOR → EN_REVISION → PUBLICADO → ARCHIVADO), TipoMultimedia clasifica los tipos de contenido (IMAGEN_360, VIDEO_360, MODELO_3D, etc.), TipoPuntoInteractivo clasifica los puntos (INFORMACION, AUDIO, VIDEO, etc.) y TipoSitio clasifica los sitios patrimoniales (ZONA_ARQUEOLOGICA, MUSEO, PUEBLO_MAGICO, etc.).

Commands y Queries (CQRS): Se separan las operaciones de escritura (CrearTourCommand, AgregarSeccionCommand, AgregarPuntoInteractivoCommand, PublicarTourCommand, etc.) de las de lectura (ObtenerTourQuery, ListarToursPorSitioQuery, ListarToursPorEstadoQuery, etc.).

Domain Events: TourCreadoEvent, TourPublicadoEvent, TourArchivadoEvent, SeccionAgregadaEvent, PuntoInteractivoAgregadoEvent y TourActualizadoEvent permiten trazabilidad y comunicación asíncrona entre módulos del Modular Monolith.

Repository: TourVirtualRepository es una interfaz que abstrae la persistencia del Aggregate Root, con métodos para búsqueda por sitio, institución, estado y tours publicados.

Nomenclatura: Se utilizó nomenclatura en inglés con Upper-Camel-Case para clases y lower-Camel-Case para atributos y métodos, siguiendo las convenciones de Java y Spring Boot."


## Justificación - Application Layer

"El diagrama de clases del Application Layer representa el orquestamiento de los casos de uso del Bounded Context 'Gestión de Tours Virtuales', siguiendo el patrón CQRS para separar responsabilidades de lectura y escritura.

Command Handlers: Procesan los Commands de escritura (CrearTourHandler, AgregarSeccionHandler, AgregarPuntoInteractivoHandler, PublicarTourHandler, etc.). Cada Handler recibe un Command, recupera o crea el Aggregate, ejecuta métodos del dominio, persiste a través del Repository y publica eventos de dominio. El PublicarTourHandler utiliza AnalyticsService y NotificationService para acciones secundarias.

Query Handlers: Procesan las Queries de lectura (ObtenerTourHandler, ListarToursPorSitioHandler, ListarToursPorEstadoHandler, etc.). Recuperan datos del Repository y los transforman a DTOs para su consumo por la capa de presentación.

DTOs: TourVirtualDTO, SeccionDTO, PuntoInteractivoDTO, MultimediaDTO y UbicacionGeograficaDTO aíslan la capa de presentación del dominio, transformando Aggregates en objetos planos serializables.

Domain Event Publisher: Publica eventos de dominio para comunicación interna entre módulos del Modular Monolith.

Event Listeners: TourEventListener escucha eventos de dominio y ejecuta acciones secundarias como registro de analytics (AnalyticsService), notificaciones a instituciones (NotificationService) y actualización de índices de búsqueda (IndexService).

Application Service: TourApplicationService orquesta casos de uso complejos como la creación de tours con validaciones y publicación con efectos secundarios.

Infrastructure Interfaces: SitioService (consulta de sitios patrimoniales), AnalyticsService (registro de métricas), NotificationService (notificaciones), IndexService (búsqueda) y ValidationService (validaciones) abstraen la comunicación con otros módulos y sistemas externos.

Esta arquitectura sigue el patrón CQRS y es compatible con el enfoque de Modular Monolith, donde cada módulo es cohesivo y se comunica mediante eventos de dominio internos, alineándose con los requisitos de DDD y Event Storming del caso México360."