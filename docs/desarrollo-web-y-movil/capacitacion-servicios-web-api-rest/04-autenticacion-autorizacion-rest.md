---
sidebar_position: 4
sidebar_label: 1.1.4 Autenticación y Autorización en APIs RESTful
---

# Autenticación y Autorización en APIs RESTful

Este módulo presenta los conceptos fundamentales de autenticación y autorización en el contexto de APIs RESTful y cómo aplicar buenas prácticas para garantizar la seguridad de la API, incluyendo los controles transversales que aplican cuando una **SPA** la consume desde el navegador.

## Contenido

### Introducción a la Autenticación y Autorización

- **Autenticación**: Proceso de verificación de identidad del usuario o entidad que *intenta acceder al servicio*.
- **Autorización**: Proceso de determinar los permisos o acceso que tiene un *usuario autenticado* para acceder a ciertos recursos de la API.
- **SPA (Single Page Application)**: Aplicación web que carga una sola vez en el navegador y actualiza la interfaz con JavaScript consumiendo una API vía HTTP (típicamente JSON sobre HTTPS), sin solicitar páginas completas al servidor en cada interacción. Desde la perspectiva de la API, una SPA es un cliente autenticado más, con la particularidad de ejecutarse dentro de un navegador y heredar sus controles (cookies, CORS, XSS, CSRF).

![autorización y autenticacion](./img/autenticacion-autorizacion-01.png)

### Tipos de Autenticación Comunes en APIs RESTful

#### 2.1 Autenticación Básica

- La **autenticación básica** es un método simple en el cual el usuario proporciona un nombre de usuario y una contraseña. Estos se envían codificados en Base64 en cada solicitud. Este método es fácil de implementar, pero tiene importantes limitaciones de seguridad si no se utiliza con HTTPS.
- **Header utilizado**: En la autenticación básica, las credenciales se envían en el **header** `Authorization` con el valor `Basic <credenciales_codificadas>`. Las credenciales se codifican en Base64 en el formato `username:password`.
- **Desventaja principal**: La información de autenticación viaja con cada solicitud, lo cual es inseguro si no está cifrado.

**Ejemplo:**

- Supongamos que tienes un usuario con el nombre de usuario `admin` y la contraseña `password123`.
- Primero, se concatenan las credenciales en el formato `username:password` -> `admin:password123`.
- Luego, se codifican en Base64 -> `YWRtaW46cGFzc3dvcmQxMjM=`.
- Finalmente, se envían en el header `Authorization` con el valor `Basic YWRtaW46cGFzc3dvcmQxMjM=`.

```http
GET /api/recursos HTTP/1.1
Host: ejemplo.com
Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=
```

#### 2.2 Autenticación con Tokens

- **Autenticación basada en tokens**: El usuario se autentica una vez y, si es exitoso, el servidor genera un **token** (un identificador único) que el cliente debe enviar en cada solicitud posterior. Este token tiene una duración limitada y puede ser invalidado si es necesario.
- **Header utilizado**: El **token** se envía en el **header** `Authorization` con el valor `Bearer <token>`. Esto permite que el servidor valide cada solicitud usando el token proporcionado.
- Los tokens proporcionan un método más seguro y eficiente para autenticar múltiples solicitudes, ya que no se necesita enviar credenciales en cada una.

**Ejemplo:**

- Supongamos que un usuario se autentica correctamente y el servidor genera un token JWT.
- El token tiene el siguiente aspecto: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`.
- El cliente debe enviar este token en el header `Authorization` con el valor `Bearer <token>`.

```http
GET /api/recursos HTTP/1.1
Host: ejemplo.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

#### 2.3 ¿Cómo se utilizan?

El flujo recomendado para **aplicaciones web** mantiene el token fuera del alcance de JavaScript: el backend lo entrega en una cookie `HttpOnly; Secure; SameSite` y el navegador la reenvía automáticamente en cada request. El frontend **nunca lee ni maneja el token directamente**.

