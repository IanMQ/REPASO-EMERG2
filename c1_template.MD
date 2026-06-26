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