---
sidebar_position: 2
sidebar_label: 1.1.2 Componentes Básicos de un Servicio Web tipo API REST
---

# Componentes Básicos de un Servicio Web tipo API REST

Para que un servicio web tipo API REST sea funcional y cumpla con las mejores prácticas, debe contar con ciertos componentes y seguir principios fundamentales. A continuación, se describen los elementos básicos que debe tener, así como algunas buenas prácticas para su diseño.

## Uso de Métodos HTTP

Las APIs RESTful utilizan los métodos estándar de HTTP para definir las operaciones sobre los recursos. Los métodos más comunes son:

- **GET:** Se utiliza para recuperar información de un recurso del servidor, como obtener la lista de detalles de un usuario.
- **POST:** Se utiliza para crear un nuevo recurso en el servidor, como registrar un nuevo usuario.
- **PUT:** Se utiliza para actualizar completamente un recurso existente, como modificar toda la información de un perfil de usuario.
- **PATCH:** Se utiliza para realizar actualizaciones parciales en un recurso, como actualizar únicamente la dirección de un usuario.
- **DELETE:** Se utiliza para eliminar un recurso del servidor, como eliminar un usuario que ya no está activo.

Cada operación debe estar bien definida y seguir el propósito de cada método HTTP. Utilizar **PATCH** es útil cuando solo se desea actualizar ciertos campos de un recurso, evitando la necesidad de enviar todo el objeto como con **PUT**.

### Código de Estado HTTP Significativos

Las APIs deben utilizar códigos de estado HTTP para indicar el resultado de las solicitudes. A continuación, se presenta una lista de los estados HTTP comunes y sus usos:

| Código de Estado         | Descripción                                               | Métodos HTTP Comúnmente Utilizados |
|--------------------------|-----------------------------------------------------------|-------------------------------------|
| **200 OK**               | Solicitud exitosa                                         | GET, PUT, PATCH, DELETE            |
| **201 Created**          | Recurso creado exitosamente                               | POST                               |
| **204 No Content**       | Solicitud exitosa sin respuesta de contenido              | DELETE, PUT                        |
| **400 Bad Request**      | Error en la solicitud del cliente (datos inválidos)       | POST, PUT, PATCH                   |
| **401 Unauthorized**     | Autenticación requerida o fallida                         | GET, POST, PUT, DELETE             |
| **403 Forbidden**        | Acceso al recurso denegado (si esta autenticado pero no tiene permisos para acceder el recurso)                               | GET, POST, PUT, DELETE             |
| **404 Not Found**        | Recurso no encontrado                                    | GET, PUT, DELETE                   |
| **422 Unprocessable Entity** | Datos inválidos que cumplen el formato pero no pasan validaciones | POST, PUT, PATCH     |
| **500 Internal Server Error** | Error en el servidor                                   | Cualquier método                   |

## Buenas Prácticas

### URLs o Endpoints Bien Definidos

Los endpoints representan los recursos de la API y deben estar estructurados de manera lógica y legible para facilitar su uso, mantenimiento y escalabilidad. A continuación, algunas buenas prácticas de nombrado:

#### 1.1 Nombrar los Endpoints

- **Usa sustantivos en plural para los recursos**:
  - Correcto: `/usuarios`, `/productos`, `/pedidos`
  - Incorrecto: `/usuario`, `/producto`, `/pedido` (usar singular puede llevar a inconsistencias)

- **Mantén la coherencia en los nombres y convenciones**:
  - Utiliza siempre minúsculas y separa las palabras con guiones (`-`) para mayor legibilidad.
    - Correcto: `/categorias-de-productos`
    - Incorrecto: `/CategoriasDeProductos` o `/categorias_de_productos`
  
- **Usa sub-recursos para indicar relaciones entre recursos**:
  - Si un recurso está subordinado a otro, estructura el endpoint de manera que refleje esta relación.
    - Correcto: `/usuarios/{id}/pedidos` para ver los pedidos de un usuario específico.
    - Incorrecto: `/pedidos-por-usuario/{id}` o `/pedidosUsuario/{id}`

#### 1.2 Definición de Acciones

Para las operaciones CRUD, es mejor utilizar métodos HTTP en lugar de incluir el verbo en el nombre del endpoint. Los métodos `GET`, `POST`, `PUT`, `DELETE`, etc., indican la acción a realizar.

