# Arquitectura de Backend API Rest en .NET Core

Este documento describe detalladamente la arquitectura de un proyecto backend implementado en .NET Core, empleando Entity Framework Core para la gestión de datos. El diagrama presentado a continuación ilustra la estructura y el flujo del proyecto, organizado en distintas capas que permiten una separación clara de responsabilidades.

## 1. Arquitectura del Proyecto (Clean Architecture)

La arquitectura del proyecto se organiza siguiendo los principios de Clean Architecture, lo cual asegura la independencia entre los distintos módulos y facilita la mantenibilidad del proyecto. Clean Architecture se estructura en varias capas concéntricas, cada una con una función específica y dependencia mínima de las demás:

- **Controladores API REST (Interface Adapters)**
- **Capa de Lógica de Negocio (Business Logic Layer)**
- **Capa de Entidades (Entities Layer)**

![diagrama-de-arquitectura](./img/arquitectura-backend-api.svg)

### 1.1 Controladores API REST (Interface Adapters)

Los controladores se encargan de recibir las solicitudes HTTP y delegar las responsabilidades a los servicios de aplicación. En esta capa se inyectan los servicios de aplicación para ejecutar la lógica de los casos de uso. Los controladores manejan las solicitudes HTTP y devuelven respuestas al cliente, adaptando el formato de los datos según lo requerido.

### 1.2 Capa de Lógica de Negocio (Business Logic Layer)

Los servicios de aplicación representan los casos de uso específicos y la lógica que define cómo interactúan las entidades. En esta capa se define el comportamiento de la aplicación para cumplir con las reglas del negocio, gestionando las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) y otros procesos específicos. La capa de lógica de negocio orquesta cómo se manipulan los datos y coordina el acceso a las entidades a través de los adaptadores de repositorio.

#### 1.2.2 Adaptadores de Repositorio (Repository Adapters)

Estos adaptadores permiten que la lógica de los servicios de aplicación interactúe con la capa de infraestructura de manera desacoplada. Los adaptadores de repositorio se encargan de traducir las operaciones de la base de datos, proporcionando una abstracción sobre el acceso a los datos para que los servicios no dependan directamente del framework o la base de datos.

- Modelo (Entity Model): Define la estructura de las entidades o clases que representan los objetos fundamentales del negocio. Los modelos en esta capa definen las reglas de negocio fundamentales y las propiedades de las entidades, independientes de cualquier marco de trabajo o base de datos.

- Data Context (DbContext): El Data Context se encarga de definir cómo se accede y gestiona la información de las entidades en la base de datos, estableciendo la conexión y gestionando las operaciones CRUD. Actúa como un puente entre las entidades y la base de datos, permitiendo que se realicen operaciones de una manera sencilla y estructurada. Se registra como un servicio en el archivo `Program.cs` utilizando la inyección de dependencias, lo cual facilita su uso en cualquier parte del proyecto.

## 2 Resumen del Flujo del Proyecto Backend

El siguiente es un resumen del flujo del proyecto basado en el diagrama proporcionado:

1. **Presentación de Datos a la Vista:** Los datos procesados por el controlador se devuelven al cliente en formato JSON, permitiendo una presentación adecuada en la interfaz del usuario.
2. **Controlador:** Los controladores reciben las solicitudes HTTP y utilizan los servicios para procesar la información antes de devolver la respuesta al cliente.
3. **Servicio:** Los servicios se encargan de la lógica de negocio, utilizando el Data Context para gestionar los datos y aplicar las reglas específicas del negocio.
4. **Data Context:** El Data Context gestiona la conexión y las operaciones con la base de datos, facilitando el acceso a los datos y su manipulación. En los casos de que no se necesite una logica de negocio compleja el Controlador puede acceder a los datos directamente injectando el Data Context.
5. **Clase Model:** La información en la tabla se mapea a un modelo de objetos para representar las entidades en el sistema.
6. **Base de Datos (Table):** Los datos se almacenan en una tabla en la base de datos.

![flujo-backend](./img/flujo-proyecto-backend-net.drawio.svg)

Esta estructura modular basada en Clean Architecture permite una clara separación de responsabilidades entre las capas, lo cual facilita el mantenimiento, escalabilidad y prueba del proyecto. Cada componente cumple un rol específico dentro del flujo de datos, desde la base de datos hasta la interfaz del cliente, asegurando una gestión eficiente de la información y promoviendo la independencia entre las distintas capas del sistema.
