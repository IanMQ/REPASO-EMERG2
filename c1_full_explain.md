El diagrama de clases UML representa el Domain Layer del Bounded Context 'Gestión de Cartas de Garantía', siguiendo los principios de Domain-Driven Design (DDD) y el patrón CQRS.

Selección del Aggregate Root:
Se eligió CartaGarantia como Aggregate Root porque es la entidad que garantiza la consistencia transaccional de todo el proceso de autorización. Representa el ciclo de vida completo de una solicitud (desde su registro hasta su resolución final) y encapsula las reglas de negocio más críticas del dominio.

Entities:
RevisionMedica es una entidad hija que registra cada evaluación realizada por un médico. Tiene identidad propia (UUID) y puede existir independientemente dentro del contexto de una carta de garantía.

Value Objects:
Se utilizan Value Objects para conceptos del dominio que no tienen identidad propia: identificadores (CartaGarantiaId, PacienteId, etc.), Procedimiento (encapsula código, nombre, tipo y complejidad) y Money (maneja montos con precisión y moneda).

Enums:
EstadoCarta define el ciclo de vida de la solicitud (SOLICITADA → EN_VALIDACION → EN_REVISION → OBSERVADA/APROBADA/RECHAZADA). TipoProcedimiento clasifica la complejidad de las intervenciones. Dictamen representa el resultado de una revisión médica.

Commands y Queries (CQRS):
Se separan las operaciones de escritura (Commands) de las de lectura (Queries):

Commands: SolicitarCartaCommand, AprobarCartaCommand, RechazarCartaCommand, ObservarCartaCommand — encapsulan las intenciones de cambio de estado.

Queries: ObtenerCartaQuery, ListarCartasQuery, ListarPorEstadoQuery — permiten consultas optimizadas.

Domain Events:
Los eventos de dominio (CartaSolicitadaEvent, CartaAprobadaEvent, etc.) permiten trazabilidad completa del proceso, comunicación asíncrona entre microservicios vía Message Broker, y auditoría interna para SUSALUD.

Repository:
CartaGarantiaRepository es una interfaz que abstrae la persistencia del Aggregate Root, permitiendo cambiar la tecnología de base de datos sin afectar la lógica del dominio.

Nomenclatura:
Se utilizó nomenclatura en inglés con UpperCamelCase para clases e interfaces y lowerCamelCase para atributos y métodos, siguiendo las convenciones estándar de Java y Spring Boot.

