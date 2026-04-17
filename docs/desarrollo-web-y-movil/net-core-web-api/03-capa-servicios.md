# La Capa de Servicios en un Backend API REST

Los servicios en un backend API REST encapsulan la lógica de negocio y sirven como la capa intermedia entre los controladores y la capa de acceso a datos. Esta separación de responsabilidades promueve un diseño más limpio y modular, facilitando el mantenimiento y la escalabilidad del sistema.

---

## ¿Qué es un Servicio?

Un servicio en una API REST se encarga de implementar la lógica de negocio de la aplicación. Los controladores gestionan las solicitudes HTTP y delegan las operaciones de negocio a los servicios, que a su vez interactúan con la capa de acceso a datos. Esta arquitectura ayuda a mantener el código más limpio y facilita la reutilización de la lógica de negocio.

## Ejemplo de Servicio: `EmpleadoService`

El siguiente ejemplo muestra cómo crear un servicio llamado `EmpleadoService` que proporciona funcionalidades para crear, buscar y eliminar empleados.

```csharp
/// <summary>
/// Servicio que proporciona la lógica de negocio para gestionar empleados.
/// </summary>
public class EmpleadoService
{
    private readonly ApplicationDbContext _context;

    public EmpleadoService(ApplicationDbContext context)
    {
        _context = context;
    }

    /// <summary>
    /// Crea un nuevo empleado en la base de datos.
    /// </summary>
    /// <param name="empleadoDto">Datos del empleado a crear.</param>
    /// <returns>El empleado creado con su ID asignado.</returns>
    public async Task<EmpleadoDTO> CrearAsync(EmpleadoDTO empleadoDto)
    {
        var empleado = new Empleado
        {
            Nombre = empleadoDto.Nombre,
            Departamento = empleadoDto.Departamento,
            FechaContratacion = empleadoDto.FechaContratacion
        };

        _context.Empleados.Add(empleado);
        await _context.SaveChangesAsync();

        empleadoDto.Id = empleado.Id;
        return empleadoDto;
    }

    /// <summary>
    /// Busca un empleado por su ID.
    /// </summary>
    /// <param name="id">ID del empleado a buscar.</param>
    /// <returns>El empleado encontrado o null si no existe.</returns>
    public async Task<EmpleadoDTO?> BuscarPorIdAsync(int id)
    {
        var empleado = await _context.Empleados.FindAsync(id);
        if (empleado == null) return null;

        return new EmpleadoDTO
        {
            Id = empleado.Id,
            Nombre = empleado.Nombre,
            Departamento = empleado.Departamento,
            FechaContratacion = empleado.FechaContratacion
        };
    }

    /// <summary>
    /// Elimina un empleado por su ID.
    /// </summary>
    /// <param name="id">ID del empleado a eliminar.</param>
    /// <returns>True si el empleado fue eliminado, false si no fue encontrado.</returns>
    public async Task<bool> EliminarAsync(int id)
    {
        var empleado = await _context.Empleados.FindAsync(id);
        if (empleado == null) return false;

        _context.Empleados.Remove(empleado);
        await _context.SaveChangesAsync();
        return true;
    }
}
```

## Uso de DTOs (Data Transfer Objects)

En el ejemplo anterior, el servicio utiliza `EmpleadoDTO` para transferir datos entre las capas. El uso de DTOs es una buena práctica porque permite desacoplar la estructura interna del modelo de datos de la API expuesta. Esto ayuda a evitar exponer datos sensibles o innecesarios y facilita el cumplimiento de principios de seguridad y mantenimiento.

**Ventajas del uso de DTOs**:

- **Encapsulamiento**: Evitan exponer detalles innecesarios del modelo de datos.
- **Validación**: Se pueden agregar reglas de validación específicas para los datos que se envían o reciben.
- **Compatibilidad**: Facilitan la evolución del sistema sin romper contratos públicos.

## Inyección de Dependencias en Servicios

Existen diferentes formas de inyectar un servicio en la aplicación. La inyección de dependencias permite que un servicio tenga acceso a otras clases necesarias (como el contexto de base de datos) sin acoplarse fuertemente a ellas.

### 1. Inyección de Dependencias sin Interfaz

La forma más sencilla de inyectar un servicio es directamente sin interfaz. En el ejemplo anterior, `EmpleadoService` se inyecta directamente en el controlador:

```csharp
public class EmpleadosController : ControllerBase
{
    private readonly EmpleadoService _empleadoService;

    public EmpleadosController(EmpleadoService empleadoService)
    {
        _empleadoService = empleadoService;
    }
}
```

Esta forma de inyección es útil cuando la implementación del servicio no tiene variaciones y no se espera sustituir `EmpleadoService` por otra implementación en el futuro.

### 2. Inyección de Dependencias con Interfaz

En aplicaciones más complejas, es una buena práctica definir una interfaz para los servicios. Esto permite cambiar la implementación del servicio sin afectar al código que lo consume, facilitando la realización de pruebas unitarias o el uso de mocks.