```mermaid
sequenceDiagram
    autonumber
    actor Usuario
    participant Web as App Web (frontend)
    participant API as API REST (backend)

    Usuario->>Web: Ingresa credenciales
    Web->>API: POST /api/autenticar con Basic base64(user pass)
    Note right of API: Verifica credenciales contra la base<br/>Resuelve roles y permisos del usuario<br/>Emite un JWT firmado con clave privada

    API-->>Web: 200 OK + Set-Cookie auth_token
    Note left of Web: La cookie viaja con atributos<br/>HttpOnly (JS no la lee)<br/>Secure (solo HTTPS)<br/>SameSite=Strict (no cross-site)
    Web-->>Usuario: Sesión iniciada

    Usuario->>Web: Ejecuta acción (ej. listar recursos)
    Web->>API: GET /api/recursos (navegador adjunta cookie)
    Note right of API: Verifica firma del JWT (no fue alterado)<br/>Verifica exp (no expiró)<br/>Verifica iss y aud (emitido para este API)<br/>Compara rol y scope del token vs. la operación
    API-->>Web: 200 OK + JSON
    Web-->>Usuario: Respuesta
```

:::tip Para clientes no navegador
Apps móviles nativas, CLIs o integraciones servidor-a-servidor **sí** usan `Authorization: Bearer <token>`, porque no tienen un `document.cookie` que proteger con `HttpOnly`. Ver 2.4 para el criterio de decisión.
:::

#### 2.4 Entrega del token: cookie `HttpOnly` vs. header `Authorization`

La elección del mecanismo de transporte del token es una decisión de arquitectura de seguridad, no de comodidad. Define qué componente custodia el secreto, qué clases de ataque son posibles y qué controles compensatorios deben implementarse. En el marco de OAuth 2.0 y las guías de la OWASP API Security Top 10, el criterio se determina por el tipo de cliente.

**Separación de responsabilidades**

| Actor | Responsabilidad |
|-------|-----------------|
| **Authorization Server** (endpoint de login del API) | Verifica credenciales, firma el JWT y lo entrega al cliente a través del canal definido (cookie o body de respuesta). |
| **Resource Server** (los endpoints de negocio del API) | Recibe el token en cada request, valida firma y claims (`iss`, `aud`, `exp`), y autoriza la operación. **No persiste el token**: los JWT son *stateless* por diseño. |
| **Cliente** | Custodia el token durante su ciclo de vida y lo presenta en cada request. La forma de custodia depende de la plataforma donde corre el cliente y es su responsabilidad, no del API. |

**Mecanismos de transporte**

| Tipo de cliente | Mecanismo | Justificación |
|-----------------|-----------|---------------|
| Navegador (SPA o server-rendered sobre el mismo dominio del API) | Cookie `HttpOnly; Secure; SameSite` emitida vía `Set-Cookie` | El atributo `HttpOnly` excluye el token del alcance de `document.cookie` y del DOM, neutralizando la exfiltración directa por XSS. El envío automático del navegador elimina la necesidad de custodia manual en el código cliente. |
| Aplicación móvil, de escritorio, CLI o servicio servidor-a-servidor | Header `Authorization: Bearer <token>` ([RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)) | No existe un equivalente al atributo `HttpOnly` fuera del navegador. La custodia recae en el cliente, que debe usar el almacenamiento seguro provisto por su plataforma (el catálogo específico queda fuera del alcance de este módulo, que se enfoca en la API). |

**Garantías que la API debe sostener — independientes del mecanismo**

- Transporte exclusivamente sobre TLS 1.2 o superior. Ningún endpoint que acepte tokens debe responder por HTTP.
- Ausencia de tokens en logs, trazas, mensajes de error o respuestas destinadas a diagnóstico.
- Rotación periódica de la clave de firma y publicación de la clave pública vía JWKS cuando se usan algoritmos asimétricos.
- Rechazo explícito de `alg: none` y de algoritmos no declarados en la política del Authorization Server.
- Ventanas de expiración cortas (`exp`) complementadas con refresh tokens de mayor duración emitidos bajo controles adicionales.

:::danger Almacenamiento prohibido para tokens
`localStorage`, `sessionStorage`, archivos en texto plano versionados, credenciales hardcodeadas y cualquier destino que aparezca en logs. Un token que haya pasado por uno de estos canales debe tratarse como comprometido e invalidarse inmediatamente.
:::

**Por qué la cookie `HttpOnly` es la vía preferida en navegador:** si un atacante logra inyectar JavaScript en la página (XSS), con la cookie `HttpOnly` **no puede leer el token**. Con el token en `localStorage` o variables JavaScript, la exfiltración es trivial.

