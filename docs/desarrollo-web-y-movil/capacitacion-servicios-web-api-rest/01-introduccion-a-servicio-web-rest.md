---
sidebar_position: 1
sidebar_label: 1.1.1 Introducción a Web Service de tipo API REST
---

# Introducción a Web Service de tipo API REST

Un servicio web es una interfaz de programación que permite la comunicación entre dos aplicaciones a través de la red, utilizando protocolos como HTTP. Entre los diferentes tipos de servicios web, uno de los más utilizados es la API REST (Representational State Transfer). Esta arquitectura permite que las aplicaciones realicen operaciones como obtener, crear, actualizar o eliminar datos de manera estructurada y estandarizada. Gracias a REST, es posible integrar aplicaciones desarrolladas en diferentes plataformas para que trabajen juntas de manera eficiente y segura.

## Escenario 1: Integración entre Plataformas

Imagina que tienes una plataforma desarrollada en .NET que necesita compartir información con clientes o sistemas de terceros que utilizan tecnologías diversas, como Java y Oracle. En este caso, podrías crear un servicio web de tipo API REST que permita a cada plataforma consumir la información de manera segura y eficiente.

![integración plataformas](./img/introduccion-servicio-web-escenario1a.drawio.png)

La **solución** consiste en implementar un servicio web RESTful que procese los datos desde el backend y los entregue a los consumidores bajo demanda. Para garantizar la seguridad, se pueden aplicar mecanismos de autenticación, como JSON Web Tokens (JWT), asegurando que solo los usuarios autorizados puedan acceder a la información.

![integración plataformas](./img/introduccion-servicio-web-escenario1b.drawio.png)

### Ejemplo de Arquitectura

En la imagen adjunta, se muestra un ejemplo de arquitectura que ilustra cómo diferentes plataformas pueden integrarse mediante APIs REST:

1. **Plataforma Java**: Una aplicación en Java con base de datos PostgreSQL que consume información de la API a través de solicitudes HTTP.
2. **Plataforma .NET**: Un backend en C# que interactúa con una base de datos MS SQL y expone los datos en formato JSON a través de la API.
3. **Plataforma Oracle**: Un sistema desarrollado en APEX con base de datos Oracle, también consumiendo información mediante solicitudes HTTP.

Cada una de estas plataformas puede interactuar con la API para obtener, crear o modificar datos según sea necesario, sin importar las diferencias en tecnología. Este enfoque asegura una **comunicación estandarizada y escalable** entre sistemas heterogéneos, optimizando la interoperabilidad y simplificando la integración.

## Escenario 2: Separación de Lógica entre Backend y Frontend

En un sistema moderno, es común separar la lógica de negocio del backend y la interfaz de usuario del frontend, manteniendo una estructura independiente pero interconectada. Este enfoque permite una **consistencia en la lógica de negocio** y ofrece flexibilidad para desarrollar aplicaciones con diferentes tecnologías en el frontend, ya sea web o móvil, sin depender del backend.

La **solución** en este caso es crear una API REST que sirva como intermediaria entre el backend y cualquier frontend. De esta manera, la lógica de negocio reside exclusivamente en el backend, mientras que el frontend se encarga solo de la presentación y experiencia del usuario. Esto permite que:

![Separación de Lógica](./img/introduccion-servicio-web-escenario2.drawio.png)

- La lógica de negocio se mantenga **centralizada y consistente**, evitando duplicación de lógica en cada cliente.
- El desarrollo del frontend se pueda realizar en múltiples tecnologías (como Angular, React, Vue para web, o Flutter, Kotlin y React Native para móvil), permitiendo elegir la mejor opción según las necesidades sin afectar al backend.
- La interfaz de usuario sea **independiente y escalable**, permitiendo actualizar o cambiar el frontend sin necesidad de modificar la lógica del backend.

### Beneficios de esta Arquitectura

- **Escalabilidad**: El frontend y el backend pueden escalar de manera independiente, según las demandas del usuario y los recursos del servidor.
- **Facilidad de Mantenimiento**: Al mantener la lógica de negocio en el backend, cualquier cambio en la lógica se aplica automáticamente a todos los clientes.
- **Interoperabilidad**: La API REST permite que diferentes tipos de frontends, incluso desarrollados en plataformas distintas, se conecten al mismo backend.