```csharp
/// <summary>
/// Interfaz para el servicio de empleados, que define las operaciones disponibles para gestionar empleados.
/// </summary>
public interface IEmpleadoService
{
        /// <summary>
    /// Crea un nuevo empleado en la base de datos.
    /// </summary>
    /// <param name="empleadoDto">Datos del empleado a crear.</param>
    /// <returns>El empleado creado con su ID asignado.</returns>
    Task<EmpleadoDTO> CrearAsync(EmpleadoDTO empleadoDto);
        /// <summary>
    /// Busca un empleado por su ID.
    /// </summary>
    /// <param name="id">ID del empleado a buscar.</param>
    /// <returns>El empleado encontrado o null si no existe.</returns>
    Task<EmpleadoDTO?> BuscarPorIdAsync(int id);
        /// <summary>
    /// Elimina un empleado por su ID.
    /// </summary>
    /// <param name="id">ID del empleado a eliminar.</param>
    /// <returns>True si el empleado fue eliminado, false si no fue encontrado.</returns>
    Task<bool> EliminarAsync(int id);
}

public class EmpleadoService : IEmpleadoService
{
    private readonly ApplicationDbContext _context;

    public EmpleadoService(ApplicationDbContext context)
    {
        _context = context;
    }

    // Implementación de los métodos del servicio...
}
```

Luego, en el controlador se inyecta la interfaz en lugar de la clase concreta:

```csharp
public class EmpleadosController : ControllerBase
{
    private readonly IEmpleadoService _empleadoService;

    public EmpleadosController(IEmpleadoService empleadoService)
    {
        _empleadoService = empleadoService;
    }
}
```

**Ventajas de usar Interfaces**:

- **Flexibilidad**: Facilita el cambio de implementación si se requiere, sin afectar a los controladores u otros servicios que lo consumen.
- **Pruebas Unitarias**: Facilita la creación de pruebas unitarias, ya que se pueden utilizar mocks de la interfaz.
- **Principios SOLID**: Cumple con el Principio de Inversión de Dependencia, mejorando la mantenibilidad del sistema.

## Tipos de Inyección de Dependencias

Los servicios se registran en el archivo `Program.cs` para que puedan ser inyectados en los controladores u otras clases de la aplicación. A continuación se muestra cómo registrar un servicio utilizando los distintos métodos de inyección de dependencias disponibles:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Registrar servicios en el contenedor de dependencias
builder.Services.AddScoped<IEmpleadoService, EmpleadoService>(); // AddScoped
builder.Services.AddTransient<OtroServicio>();                   // AddTransient
builder.Services.AddSingleton<ServicioUnico>();                  // AddSingleton

var app = builder.Build();

// Configurar el pipeline de la aplicación
app.MapControllers();
app.Run();
```

### 1. `AddScoped`

`AddScoped` registra un servicio con un ciclo de vida por solicitud. Cada vez que se recibe una solicitud HTTP, se crea una nueva instancia del servicio. Este enfoque es ideal para servicios que contienen lógica que no debería compartirse entre diferentes solicitudes concurrentes.

### 2. `AddTransient`

`AddTransient` registra un servicio con un ciclo de vida transitorio, lo que significa que se crea una nueva instancia cada vez que el servicio es solicitado. Este enfoque es adecuado para servicios ligeros y sin estado, donde una nueva instancia por cada uso es beneficiosa.

### 3. `AddSingleton`

`AddSingleton` registra un servicio con un ciclo de vida único durante toda la vida útil de la aplicación. Es decir, se crea una única instancia del servicio cuando se inyecta por primera vez y se comparte en toda la aplicación. Este tipo de inyección es útil para servicios que contienen lógica que se puede compartir sin restricciones entre solicitudes, como cachés en memoria.

## Buenas Prácticas al Usar Servicios

1. **Mantener los Métodos Simples**: Cada método debe realizar una única acción clara. Esto facilita el mantenimiento y las pruebas.
2. **Utilizar DTOs**: Usa DTOs para evitar exponer directamente los modelos de la base de datos y para controlar la información que se transfiere entre capas.
3. **Manejo de Excepciones**: Los servicios deben capturar las excepciones esperadas y lanzar excepciones específicas que sean manejadas adecuadamente en los controladores.
4. **Validación de Datos**: Realiza validaciones tanto a nivel de controlador (para validar el formato de la solicitud) como en el servicio (para validar reglas de negocio).
5. **Separación de Responsabilidades**: El servicio debe contener solo lógica de negocio. La lógica de validación del modelo debe hacerse en el controlador, y la lógica de acceso a datos debe ser responsabilidad de los repositorios.

## Resumen

- **Servicios**: Son la capa que encapsula la lógica de negocio y se comunican con la capa de acceso a datos.
- **DTOs**: Facilitan la transferencia de datos y ayudan a proteger la estructura interna del modelo.
- **Inyección de Dependencias**: Se puede hacer con o sin interfaz. Usar interfaces proporciona flexibilidad y facilita las pruebas.
- **Buenas Prácticas**: Mantener métodos simples, utilizar DTOs, manejar excepciones y seguir el principio de separación de responsabilidades.

Con estas prácticas y conceptos, puedes estructurar la capa de servicios de una API REST de manera efectiva, asegurando un código modular, limpio y fácil de mantener.