**Flujo con cookie `HttpOnly` (paso a paso, complementa el diagrama de 2.3):**

1. El usuario envía credenciales al endpoint de login.
2. El backend valida, emite el JWT, y **lo devuelve en un header `Set-Cookie`** con los atributos `HttpOnly; Secure; SameSite=Strict` (o `Lax` según el caso). El frontend recibe una respuesta **sin token visible en el body** — solo confirmación de sesión y datos del usuario.
3. El navegador guarda la cookie asociada al dominio del backend.
4. En cada request posterior, el navegador **adjunta la cookie automáticamente**; el código JS del frontend no manipula tokens.
5. El backend lee la cookie, valida firma y expiración, y autoriza o rechaza la operación.

**Ejemplo de respuesta del login:**

```http
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: auth_token=eyJhbGc...SflKxwR; HttpOnly; Secure; SameSite=Strict; Path=/; Max-Age=3600

{ "user": { "id": 42, "name": "John Doe", "roles": ["editor"] } }
```

**Ejemplo de request posterior — el frontend no ve ni envía el token manualmente:**

```http
GET /api/recursos HTTP/1.1
Host: ejemplo.com
Cookie: auth_token=eyJhbGc...SflKxwR
```

**Consideraciones adicionales cuando se usa cookie `HttpOnly`:**

- **CSRF:** al enviar cookies automáticamente, el sitio se vuelve susceptible a Cross-Site Request Forgery. Mitigaciones: `SameSite=Strict` (bloquea envío cross-site para la mayoría de casos), **token anti-CSRF** en un header custom (`X-CSRF-Token`) validado en mutaciones, o verificación del header `Origin`/`Referer` en el backend.
- **CORS:** si el frontend y el backend viven en dominios distintos, el backend debe responder con `Access-Control-Allow-Credentials: true` y un `Access-Control-Allow-Origin` **específico** (no `*`), y el cliente debe enviar `fetch(..., { credentials: 'include' })`.
- **Refresh de sesión:** el refresh token también debe ser cookie `HttpOnly`, preferiblemente con `Path` restringido al endpoint de refresh (`Path=/auth/refresh`), para que no viaje en cada request normal.

#### 2.5 Sesiones vs. Tokens

- **Sesiones**: Se guardan en el servidor y requieren que el servidor mantenga un estado por cada usuario, lo cual no es ideal para aplicaciones altamente escalables.
- **Tokens**: No requieren que el servidor guarde información del estado del usuario, lo cual los hace más adecuados para arquitecturas distribuidas.

#### 2.6 Autenticación Multifactor (MFA)

**MFA (Multi-Factor Authentication)** es un control que exige al usuario presentar **dos o más pruebas de identidad de distinta naturaleza** antes de emitir un token de sesión. Las categorías clásicas son: *algo que sabes* (contraseña), *algo que tienes* (código temporal enviado a un correo, SMS o generado por app autenticadora tipo TOTP) y *algo que eres* (biometría). "Credenciales correctas" deja de equivaler a "sesión iniciada": el API distingue un estado intermedio (*autenticación parcial*) hasta que se valida el segundo factor.

**Contrato HTTP típico entre API y cliente (SPA u otro):**

1. El cliente envía credenciales al endpoint de login.
2. Si el usuario tiene MFA habilitado, el API **no emite token todavía**. Responde con un status que indique *MFA requerido* (por ejemplo `202 Accepted` con un identificador de desafío) y dispara la entrega del segundo factor (correo, SMS, notificación push, etc.).
3. El cliente cambia de vista y solicita al usuario el código.
4. El cliente envía el código a un endpoint de verificación MFA.
5. Si el código es válido dentro de su ventana de tiempo, el API emite el token real (cookie `HttpOnly` o Bearer) y la sesión queda establecida.

**Diagrama de flujo — MFA basado en código de un solo uso:**

