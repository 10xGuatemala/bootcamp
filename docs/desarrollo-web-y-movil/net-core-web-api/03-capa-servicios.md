---
sidebar_position: 3
sidebar_label: 1.2.3 La Capa de Servicios en un Backend API REST
---

# La Capa de Servicios en un Backend API REST

Los servicios en un backend API REST encapsulan la lógica de negocio y sirven como la capa intermedia entre los controladores y la capa de acceso a datos. Esta separación de responsabilidades promueve un diseño más limpio y modular, facilitando el mantenimiento y la escalabilidad del sistema.

---

## ¿Qué es un Servicio?

Un servicio en una API REST se encarga de implementar la lógica de negocio de la aplicación. Los controladores gestionan las solicitudes HTTP y delegan las operaciones de negocio a los servicios, que a su vez interactúan con la capa de acceso a datos. Esta arquitectura ayuda a mantener el código más limpio y facilita la reutilización de la lógica de negocio.

## Ejemplo de Servicio: `EmpleadoService`

El siguiente ejemplo muestra cómo crear un servicio llamado `EmpleadoService` que proporciona funcionalidades para crear, buscar y eliminar empleados.

:::note Trazabilidad Qué → Cómo
El requerimiento funcional detrás de este servicio es: *"el sistema debe permitir crear, consultar y eliminar expedientes de empleados, persistiendo los datos de forma confiable"*. Ese es el **Qué**.

El uso de `ApplicationDbContext`, la inyección por constructor, el mapeo `EmpleadoDTO → Empleado` y la llamada a `SaveChangesAsync` son el **Cómo** — decisiones de diseño técnico (Entity Framework Core + inyección de dependencias) que podrían reemplazarse por Dapper, un repositorio, o un ORM distinto sin cambiar el requerimiento.
:::

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

### Inyección de Dependencias sin Interfaz

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

### Inyección de Dependencias con Interfaz

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

### `AddScoped`

`AddScoped` registra un servicio con un ciclo de vida por solicitud. Cada vez que se recibe una solicitud HTTP, se crea una nueva instancia del servicio. Este enfoque es ideal para servicios que contienen lógica que no debería compartirse entre diferentes solicitudes concurrentes.

### `AddTransient`

`AddTransient` registra un servicio con un ciclo de vida transitorio, lo que significa que se crea una nueva instancia cada vez que el servicio es solicitado. Este enfoque es adecuado para servicios ligeros y sin estado, donde una nueva instancia por cada uso es beneficiosa.

### `AddSingleton`

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

## Glosario

**Servicio de aplicación** *(Application service)* — clase que implementa la lógica de negocio y coordina el acceso a datos.

**DTO** *(Data Transfer Object)* — objeto que transporta datos entre capas sin exponer la estructura interna del modelo.

**Inyección de dependencias** *(Dependency injection)* — técnica para proveer colaboradores por constructor en lugar de instanciarlos dentro de la clase.

**`AddScoped`** *(Scoped lifetime)* — ciclo de vida que crea una instancia por solicitud HTTP; adecuado para servicios con `DbContext`.

**`AddTransient`** *(Transient lifetime)* — ciclo de vida que crea una instancia nueva en cada resolución; ideal para servicios sin estado.

**`AddSingleton`** *(Singleton lifetime)* — instancia única compartida durante toda la vida de la aplicación.

**Principio de inversión de dependencias** *(Dependency Inversion Principle)* — principio SOLID que favorece depender de abstracciones en lugar de implementaciones concretas.

:::info Referencias primarias
- [Microsoft · .NET docs](https://learn.microsoft.com/en-us/dotnet/) — referencia del ecosistema .NET.
- [ASP.NET Core · Dependency injection](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection) — guía oficial de DI.
- [Entity Framework Core docs](https://learn.microsoft.com/en-us/ef/core/) — referencia de EF Core.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** encapsular la lógica de negocio en la capa de servicios de una API REST en .NET Core con buenas prácticas de diseño e inyección de dependencias.

**Entradas:**
- Requerimientos funcionales y reglas de negocio del dominio.
- DTOs definidos para transferencia entre capas.
- Contexto de datos (`DbContext`) del proyecto.
- Política de ciclo de vida de los servicios (Scoped, Transient, Singleton).

**Pasos:**
1. Crear una clase de servicio con responsabilidad única por dominio (p. ej. `EmpleadoService`).
2. Exponer métodos claros de creación, consulta, actualización y eliminación.
3. Utilizar DTOs para desacoplar la estructura interna del contrato público.
4. Inyectar el `DbContext` y otras dependencias por constructor.
5. Definir interfaz si se requiere sustituir la implementación o facilitar pruebas.
6. Registrar el servicio en `Program.cs` con el ciclo de vida adecuado.
7. Manejar excepciones y validar reglas de negocio dentro del servicio.

**Salidas:**
- Servicios con responsabilidad única y probables mediante mocks.
- DTOs estables para comunicación entre capas.
- Configuración de DI alineada al ciclo de vida esperado.

**Errores comunes:**
- Exponer entidades de dominio directamente al controlador.
- Mezclar validación HTTP con reglas de negocio en el servicio.
- Registrar servicios como Singleton cuando dependen de `DbContext`.
- Agregar interfaces innecesarias sin necesidad de sustitución.

**Referencias cruzadas:**
- [1.2.1 Arquitectura de Backend API Rest en .NET Core](./01-arquitectura-de-backend.md)
- [1.2.2 La Capa de Controlador en un Backend API REST](./02-capa-controlador.md)
- [1.2.4 La Capa de Datos en un Backend API REST](./04-capa-datos.md)
</div>