Este enfoque es especialmente útil en aplicaciones que requieren **flexibilidad en la presentación** y deben ofrecer una experiencia consistente en múltiples dispositivos y entornos.

## Las características fundamentales de una API REST

- Stateless: Cada solicitud del cliente contiene toda la información necesaria, sin depender del estado en el servidor
- HTTP Methods: Usa métodos HTTP como GET, POST, PUT, DELETE para definir operaciones CRUD (Create, Read, Update, Delete).
- Resource-Based: La información se estructura en "recursos" identificados por URL, y las acciones se realizan sobre estos.
- Representation: Los datos se envían en formatos como JSON o XML, permitiendo flexibilidad en la representación
- Cacheable: Las respuestas deben ser cacheables para mejorar la eficiencia y el rendimiento.
- Layered System: La arquitectura permite capas (por ejemplo, entre cliente y servidor), lo que facilita escalabilidad y seguridad.

![Caracteristicas Rest](./img/introduccion-servicio-web-caracteristicas-rest.drawio.png)

## Glosario

**API REST** *(REST API)* — interfaz que expone recursos a través de HTTP siguiendo los principios definidos por Roy Fielding en su [disertación doctoral](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm).

**Recurso** *(Resource)* — entidad identificable por una URI; abstracción central del estilo REST.

**Stateless** *(Stateless)* — cada petición contiene toda la información necesaria; el servidor no mantiene estado de sesión entre llamadas.

**Cliente-servidor** *(Client-server)* — separación de responsabilidades entre la aplicación que consume el recurso y la que lo provee.

**Interoperabilidad** *(Interoperability)* — capacidad de sistemas heterogéneos de intercambiar información siguiendo contratos comunes (HTTP + JSON).

**Layered system** *(Layered system)* — arquitectura en capas que permite intermediarios (proxy, caché, balanceador) sin alterar el contrato.

:::info Referencias primarias
- [RFC 7231 HTTP/1.1 Semantics and Content](https://datatracker.ietf.org/doc/html/rfc7231) — especificación oficial de semántica HTTP.
- [Roy Fielding dissertation](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) — definición original del estilo REST.
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0) — estándar para describir APIs REST.
- [JSON.org](https://www.json.org/) — especificación del formato JSON.
- [OWASP API Security](https://owasp.org/API-Security/) — riesgos y buenas prácticas de seguridad en APIs.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** decidir cuándo un servicio web tipo API REST es la arquitectura correcta para integrar sistemas o separar backend y frontend.

**Entradas:**
- Inventario de sistemas a integrar y sus tecnologías (Java, .NET, Oracle, etc.).
- Requisitos de interoperabilidad entre plataformas.
- Necesidad de separar lógica de negocio de la capa de presentación.
- Restricciones de seguridad, escalabilidad y rendimiento.

**Pasos:**
1. Identificar los consumidores de la API y sus tecnologías.
2. Evaluar si el caso se ajusta a integración entre plataformas o a separación backend/frontend.
3. Validar el cumplimiento de los principios REST (stateless, recursos, métodos HTTP, representación).
4. Definir formato de intercambio (JSON) y mecanismos de autenticación (por ejemplo, JWT sobre HTTPS).
5. Dimensionar capas y cacheo para cumplir con escalabilidad y rendimiento.
6. Documentar la arquitectura resultante para consumidores internos y externos.

**Salidas:**
- Justificación del uso de API REST sobre otras alternativas.
- Diagrama de integración con los consumidores.
- Lista de principios REST que debe cumplir la implementación.

**Errores comunes:**
- Exponer lógica de negocio duplicada en cada cliente en lugar de centralizarla en el backend.
- Romper la característica stateless manteniendo sesión en el servidor.
- No definir autenticación desde el inicio y agregarla como parche posterior.
- Confundir "API REST" con cualquier endpoint HTTP sin aplicar sus restricciones.

**Referencias cruzadas:**
- [1.1.2 Componentes Básicos de un Servicio Web tipo API REST](./02-componentes-basicos-api-rest.md)
- [1.1.4 Autenticación y Autorización en APIs RESTful](./04-autenticacion-autorizacion-rest.md)
- [1.2.1 Arquitectura de Backend API Rest en .NET Core](../net-core-web-api/01-arquitectura-de-backend.md)
</div>