```mermaid
sequenceDiagram
    autonumber
    actor Usuario
    participant SPA as App Web (SPA)
    participant API as API REST (backend)
    participant Correo as Servicio de correo

    Usuario->>SPA: Ingresa usuario y contraseña
    SPA->>API: POST /api/autenticar con Basic base64(user pass)
    Note right of API: Verifica credenciales<br/>Detecta MFA habilitado<br/>Genera código de un solo uso<br/>Asocia código a usuario con TTL

    API->>Correo: Enviar código al correo registrado
    API-->>SPA: 202 Accepted con MfaRequired
    Note left of SPA: Guarda identificador del desafío<br/>(no el token — aún no existe)<br/>Cambia a pantalla de MFA

    Correo-->>Usuario: Correo con código de 6 dígitos
    Usuario->>SPA: Ingresa el código recibido
    SPA->>API: POST /api/validar-mfa con usuario y código
    Note right of API: Verifica código contra el emitido<br/>Verifica que no haya expirado<br/>Verifica intentos dentro del límite<br/>Emite JWT firmado

    API-->>SPA: 200 OK + Set-Cookie auth_token
    Note left of SPA: Sesión establecida<br/>Descarta identificador del desafío
    SPA-->>Usuario: Accede al sistema
```

**Decisiones de diseño que la API debe resolver:**

- **Ventana de vigencia del código.** Corta (típicamente 5 a 10 minutos). Pasado ese tiempo, exigir reenvío.
- **Límite de intentos.** Cerrar el desafío después de *N* intentos fallidos (por ejemplo 3 a 5) y forzar reinicio del flujo.
- **Reenvío con cooldown.** Permitir al usuario pedir un nuevo código, pero con un intervalo mínimo (por ejemplo 180 segundos) para evitar abuso del canal de entrega.
- **Aislamiento del estado parcial.** Entre paso 1 y paso 4 el servidor puede mantener un desafío temporal (identificador + código + intentos restantes), preferentemente en caché con expiración automática. Nunca emitir un token "de paso" que el cliente pueda usar para consumir recursos.
- **Auditoría.** Registrar intentos de validación (éxito y fallo) con marca de tiempo, IP y agente — insumo clave para detectar ataques dirigidos.

### Tokens de Autenticación

#### 3.1 JWT (JSON Web Token)

- **JWT** es un estándar para representar **claims** de forma segura entre dos partes. Un JWT está compuesto de tres partes: **header**, **payload**, y **signature**.
  - **Header**: Contiene el tipo de token (JWT) y el algoritmo de cifrado utilizado.
  - **Payload**: Incluye la información (claims) sobre el usuario y otros datos relevantes, como roles o permisos.
  - **Signature**: Se utiliza para verificar que el token no ha sido modificado.

**Claims estándar más usados al validar un JWT** (definidos en el [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1)):

| Claim | Nombre completo | Qué contiene | Por qué se valida |
|-------|-----------------|--------------|-------------------|
| `iss` | *issuer* (emisor) | URL o identificador del servicio que **emitió** el token, por ejemplo `https://auth.miempresa.com`. | Asegura que el token viene de un emisor de confianza. Si la API recibe un token con un `iss` distinto al esperado, lo rechaza aunque la firma sea matemáticamente válida. |
| `aud` | *audience* (audiencia) | Identificador de la API o servicio para el cual **fue emitido** el token, por ejemplo `api.miempresa.com/v1`. | Evita que un token emitido para otra API sea aceptado por esta. |
| `exp` | *expiration* | Timestamp Unix en que el token deja de ser válido. | Limita el daño si el token se filtra. |
| `sub` | *subject* | Identificador del usuario o entidad dueña del token. | Es la "identidad" que la API asocia a la sesión. |
| `iat` | *issued at* | Timestamp Unix en que fue emitido. | Útil para auditoría y para rechazar tokens muy viejos. |
| `jti` | *JWT ID* | Identificador único del token. | Permite revocación individual (lista negra de `jti`). |

Un token bien firmado pero con `iss` desconocido o `aud` distinto al de la API debe rechazarse — la firma sola **no garantiza que el token fue emitido para ti**.

#### 3.2 Ventajas de JWT

- **Escalabilidad**: Al ser **stateless** (sin estado), no se requiere almacenar la sesión en el servidor, lo cual permite escalar la API más fácilmente.
- **Portabilidad**: Los tokens se pueden usar en diferentes dominios y servicios, lo cual facilita la integración con otros sistemas.

#### 3.3 Buenas Prácticas en el Uso de Tokens

