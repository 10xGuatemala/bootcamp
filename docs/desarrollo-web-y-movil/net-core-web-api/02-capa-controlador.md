# La Capa de Controlador en un Backend API REST

La capa de controlador en un proyecto de API REST es la interfaz principal entre el cliente y la lógica de negocio. Los controladores reciben las solicitudes HTTP, las procesan utilizando servicios, y devuelven respuestas al cliente. A continuación, se explicará con ejemplos simples los conceptos principales:

## Ejemplo Básico de un Controlador

```csharp
[Route("api/[controller]")]
[Authorize]
[ApiController]
public class EmpleadosController : ControllerBase
{
    // Servicio
    private readonly EmpleadoService _empleadoService;

    // La inyección del servicio se hace en el constructor
    public EmpleadosController(EmpleadoService empleadoService)
    {
        _empleadoService = empleadoService;
    }

    /// <summary>
    /// Registrar un nuevo empleado.
    /// </summary>
    /// <param name="request">Datos del empleado a registrar.</param>
    /// <returns>Empleado creado.</returns>
    [HttpPost]
    [Authorize(Roles = "Administrador, RecursosHumanos")]
    public async Task<IActionResult> CrearEmpleado([FromBody] EmpleadoRequest request)
    {
        var empleado = await _empleadoService.CrearAsync(request);
        return CreatedAtAction(nameof(CrearEmpleado), new { id = empleado.Id }, empleado);
    }

    /// <summary>
    /// Obtener un empleado por su ID.
    /// </summary>
    /// <param name="id">ID del empleado.</param>
    /// <returns>Empleado solicitado.</returns>
    [HttpGet("{id}")]
    public async Task<IActionResult> ObtenerEmpleado(int id)
    {
        var empleado = await _empleadoService.ObtenerPorIdAsync(id);
        if (empleado == null)
            return NotFound();
        return Ok(empleado);
    }
}
```

Este ejemplo muestra cómo definir rutas, autorizar accesos y recibir solicitudes del cliente.

## Explicación Detallada

### 1. Cabeceras Básicas del Controlador

- **`[Route("api/[controller]")]`**: Define la ruta base para las acciones del controlador. El patrón `[controller]` se reemplaza automáticamente por el nombre de la clase controlador (sin el sufijo "Controller"), en este caso `Empleados`.

- **`[ApiController]`**: Define que el controlador maneja solicitudes de API REST, habilitando características como la validación automática de modelos y respuestas automáticas en caso de errores de validación. Esto significa que si los datos enviados en la solicitud no cumplen con las reglas definidas en el modelo, el framework generará automáticamente una respuesta de error con detalles del problema, sin necesidad de código adicional.

- **`ControllerBase`**: Es una clase base diseñada específicamente para controladores de APIs RESTful. Ofrece métodos útiles como `Ok()`, `NotFound()`, `BadRequest()`, y `CreatedAtAction()` que simplifican el manejo de solicitudes y respuestas HTTP comunes. A diferencia de `Controller`, no incluye funcionalidades relacionadas con vistas, lo que la hace más ligera y adecuada para servicios API.

**Ejemplo**:

```csharp
return Ok(empleado); // Devuelve una respuesta HTTP 200 con el objeto empleado
return NotFound(); // Devuelve una respuesta HTTP 404 si el empleado no se encuentra
```

### 2. Autorización con `[Authorize]`

La anotación `[Authorize]` se utiliza para restringir el acceso a ciertos controladores o métodos únicamente a usuarios autenticados. `[Authorize]` verifica si el usuario está autenticado antes de permitir el acceso a un recurso. Esto es fundamental para asegurar que solo usuarios autorizados puedan acceder a recursos sensibles o ejecutar ciertas acciones.

#### Niveles de Uso de `[Authorize]`

- **A nivel de clase**: Al aplicar `[Authorize]` a nivel de clase, todas las acciones dentro de ese controlador requieren autenticación.
  
  ```csharp
  [Authorize]
  [Route("api/[controller]")]
  [ApiController]
  public class EmpleadosController : ControllerBase
  {
      // Todas las acciones requieren autenticación
  }
  ```

- **A nivel de método**: También puedes aplicar `[Authorize]` a nivel de método individual para restringir el acceso solo a ciertas acciones específicas.
  
  ```csharp
  [HttpPost]
  [Authorize]
  public IActionResult CrearEmpleado()
  {
      // Solo esta acción requiere autenticación
  }
  ```

#### Tipos de Acceso con `[Authorize]`

- **Sin roles**: Al usar `[Authorize]` sin parámetros adicionales, solo se verifica si el usuario está autenticado. Esto significa que cualquier usuario autenticado puede acceder al recurso.
  
  ```csharp
  [Authorize]
  public IActionResult ObtenerDatos()
  {
      // Cualquier usuario autenticado puede acceder
  }
  ```

