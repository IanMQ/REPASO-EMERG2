# 📋 Cómo adaptar esta plantilla a tu caso
|Elemento a reemplazar	|Cómo reemplazarlo	|Ejemplo (Insuretechnova)|
|-|-|-|
|SampleAggregate|	Nombre de tu Aggregate principal	|CartaGarantia|
|SampleAggregateId	|ID del Aggregate|	CartaGarantiaId|
|SampleAggregateEstado	|Enum de estado|	EstadoCarta|
|EntidadRelacionadaId	|ID de entidad relacionada	|PacienteId, ClinicaId|
|RecursoId|	ID de recurso que se asigna	|MedicoId|
|ConceptoDominio1	|Value Object del dominio	|Procedimiento|
|ConceptoDominio2	|Otro Value Object	|Monto|

# 🔍 Explicación de cada componente y su uso

## ENUMS
    enum SampleAggregateEstado {
        CREADO
        EN_PROCESO
        EN_REVISION
        APROBADO
        RECHAZADO
        CERRADO    
    }

|Aspecto|	Explicación|
|-|-|
|¿Qué es?	|Define los estados posibles del Aggregate durante su ciclo de vida.|
|Cuándo usarlo	|El Aggregate tiene un flujo de estados finito y predecible.|
|Cuándo NO usarlo|	Si el estado es dinámico o depende de reglas externas.|
|Cómo personalizarlo|	Cambia los estados según tu dominio. Ejemplo: SOLICITADA, EN_VALIDACION, EN_REVISION, APROBADA, RECHAZADA.|
|Qué pasa si necesito más estados|	Agrega más valores al enum. Ejemplo: PENDIENTE_PAGO, EN_ESPERA, CANCELADA.|

## VALUE OBJECTS - Identificadores

    class SampleAggregateId {
        - value: UUID
    }

    class EntidadRelacionadaId {
        - value: UUID
    }

    class RecursoId {
        - value: UUID
    }


|Aspecto|	Explicación|
|-|-|
|¿Qué es?|	Encapsula un identificador con tipado fuerte.|
|Cuándo usarlo|	Siempre que necesites identificar una entidad.|
|Cuándo NO usarlo|	Si tu lenguaje/framework no lo soporta bien, o si es un sistema muy simple.|
|Cómo personalizarlo|	Cambia el nombre según las entidades. Ejemplo: PacienteId, ClinicaId, MedicoId.|
|Qué pasa si necesito más IDs|	Agrega más clases como SolicitudId, AprobacionId, RevisorId.|
|Puedo usar tipos diferentes a UUID|	Sí, usa Long, String o Integer según tu caso.|

## VALUE OBJECTS - Conceptos del dominio

    class ConceptoDominio1 {
        - atributo1: String
        - atributo2: Integer
        - atributo3: Date
    }

    class ConceptoDominio2 {
        - atributo1: Double
        - atributo2: String
        + sumar(otro: ConceptoDominio2): ConceptoDominio2
        + esValido(): boolean
    }

|Aspecto|	Explicación|
|-|-|
|¿Qué es?	|Objeto inmutable que representa un concepto del dominio sin identidad propia.|
|Cuándo usarlo|	Cuando un conjunto de atributos forma un concepto coherente (ej. dirección, dinero, procedimiento).|
|Cuándo NO usarlo	|Si el concepto es demasiado simple (ej. un solo String). Usa un atributo simple.|
|Cómo personalizarlo|	Reemplaza por tus conceptos. Ejemplo: Procedimiento (código, nombre, complejidad), Monto (valor, moneda).|
|Qué pasa si necesito más VO|	Agrega más clases como Direccion, Contacto, Periodo.|
|Pueden tener métodos|	Sí, pueden tener métodos de validación, cálculos, o transformaciones.|

## AGGREGATE ROOT

    class SampleAggregate {
        - id: SampleAggregateId
        - entidadRelacionadaId: EntidadRelacionadaId
        - recursoId: RecursoId
        - concepto1: ConceptoDominio1
        - concepto2: ConceptoDominio2
        - estado: SampleAggregateEstado
        - fechaCreacion: Date
        - fechaResolucion: Date
        - observaciones: String

        + crear(entidadId: EntidadRelacionadaId, 
                concepto1: ConceptoDominio1, 
                concepto2: ConceptoDominio2): void
        + validar(valido: boolean): void
        + asignar(recursoId: RecursoId): void
        + aprobar(obs: String): void
        + rechazar(motivo: String): void
        + cerrar(): void
        + getEstado(): SampleAggregateEstado
        + esAprobado(): boolean
        + esRechazado(): boolean
    }


|Aspecto	|Explicación|
|-|-|
|¿Qué es?|	La entidad principal del dominio que garantiza consistencia transaccional.|
|Cuándo usarlo|	Siempre es el corazón del Bounded Context.|
|Cómo personalizarlo|	Cambia el nombre y los atributos según tu dominio. Ejemplo: CartaGarantia.|
|Qué pasa si necesito más atributos|	Agrega más atributos como fechaModificacion, prioridad, codigoInterno.|
|Qué pasa si necesito más métodos|	Agrega métodos como modificar(), cancelar(), reabrir().|
|Puede tener Entities hijas|	Sí, ej. List<RevisionMedica>. Se agregan como atributos y se relacionan con *--.|