- **Tiempos de expiración cortos:** el token de acceso debe durar minutos, no horas. Complementar con un refresh token de mayor duración para renovar sin pedir credenciales otra vez.
- **Almacenamiento en el navegador:** la vía recomendada es **cookie `HttpOnly` + `Secure` + `SameSite`** (ver 2.4). `localStorage` y `sessionStorage` son accesibles desde cualquier XSS y **no deben usarse para tokens** en producción.
- **Firma robusta:** usar algoritmos asimétricos (RS256, ES256) cuando varios servicios validan el token; HS256 solo si un único servicio firma y verifica. Nunca aceptar `alg: none`.
- **Validar siempre:** firma, expiración (`exp`), emisor (`iss`) y audiencia (`aud`). Un token bien firmado pero dirigido a otra audiencia no debe aceptarse.
- **Revocación:** los JWT son *stateless* por diseño, pero un caso real (logout, cambio de contraseña, usuario baneado) requiere invalidación. Opciones: lista de tokens revocados en caché, sesiones de corta duración, o `jti` (JWT ID) contra una lista negra.
- **Alcance mínimo (`scope`):** emitir tokens con el conjunto más pequeño de permisos necesarios para la operación.

### Principios de Autorización

#### 4.1 Roles y Permisos

- **Roles**: Un rol es una agrupación lógica de permisos. Por ejemplo, un rol de **administrador** puede tener permisos de lectura, escritura, y eliminación.
- **Permisos**: Son las acciones específicas que un usuario puede realizar. Los permisos definen qué operaciones están permitidas para un rol o usuario específico.

#### 4.2 Scope o Ámbito de Permisos

- El **scope** es un mecanismo para limitar el acceso a diferentes operaciones dentro de la API. Por ejemplo, un **token** puede tener un scope limitado a operaciones de solo lectura, lo cual previene modificaciones no autorizadas.

#### 4.3 Autorización Basada en Claims