- **Con roles específicos**: Se puede restringir el acceso a usuarios con roles específicos usando el parámetro `Roles`.
  
  ```csharp
  [Authorize(Roles = "Administrador, RecursosHumanos")]
  public IActionResult CrearEmpleado()
  {
      // Solo los usuarios con los roles "Administrador" o "RecursosHumanos" pueden acceder
  }
  ```

- **Permitir acceso anónimo**: En algunos casos, es posible que se desee permitir el acceso sin autenticación, incluso si `[Authorize]` está configurado a nivel de clase. Para ello, se puede usar `[AllowAnonymous]`.
  
  ```csharp
  [AllowAnonymous]
  public IActionResult ObtenerInformacionPublica()
  {
      // Cualquier usuario, autenticado o no, puede acceder
  }
  ```

### 3. Configuración de Autorización en Program.cs

Para usar `[Authorize]`, debes configurar la autenticación y la autorización en `Program.cs`. Esto se hace para definir cómo el sistema autentica y autoriza a los usuarios. Por ejemplo:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Configuración de autenticación (por ejemplo, autenticación JWT)
builder.Services.AddAuthentication("Bearer")
    .AddJwtBearer("Bearer", options =>
    {
        options.Authority = "https://tu-autoridad";
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateAudience = false
        };
    });

var app = builder.Build();

app.UseAuthentication(); // Middleware para la autenticación
app.UseAuthorization();  // Middleware para la autorización

app.MapControllers();
app.Run();
```

### 4. Uso de DTOs y Validaciones

Cuando se reciben datos a través del cuerpo de la solicitud (`[FromBody]`), lo ideal es definir una clase DTO (Data Transfer Object). Los DTO ayudan a controlar los datos que entran a la aplicación, permitiendo la validación y evitando problemas de seguridad.

**Ejemplo de Clase DTO con Validaciones**:

```csharp
public class EmpleadoRequest
{
    [Required]
    public string Nombre { get; set; }

    [EmailAddress]
    public string CorreoElectronico { get; set; }

    [Range(18, 65)]
    public int Edad { get; set; }
}
```

**Atributos de Validación Comunes**:

- `[CreditCard]`: Valida que la propiedad tenga formato de tarjeta de crédito.
- `[Compare]`: Valida que dos propiedades en un modelo coincidan.
- `[EmailAddress]`: Valida que la propiedad tenga formato de correo electrónico.
- `[Phone]`: Valida que la propiedad tenga formato de teléfono.
- `[Range]`: Valida que el valor de la propiedad esté dentro del rango especificado.
- `[RegularExpression]`: Valida que los datos coincidan con la expresión regular especificada.
- `[Required]`: Hace que una propiedad sea obligatoria.
- `[StringLength]`: Valida que una propiedad de tipo cadena tenga como máximo la longitud especificada.
- `[Url]`: Valida que la propiedad tenga formato de URL.

Estas validaciones aseguran que los datos enviados por el cliente cumplan con ciertos requisitos antes de ser procesados por la aplicación.

### 5. Importancia de la Documentación

La documentación adecuada de los controladores y sus métodos es crucial para que otros desarrolladores puedan entender y usar tu API.

- **Comentarios XML**: Los comentarios `///` describen cada acción, sus parámetros y respuestas esperadas.
  - **Ejemplo**:
  
    ```csharp
    /// <summary>
    /// Registrar un nuevo empleado.
    /// </summary>
    /// <param name="request">Datos del empleado a registrar.</param>
    /// <returns>Empleado creado.</returns>
    ```

- **Swagger/OpenAPI**: Herramientas como Swagger permiten generar una interfaz interactiva basada en los comentarios XML. Esto facilita la prueba de los métodos de la API y la comprensión de sus funcionalidades.

## Resumen

- **Anotaciones del Controlador**:
  - Usa `[Route("api/[controller]")]` para definir la ruta base del controlador.
  - Usa `[ApiController]` para habilitar características RESTful como la validación automática.
  - Heredar de `ControllerBase` permite una estructura ligera y enfocada para las APIs.

- **Autorización**:
  - Usa `[Authorize]` para restringir acceso a los recursos según la autenticación del usuario.
  - Configura roles específicos para controlar el acceso con mayor precisión.

- **Parámetros y Validaciones**:
  - Define clases DTO para recibir datos y utiliza atributos de validación para garantizar que los datos sean correctos.

- **Documentación**:
  - Implementa comentarios XML y herramientas como Swagger para facilitar la comprensión y el uso de tu API.

Con estos conceptos, los desarrolladores junior pueden estructurar controladores efectivos y bien documentados, asegurando claridad y funcionalidad en una API REST. Si bien la información aquí es detallada, recomiendo avanzar paso a paso, comprendiendo primero las bases antes de abordar funcionalidades más avanzadas.