## COMMANDS (CQRS - Escritura)

    class CrearSampleAggregateCommand {
        - entidadRelacionadaId: EntidadRelacionadaId
        - concepto1: ConceptoDominio1
        - concepto2: ConceptoDominio2
    }

    class AprobarSampleAggregateCommand {
        - sampleAggregateId: SampleAggregateId
        - observaciones: String
    }

    class RechazarSampleAggregateCommand {
        - sampleAggregateId: SampleAggregateId
        - motivo: String
    }

    class AsignarSampleAggregateCommand {
        - sampleAggregateId: SampleAggregateId
        - recursoId: RecursoId
    }

    class CerrarSampleAggregateCommand {
        - sampleAggregateId: SampleAggregateId
        - comentarios: String
    }

|Aspecto	|Explicación|
|-|-|
|¿Qué es?|	Representa una intención de escritura o cambio de estado.|
|Cuándo usarlo|	Para todas las operaciones que modifican el estado del Aggregate.|
|Cuándo NO usarlo|	Para consultas de solo lectura (usa Queries).|
|Cómo personalizarlo|	Cada operación del dominio debe tener un Command. Ejemplo: SolicitarCartaCommand, AprobarCartaCommand, RechazarCartaCommand.|
|Cuándo agregar más Commands|	Cuando hay nuevas operaciones de negocio. Ejemplo: ModificarCartaCommand, CancelarCartaCommand.|
|Cuándo NO agregar un Command|	Si la operación no modifica el estado (ej. consulta).|

    Regla de oro: Un Command debe ser un verbo en infinitivo que describa la acción.

## QUERIES (CQRS - Lectura)

    class ObtenerSampleAggregateQuery {
        - sampleAggregateId: SampleAggregateId
    }

    class ListarSampleAggregatesQuery {
        - entidadRelacionadaId: EntidadRelacionadaId
        - fechaInicio: Date
        - fechaFin: Date
    }

    class ListarPorEstadoQuery {
        - estado: SampleAggregateEstado
    }

    class ListarPorRecursoQuery {
        - recursoId: RecursoId
    }

|Aspecto	|Explicación|
|-|-|
|¿Qué es?|	Representa una intención de lectura que NO modifica el estado.|
|Cuándo usarlo|	Para todas las operaciones de consulta.|
|Cuándo NO usarlo|	Si la consulta es demasiado compleja, crea un Query específico.|
|Cómo personalizarlo|	Cada tipo de consulta debe tener un Query. Ejemplo: ObtenerCartaQuery, ListarCartasPorPacienteQuery.|
|Cuándo agregar más Queries|	Cuando hay nuevas necesidades de consulta. Ejemplo: ListarCartasPorClinicaQuery, ListarCartasPorRangoFechasQuery.|
|Cuándo NO agregar un Query	|Si la consulta se puede resolver con un Query existente.|

    Regla de oro: Un Query debe ser un sustantivo que describa lo que se quiere obtener.

## DOMAIN EVENTS

    class SampleAggregateCreadoEvent {
        - sampleAggregateId: SampleAggregateId
        - entidadRelacionadaId: EntidadRelacionadaId
        - fechaCreacion: Date
    }

    class SampleAggregateAprobadoEvent {
        - sampleAggregateId: SampleAggregateId
        - fechaResolucion: Date
        - observaciones: String
    }

    class SampleAggregateRechazadoEvent {
        - sampleAggregateId: SampleAggregateId
        - fechaResolucion: Date
        - motivo: String
    }

    class SampleAggregateAsignadoEvent {
        - sampleAggregateId: SampleAggregateId
        - recursoId: RecursoId
        - fechaAsignacion: Date
    }

    class SampleAggregateCerradoEvent {
        - sampleAggregateId: SampleAggregateId
        - fechaCierre: Date
        - comentarios: String
    }

|Aspecto|	Explicación|
|-|-|
|¿Qué es?|	Evento que ocurre en el dominio cuando algo importante sucede.|
|Cuándo usarlo|	Cuando necesitas trazabilidad, auditoría o comunicación asíncrona.|
|Cuándo NO usarlo|	En sistemas pequeños que no necesitan auditoría.|
|Cómo personalizarlo|	Cada cambio de estado relevante debe tener un evento. Ejemplo: CartaSolicitadaEvent, CartaAprobadaEvent, CartaRechazadaEvent.|
|Cuándo agregar más eventos|	Cuando hay nuevos cambios de estado relevantes. Ejemplo: CartaModificadaEvent, CartaCanceladaEvent.|
|Cuándo NO agregar un evento|	Si el cambio no es relevante para auditoría o comunicación.|

    Regla de oro: Los Domain Events se disparan desde el Aggregate cuando ocurre un cambio de estado importante.

## REPOSITORY

    interface SampleAggregateRepository {
        + save(sampleAggregate: SampleAggregate): void
        + findById(id: SampleAggregateId): SampleAggregate
        + findByEntidadRelacionada(entidadRelacionadaId: EntidadRelacionadaId): List<SampleAggregate>
        + findByEstado(estado: SampleAggregateEstado): List<SampleAggregate>
        + findByRecurso(recursoId: RecursoId): List<SampleAggregate>
        + delete(id: SampleAggregateId): void
    }

|Aspecto|	Explicación|
|-|-|
|¿Qué es?|	Interfaz que abstrae la persistencia del Aggregate Root.|
|Cuándo usarlo|	Siempre que necesites persistir o recuperar el Aggregate.|
|Cuándo NO usarlo|	Si el sistema no persiste datos (ej. aplicación de solo cálculo).|
|Cómo personalizarlo|	Cambia el nombre y los métodos según tus necesidades de consulta. Ejemplo: CartaGarantiaRepository.|
|Cuándo agregar más métodos|	Cuando necesitas consultas específicas. Ejemplo: findByClinica(), findByRangoFechas().|
|Cuándo NO agregar un método|	Si la consulta no es necesaria para el dominio.|

    Regla de oro: El Repository es una interfaz en el dominio, su implementación está en la capa de Infraestructura.