- **Evita verbos en el endpoint**:
  - Correcto: `POST /usuarios` para crear un usuario.
  - Incorrecto: `POST /crearUsuario`
  
- **Endpoints para acciones no CRUD**:
  - Si necesitas realizar una acción específica que no encaja en el CRUD, considera incluir el verbo en el endpoint, pero en contextos específicos y solo si es necesario.
    - Correcto: `POST /usuarios/{id}/activar` para activar un usuario.
    - Incorrecto: `POST /activarUsuario/{id}` (evita poner el verbo antes del recurso principal)

#### 1.3 Uso de Filtros y Parámetros

- **Utiliza parámetros de consulta (query parameters) para filtros, búsquedas y paginación**:
  - Correcto: `/productos?categoria=electronica&orden=precio_desc`
  - Incorrecto: `/productos/filtro/categoria/electronica/precio/descendente` (evita incluir filtros en el path principal)

#### 1.4 Ejemplos de Endpoints Bien y Mal Especificados

| Acción                       | Endpoint Bien Escrito                       | Endpoint Mal Escrito               |
|------------------------------|---------------------------------------------|------------------------------------|
| Obtener todos los usuarios   | `GET /usuarios`                            | `GET /obtenerUsuarios`            |
| Crear un nuevo producto      | `POST /productos`                          | `POST /crearProducto`             |
| Ver pedidos de un usuario    | `GET /usuarios/{id}/pedidos`               | `GET /verPedidosUsuario/{id}`     |
| Activar una cuenta de usuario| `POST /usuarios/{id}/activar`              | `POST /activarCuentaUsuario/{id}` |
| Buscar productos por categoría | `GET /productos?categoria=electronica`   | `GET /buscarProductoPorCategoria/electronica` |

### Soporte para Paginación, Filtrado y Ordenamiento

En caso de listas extensas de datos, la API debe ofrecer opciones de paginación (por ejemplo, ?page=1&limit=20), filtrado y ordenamiento para evitar el sobrecargado de datos en una sola solicitud y mejorar el rendimiento.

### Representación de Recursos

Los recursos deben representarse en un formato de datos estándar y fácil de entender para los consumidores de la API. El formato más común es **JSON**, aunque también puede soportarse **XML** u otros formatos. La API debe enviar y recibir datos en un formato consistente.

### Stateless (Sin Estado)

Las APIs RESTful deben ser sin estado, es decir, cada solicitud del cliente debe contener toda la información necesaria para que el servidor la procese. El servidor no debe almacenar información sobre el estado de la sesión del cliente entre solicitudes. Esto implica que los datos de autenticación (como tokens) deben enviarse en cada solicitud, generalmente en los encabezados.

### Autenticación y Autorización

Para proteger los recursos, las APIs deben implementar mecanismos de autenticación como **JWT (JSON Web Token)**, **OAuth** o **API Keys**. La autorización es necesaria para asegurarse de que solo los usuarios con los permisos adecuados puedan acceder o modificar ciertos recursos.

### Manejo de Errores y Mensajes Claros (Problem Details)

La API debe devolver mensajes de error claros y útiles cuando algo falla. Para ello, se recomienda utilizar el estándar **Problem Details** (RFC 7807), que proporciona una estructura consistente para representar errores en APIs HTTP. Este estándar facilita el diagnóstico de problemas por parte del cliente.

Un ejemplo de mensaje de error usando Problem Details sería el siguiente:

```json
{
  "type": "https://example.com/problema/usuario-no-encontrado",
  "title": "Usuario no encontrado",
  "status": 404,
  "detail": "El usuario con el ID especificado no existe en el sistema.",
  "instance": "/api/usuarios/12345"
}
```

- **type**: URI que identifica el tipo de error; puede llevar a una página con detalles adicionales.
- **title**: Un título corto y legible para el error.
- **status**: Código de estado HTTP correspondiente.
- **detail**: Descripción más detallada del error.
- **instance**: URI que señala la instancia específica del recurso o endpoint donde ocurrió el error.

Este formato estandarizado permite a los desarrolladores entender rápidamente el contexto y la causa de un error, mejorando la experiencia del cliente de la API y facilitando la depuración.

### Documentación de la API

La API debe estar bien documentada para que los desarrolladores puedan entender cómo usarla. Herramientas como Swagger/OpenAPI o Postman pueden ayudar a documentar y probar la API. La documentación debe incluir ejemplos de solicitudes y respuestas, descripciones de los endpoints, parámetros, códigos de estado y métodos de autenticación.

