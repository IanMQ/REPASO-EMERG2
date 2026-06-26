# 📝 Justificación para tu PPT
## Justificación - Domain Layer

"El diagrama de clases del Domain Layer representa el Bounded Context 'Gestión de Usuarios' para la plataforma de autenticación centralizada de TechNova Solutions, desarrollada con Spring Boot y siguiendo los principios de Domain-Driven Design (DDD) con CQRS.

Bounded Context Seleccionado: Se ha identificado 'Gestión de Usuarios' como el Bounded Context principal, ya que es el núcleo de la plataforma de autenticación y contiene las reglas de negocio más críticas para el alta, baja y políticas de usuarios.

Aggregate Root: Se ha seleccionado Usuario como el Aggregate Root principal porque es la entidad que garantiza la consistencia transaccional de todo el ciclo de vida del usuario. Encapsula las reglas de negocio más importantes:

Un usuario solo puede activarse si está en estado PENDIENTE_VERIFICACION

Un usuario bloqueado solo puede desbloquearse después de un período definido

Un usuario solo puede tener roles válidos según su tipo (INTERNO/EXTERNO)

Las políticas de seguridad (MFA, session timeout, intentos fallidos) se aplican al usuario

Value Objects: Se utilizan Value Objects para conceptos del dominio sin identidad propia:

UsuarioId, PoolId, EmpresaId — identificadores tipados

Credenciales — encapsula email, password hash y políticas de cambio de contraseña

DatosPersonales — información personal del usuario

PoliticasUsuario — reglas de seguridad aplicables al usuario

PoolUsuarios — representa los pools de usuarios externos (por región, tipo cliente, etc.)

Enums: TipoUsuario (INTERNO/EXTERNO), EstadoUsuario (ACTIVO/INACTIVO/BLOQUEADO/PENDIENTE_VERIFICACION), RolUsuario (EMPLEADO, ADMINISTRADOR, CLIENTE_FREEMIUM, etc.) y TipoPool (REGION, TIPO_CLIENTE, IDIOMA, SECTOR).

Commands y Queries (CQRS): Se separan las operaciones de escritura (CrearUsuarioCommand, ActivarUsuarioCommand, BloquearUsuarioCommand, CambiarPasswordCommand, etc.) de las de lectura (ObtenerUsuarioQuery, ListarUsuariosPorTipoQuery, ListarUsuariosPorEstadoQuery, etc.).

Domain Events: UsuarioCreadoEvent, UsuarioActivadoEvent, UsuarioBloqueadoEvent, PasswordCambiadoEvent, RolAsignadoEvent y PoolAsignadoEvent permiten trazabilidad y comunicación asíncrona entre módulos.

Repository: UsuarioRepository es una interfaz que abstrae la persistencia del Aggregate Root, con métodos para búsqueda por email, tipo, estado, rol y pool.

Nomenclatura: Se utilizó nomenclatura en inglés con Upper-Camel-Case para clases y lower-Camel-Case para atributos y métodos, siguiendo las convenciones de Java y Spring Boot."

## Justificación - Application Layer

"El diagrama de clases del Application Layer representa el orquestamiento de los casos de uso del Bounded Context 'Gestión de Usuarios', siguiendo el patrón CQRS para separar responsabilidades de lectura y escritura.

Command Handlers: Procesan los Commands de escritura (CrearUsuarioHandler, ActivarUsuarioHandler, BloquearUsuarioHandler, CambiarPasswordHandler, etc.). Cada Handler recibe un Command, recupera o crea el Aggregate, ejecuta métodos del dominio, persiste a través del Repository y publica eventos de dominio. El CrearUsuarioHandler utiliza PasswordEncoder para encriptar la contraseña antes de persistir.

Query Handlers: Procesan las Queries de lectura (ObtenerUsuarioHandler, ListarUsuariosPorTipoHandler, ListarUsuariosPorEstadoHandler, etc.). Recuperan datos del Repository y los transforman a DTOs para su consumo por la capa de presentación.

DTOs: UsuarioDTO, CredencialesDTO y PoolUsuariosDTO aíslan la capa de presentación del dominio, transformando Aggregates en objetos planos serializables.

Domain Event Publisher: Publica eventos de dominio para comunicación interna entre módulos.

Event Listeners: UsuarioEventListener escucha eventos de dominio y ejecuta acciones secundarias como registro de auditoría (AuditService), notificaciones al usuario (NotificationService) y sincronización con el proveedor de identidad externo (ExternalIdentityService).

Application Service: UsuarioApplicationService orquesta casos de uso complejos como el registro completo de un usuario con validaciones y sincronización con sistemas externos.

Infrastructure Interfaces: PasswordEncoder (encriptación de contraseñas), AuditService (trazabilidad), NotificationService (notificaciones) y ExternalIdentityService (integración con Auth0/Keycloak/Cognito) abstraen la comunicación con infraestructura y sistemas externos.

Esta arquitectura sigue el patrón CQRS y permite la integración con Active Directory para usuarios internos y con proveedores de identidad externos (Auth0, Keycloak, Cognito) para usuarios externos, cumpliendo con los requisitos de SSO, MFA y pools de usuarios múltiples."