## 📝 TEMPLATE DE JUSTIFICACIÓN - DOMAIN LAYER (Versión Ultra Básica)

El diagrama de clases del **Domain Layer** representa el Bounded Context **[NOMBRE_DEL_BOUNDED_CONTEXT]** para el sistema **[NOMBRE_DEL_SISTEMA]**, desarrollado con **Spring Boot** y siguiendo los principios de **Domain-Driven Design (DDD)** con **CQRS**.

---

**1. Bounded Context Seleccionado**
Se ha identificado **[NOMBRE_DEL_BOUNDED_CONTEXT]** como el Bounded Context principal porque **[RAZÓN_PRINCIPAL_POR_LA_QUE_ES_EL_MÁS_IMPORTANTE]**.

---

**2. Aggregate Root**
Se ha seleccionado **`[NOMBRE_DEL_AGGREGATE]`** como el Aggregate Root porque es la entidad que garantiza la consistencia transaccional del proceso de **[PROCESO_DEL_NEGOCIO]**.

Encapsula las reglas de negocio más importantes:
- **[REGLAS_DE_NEGOCIO_1]**
- **[REGLAS_DE_NEGOCIO_2]**
- **[REGLAS_DE_NEGOCIO_3]**

---

**3. Value Objects**
Se utilizan Value Objects para conceptos del dominio sin identidad propia:
- **`[NOMBRE_DEL_ID]`** — identificador tipado del Aggregate.
- **`[NOMBRE_DEL_ID_RELACIONADO]`** — identificador de la entidad relacionada.
- **`[NOMBRE_DEL_CONCEPTO]`** — encapsula **[DESCRIPCIÓN_DEL_CONCEPTO]**.

---

**4. Enums**
**`[NOMBRE_DEL_ENUM]`** define el ciclo de vida del Aggregate: **[LISTA_DE_ESTADOS]**.

---

**5. Commands y Queries (CQRS)**
Se separan las operaciones de escritura de las de lectura:
- **Commands (escritura):** `Crear[NOMBRE]Command`, `[Accion1][NOMBRE]Command`, `[Accion2][NOMBRE]Command` — representan intenciones de cambio de estado.
- **Queries (lectura):** `Obtener[NOMBRE]Query`, `Listar[NOMBRE]Query` — permiten consultar información sin modificar el estado.

---

**6. Domain Events**
Los eventos de dominio (`[NOMBRE]CreadoEvent`, `[NOMBRE][Accion1]Event`, `[NOMBRE][Accion2]Event`) permiten:
- Trazabilidad completa del proceso.
- Auditoría interna para cumplimiento regulatorio.
- Comunicación asíncrona entre módulos.

---

**7. Repository**
**`[NOMBRE]Repository`** es una interfaz que abstrae la persistencia del Aggregate Root, siguiendo el patrón Repository recomendado por Evans en DDD. Su implementación concreta es responsabilidad de la capa de Infraestructura.

---

**8. Nomenclatura y convenciones**
Se utilizó nomenclatura en **inglés** con **Upper-Camel-Case** para clases e interfaces y **lower-Camel-Case** para atributos y métodos, siguiendo las convenciones estándar de **Java y Spring Boot**.

---

## 📝 TEMPLATE DE JUSTIFICACIÓN - APPLICATION LAYER (Versión Ultra Básica)

El diagrama de clases del **Application Layer** representa el orquestamiento de los casos de uso del Bounded Context **[NOMBRE_DEL_BOUNDED_CONTEXT]**, siguiendo el patrón **CQRS** para separar responsabilidades de lectura y escritura.

---

**1. Command Handlers**
Procesan los Commands de escritura:
- **`Crear[NOMBRE]Handler`** — recibe el Command, crea el Aggregate, ejecuta métodos del dominio, persiste a través del Repository y publica eventos de dominio.
- **`[Accion1][NOMBRE]Handler`** — recupera el Aggregate, ejecuta la acción correspondiente y persiste los cambios.
- **`[Accion2][NOMBRE]Handler`** — similar al anterior, para la acción inversa.

Cada Handler delega la lógica de negocio al Aggregate, manteniendo la capa de aplicación libre de reglas de negocio.

---

**2. Query Handlers**
Procesan las Queries de lectura:
- **`Obtener[NOMBRE]Handler`** — recupera un Aggregate por su ID y lo transforma a DTO.
- **`Listar[NOMBRE]Handler`** — recupera una lista de Aggregates según un filtro (ej. estado) y los transforma a DTOs.

No modifican el estado del sistema.

---

**3. DTOs**
**`[NOMBRE]DTO`** aísla la capa de presentación del dominio, transformando el Aggregate en un objeto plano serializable. Contiene el método `fromDomain()` para la conversión.

---

**4. Domain Event Publisher**
**`DomainEventPublisher`** es una interfaz que publica eventos de dominio para comunicación asíncrona entre módulos, permitiendo:
- Trazabilidad.
- Desacoplamiento.
- Reacciones automáticas (notificaciones, auditoría).

---

**5. Event Listener (Opcional)**
**`[NOMBRE]EventListener`** escucha eventos de dominio y ejecuta acciones secundarias como:
- Envío de notificaciones.
- Registro de auditoría.
- Integración con sistemas externos.

---

**6. Application Service (Opcional)**
**`[NOMBRE]ApplicationService`** orquesta casos de uso complejos que involucran múltiples pasos o integraciones con servicios externos.

---

**7. Infrastructure Services (Opcional)**
**`NotificationService`** abstrae la comunicación con sistemas externos (ej. envío de correos), permitiendo cambiar la implementación sin afectar la lógica de negocio.

---

**8. Nomenclatura y convenciones**
Se utilizó nomenclatura en **inglés** con **Upper-Camel-Case** para clases e interfaces y **lower-Camel-Case** para atributos y métodos, siguiendo las convenciones estándar de **Java y Spring Boot**.

---