### Versionamiento de la API

Es importante implementar versionamiento para evitar interrupciones cuando se hacen cambios en la API. El versionamiento puede incluirse en la URL o en los encabezados:

- Ejemplo de versionamiento en la URL: `/api/v1/usuarios`.

Esto permite que los consumidores de la API migren a nuevas versiones de forma controlada.

### Seguridad

Además de la autenticación, se deben implementar medidas de seguridad, como el uso de HTTPS para encriptar las comunicaciones y proteger los datos durante el transporte. Limitar el número de solicitudes por minuto (Rate Limiting) para evitar ataques de denegación de servicio (DDoS).

## Resumen

Con estos elementos, una API REST está bien estructurada y cumple con los principios de diseño RESTful, ofreciendo una experiencia confiable, segura y eficiente para los consumidores de la API.

## Glosario

**Método HTTP** *(HTTP method)* — verbo de la petición (GET, POST, PUT, PATCH, DELETE) que indica la operación sobre el recurso ([RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231)).

**Código de estado** *(HTTP status code)* — número de 3 dígitos que indica el resultado de la solicitud (2xx éxito, 4xx error del cliente, 5xx error del servidor).

**Endpoint** *(Endpoint)* — URL concreta que identifica un recurso o acción sobre él.

**CRUD** *(Create, Read, Update, Delete)* — conjunto de operaciones básicas sobre recursos.

**Paginación** *(Pagination)* — mecanismo para dividir respuestas grandes en páginas, típicamente con parámetros `page` y `limit`.

**Problem Details** *(Problem Details for HTTP APIs)* — estándar para representar errores en APIs HTTP definido por [RFC 7807](https://datatracker.ietf.org/doc/html/rfc7807).

**Versionamiento** *(API versioning)* — práctica para evolucionar la API sin romper a los consumidores existentes, típicamente vía prefijo en URL (`/v1/`).

**Rate limiting** *(Rate limiting)* — limitación del número de peticiones por unidad de tiempo para proteger la API de abuso y DDoS.

:::info Referencias primarias
- [RFC 7231 HTTP/1.1 Semantics and Content](https://datatracker.ietf.org/doc/html/rfc7231) — métodos y códigos de estado.
- [RFC 7807 Problem Details](https://datatracker.ietf.org/doc/html/rfc7807) — estructura estándar de errores HTTP.
- [OpenAPI Specification 3.1](https://spec.openapis.org/oas/v3.1.0) — descripción formal de APIs.
- [OWASP API Security](https://owasp.org/API-Security/) — autenticación, autorización y rate limiting.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diseñar los componentes base y las convenciones mínimas para construir una API REST correcta y mantenible.

**Entradas:**
- Listado de recursos del dominio y operaciones esperadas.
- Reglas de nombrado y convenciones de la organización.
- Requisitos de seguridad, paginación y documentación.
- Estándares a adoptar (HTTP status codes, Problem Details, versionamiento).

**Pasos:**
1. Definir recursos en plural y URLs jerárquicas para sub-recursos.
2. Mapear operaciones CRUD a los métodos HTTP adecuados (GET, POST, PUT, PATCH, DELETE).
3. Establecer la tabla de códigos de estado aplicables por operación.
4. Definir representación JSON, filtros, paginación y ordenamiento por query string.
5. Aplicar Problem Details (RFC 7807) para respuestas de error.
6. Incluir autenticación, HTTPS y rate limiting en la capa de seguridad.
7. Versionar la API y documentarla con Swagger/OpenAPI o equivalente.

**Salidas:**
- Guía de endpoints, métodos y códigos de estado.
- Contrato de errores estandarizado.
- Documentación inicial de la API lista para consumo.

**Errores comunes:**
- Incluir verbos en las URLs (`/crearUsuario`) en vez de usar métodos HTTP.
- Mezclar singular y plural en los recursos.
- Devolver 200 OK para errores de validación en lugar de 400 o 422.
- Omitir versionamiento y romper clientes existentes al evolucionar la API.

**Referencias cruzadas:**
- [1.1.1 Introducción a Web Service de tipo API REST](./01-introduccion-a-servicio-web-rest.md)
- [1.1.3 JSON y Mejores Prácticas en APIs RESTful](./03-formato-json-buenas-practicas.md)
- [1.1.5 Documentación y Mejora Continua](./05-documentacion-mejora-continua.md)
</div>