- Los **claims** son atributos que se agregan al **payload** de un token para definir roles, permisos u otros atributos que permitan al servidor decidir si un usuario puede realizar una acción específica.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "roles": ["admin", "editor"],
  "iat": 1516239022
}
```

### Buenas Prácticas de Seguridad

- **Utilizar SSL**: Asegúrate de que todas las comunicaciones entre el cliente y el servidor se realicen mediante SSL (Secure Sockets Layer) para cifrar los datos transmitidos y protegerlos contra ataques de interceptación.
- **Utilizar autenticación por medio de tokens de seguridad**: Implementar autenticación basada en tokens para evitar la necesidad de almacenar sesiones en el servidor, proporcionando así un método más seguro y escalable.

#### 5.1 Validación de Tokens

- Es fundamental validar que un token sea auténtico antes de permitir el acceso. La validación de tokens implica verificar la **firma** y asegurarse de que no haya sido modificado.
- Además, se debe revisar si el token ha expirado o si ha sido revocado.

#### 5.2 Implementación de HTTPS

- Utilizar **HTTPS** en todas las solicitudes para garantizar que la comunicación entre el cliente y el servidor esté cifrada y segura. Esto evita que un atacante pueda interceptar el tráfico y robar información confidencial.

#### 5.3 Rotación y Expiración de Tokens

- **Rotación de tokens**: Implementar la rotación de tokens permite generar nuevos tokens antes de que los anteriores expiren, lo cual asegura una continuidad segura del acceso.
- **Expiración**: Los tokens deben tener un tiempo de vida limitado. Al expirar, se debe requerir al usuario que se autentique nuevamente.

#### 5.4 CORS (Cross-Origin Resource Sharing)

**CORS** es un mecanismo definido por el navegador ([especificación del W3C](https://fetch.spec.whatwg.org/#http-cors-protocol)) mediante el cual una página servida desde un **origen** (`protocolo + dominio + puerto`) puede solicitar recursos a un API alojada en **otro origen** solo si el API lo autoriza explícitamente con los encabezados `Access-Control-Allow-*`. Es una política del lado del navegador, no una barrera del servidor: un cliente no-navegador ignora CORS por completo. Su propósito es evitar que una página maliciosa en un dominio cualquiera haga peticiones autenticadas contra APIs donde el usuario ya tiene sesión.

**Reglas para una API consumida por SPAs desde otro origen:**

- `Access-Control-Allow-Origin` debe devolver un **origen específico** (lista blanca de SPAs autorizadas), nunca `*` cuando la sesión viaja por cookies.
- `Access-Control-Allow-Credentials: true` es obligatorio si el navegador debe adjuntar cookies, y el cliente debe llamar al API con la opción equivalente a `credentials: 'include'`.
- `Access-Control-Allow-Methods` y `Access-Control-Allow-Headers` se limitan al conjunto realmente usado por la SPA. Una lista permisiva amplía superficie de ataque sin ganancia funcional.
- Las peticiones con método distinto a `GET`/`HEAD`/`POST`-simple disparan un **preflight** (`OPTIONS`), que el backend debe responder con los encabezados correctos antes del request real.

Una configuración CORS permisiva combinada con cookies de sesión habilita escenarios de CSRF cross-site aunque el backend crea tener `SameSite`. El origen permitido debe reflejar exactamente los hosts de frontend en producción.

#### 5.5 Rate Limiting y respuesta `429`

**Rate limiting** es el control que restringe el número de peticiones que un cliente puede hacer a un endpoint en una ventana de tiempo. Su objetivo no es sustituir autenticación ni autorización, sino **contener abuso**: fuerza bruta contra login, enumeración de cuentas, scraping, agotamiento de recursos.

Cuando se supera el umbral, la API responde con `429 Too Many Requests` y, opcionalmente, el encabezado `Retry-After` indicando cuántos segundos debe esperar el cliente antes de reintentar.

**Endpoints donde el rate limiting aporta más valor:**

- **Login y verificación MFA.** Primera línea de defensa contra fuerza bruta de credenciales y códigos.
- **Recuperación o reseteo de contraseña.** Evita enumeración de cuentas y abuso del canal de correo/SMS.
- **Registro de cuentas.** Previene creación masiva de cuentas automatizadas.
- **Reenvío de códigos (MFA, confirmación).** Cooldown por cuenta y por IP.
- **Endpoints públicos sin autenticación previa.** Controla scraping y denegación de servicio.

**Criterios para definir los límites:**

- **Clave de conteo:** IP, cuenta, token, origen — típicamente combinada (por ejemplo "5 intentos de login por IP por minuto **y** 10 por cuenta por hora").
- **Ventana:** deslizante (`sliding window`) para evitar ráfagas al cambio de minuto; de contador fijo si se prioriza simplicidad.
- **Distribución:** si el API corre en varias instancias, el contador debe vivir en un almacén compartido (Redis, Memcached) — nunca en memoria de una sola instancia.

#### 5.6 La decisión final de autorización vive en el servidor

La interfaz del cliente puede ocultar botones, menús o vistas según el rol del usuario — es una mejora de experiencia, no un control de seguridad. **Todo endpoint debe validar permisos en el servidor** antes de ejecutar la operación, incluso si el cliente "nunca debería" haberlo invocado: un atacante puede abrir la consola del navegador, llamar al endpoint directamente con `curl` o con Postman, o modificar el JavaScript que corre en su máquina. La autorización en el cliente es **cosmética**; la autorización en el servidor es la única que cuenta.

#### 5.7 Observabilidad y respuesta ante incidentes

Los controles de seguridad solo aprenden del uso real si se miden. La API debería emitir señales suficientes para detectar abuso temprano:

- **Autenticación:** conteo de intentos fallidos por cuenta y por IP, tasa de MFA fallido, tiempo promedio del flujo completo de login.
- **Autorización:** conteo de respuestas `403 Forbidden` por endpoint y por usuario.
- **Rate limiting:** conteo de respuestas `429` por endpoint, claves más rate-limited.
- **Sesiones:** tokens emitidos, refreshes, logouts forzados, revocaciones.
- **Tráfico anómalo:** incrementos súbitos en un endpoint, patrones fuera de horarios esperados.

Estos datos alimentan alertas (ej. "más de 50 `401` por minuto en login" o "tasa de `403` diez veces superior al promedio en endpoint X") que permiten detener un ataque en curso antes de que se convierta en compromiso.

### Casos de Uso Comunes

#### 6.1 Autenticación de Usuario para Recursos Restringidos

- Para acceder a un recurso restringido, el usuario debe autenticarse primero. Si el proceso es exitoso, se le otorga un **token** que debe ser enviado con cada solicitud para acceder a recursos protegidos.

#### 6.2 Diferentes Permisos para Distintos Roles de Usuario

- Diferentes roles (por ejemplo, **administrador**, **usuario estándar**) tienen distintos niveles de acceso. Los permisos específicos que cada rol tiene deben ser definidos claramente para evitar accesos no autorizados a recursos críticos.

## Glosario

**Autenticación** *(Authentication)* — proceso de verificar la identidad del usuario o sistema que intenta acceder a la API.

**Autorización** *(Authorization)* — proceso de determinar qué acciones puede realizar un usuario ya autenticado.

**JWT** *(JSON Web Token)* — estándar para representar claims entre partes de forma firmada ([RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)).

**Bearer token** *(Bearer token)* — token cuyo poseedor puede acceder a los recursos; se envía en `Authorization: Bearer <token>` según [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750).

**Cookie HttpOnly** *(HttpOnly cookie)* — cookie inaccesible para JavaScript (`document.cookie`), usada para mitigar XSS sobre tokens.

**MFA** *(Multi-Factor Authentication)* — exigencia de dos o más factores de distinta naturaleza (sabes / tienes / eres) para autenticar.

**CORS** *(Cross-Origin Resource Sharing)* — mecanismo del navegador que controla qué orígenes pueden consumir una API desde otro dominio.

**CSRF** *(Cross-Site Request Forgery)* — ataque que aprovecha cookies de sesión para ejecutar acciones no deseadas en nombre del usuario.

**Rate limiting** *(Rate limiting)* — límite al número de peticiones por ventana de tiempo; respuesta típica `429 Too Many Requests`.

**Scope** *(Scope)* — alcance de permisos asociado a un token; limita las operaciones que puede ejecutar.

:::info Referencias primarias
- [RFC 7519 JSON Web Token](https://datatracker.ietf.org/doc/html/rfc7519) — especificación oficial de JWT.
- [RFC 6750 Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc6750) — uso de tokens Bearer en OAuth 2.0.
- [OWASP API Security Top 10](https://owasp.org/API-Security/) — riesgos críticos en APIs.
- [Fetch · CORS protocol](https://fetch.spec.whatwg.org/#http-cors-protocol) — especificación CORS del WHATWG.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** diseñar el esquema de autenticación y autorización de una API REST considerando clientes navegador y no-navegador.

**Entradas:**
- Tipos de clientes que consumirán la API (SPA, móvil, CLI, servidor).
- Requisitos de MFA, roles y alcances.
- Nivel de riesgo de los endpoints expuestos.
- Políticas de seguridad vigentes (TLS, rotación de claves, auditoría).

**Pasos:**
1. Elegir transporte del token: cookie `HttpOnly; Secure; SameSite` para navegador; `Authorization: Bearer` para clientes no-navegador.
2. Definir claims JWT a validar (`iss`, `aud`, `exp`, `sub`, `jti`) y algoritmo de firma.
3. Configurar expiración corta de tokens de acceso y refresh tokens con alcance restringido.
4. Incorporar MFA donde el riesgo lo justifique, con ventana, reintentos y cooldown.
5. Aplicar rate limiting a login, MFA, registro y recuperación de contraseña.
6. Blindar CORS y CSRF cuando la sesión viaje por cookies.
7. Validar permisos en el servidor en cada endpoint, sin confiar en el cliente.
8. Instrumentar observabilidad (`401`, `403`, `429`, MFA fallido) para detección temprana.

**Salidas:**
- Esquema de autenticación documentado por tipo de cliente.
- Políticas de rotación, expiración y revocación de tokens.
- Métricas y alertas asociadas a autenticación y autorización.

**Errores comunes:**
- Almacenar tokens en `localStorage` o `sessionStorage`.
- Aceptar tokens con `alg: none` o sin validar `aud`/`iss`.
- Tratar la autorización del cliente como control de seguridad.
- Omitir rate limiting en endpoints sensibles como login o reset de contraseña.

**Referencias cruzadas:**
- [1.1.2 Componentes Básicos de un Servicio Web tipo API REST](./02-componentes-basicos-api-rest.md)
- [1.3.5 SAST y SCA en la fase de validación](../fundamentos-sonarqube/05-sast-y-sca-en-validacion.md)
- [6.4 Seguridad al ejecutar herramientas externas](../../colaboracion-con-agentes-ia/04-seguridad-en-herramientas-externas.md)
</div>
