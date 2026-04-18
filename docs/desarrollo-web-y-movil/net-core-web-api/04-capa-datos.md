---
sidebar_position: 4
sidebar_label: 1.2.4 La Capa de Datos en un Backend API REST
---

# La Capa de Datos en un Backend API REST

La capa de datos en un backend API REST es responsable de la interacción directa con la base de datos. Incluye las entidades que representan las tablas de la base de datos y el contexto de datos (DbContext), encargado de gestionar la conexión y las operaciones CRUD. Su diseño permite mantener un acceso a datos limpio y desacoplado de la lógica de negocio, promoviendo así una arquitectura más organizada y mantenible.

:::note Qué vs Cómo en esta capa
El **requerimiento** es que el sistema persista datos de forma confiable y recuperable. La **decisión técnica** que adoptamos aquí — usar Entity Framework Core, un `DbContext` y anotaciones de mapeo — es una forma válida de satisfacer ese requerimiento, no la única. Equipos con restricciones distintas podrían elegir Dapper, procedimientos almacenados, o un repositorio sobre ADO.NET sin cambiar el *Qué*.
:::

Para implementar esta capa de forma eficiente, es recomendable utilizar Entity Framework Core (EF Core), un framework de mapeo objeto-relacional (ORM) para .NET. EF Core permite trabajar con bases de datos relacionales mediante clases y objetos, eliminando la necesidad de escribir consultas SQL directamente. Esto facilita un diseño más natural y expresivo, mejora la mantenibilidad del código y reduce la complejidad asociada al acceso a datos.

---

## ¿Qué es la Capa de Datos?

La capa de datos es la encargada de definir cómo se estructuran y se almacenan los datos. Esto incluye la definición de las entidades, las relaciones entre ellas, y la configuración del contexto de base de datos (`DbContext`). Esta capa proporciona la infraestructura necesaria para realizar operaciones CRUD sobre los datos de manera eficiente y escalable.

### Mapeo de Entidades

El siguiente ejemplo muestra cómo crear una entidad llamada `Empleado` que representa la estructura de la tabla en la base de datos:

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

/// <summary>
/// Entidad que representa la tabla Empleados en la base de datos.
/// </summary>
[Table("EMPLEADOS", Schema = "RRHH")]
public class Empleado
{
    [Key]
    public int Id { get; set; }

    [Required]
    [MaxLength(100)]
    public string Nombre { get; set; }

    [Required]
    [MaxLength(50)]
    public string Departamento { get; set; }

    [Column(TypeName = "date")]
    public DateTime FechaContratacion { get; set; }
}
```

#### Explicación de los Atributos

- **`[Key]`**: Indica que la propiedad `Id` es la clave primaria de la tabla.
- **`[Required]`**: Marca un campo como obligatorio, lo cual asegura que no se permitan valores nulos en la columna correspondiente.
- **`[MaxLength(100)]`**: Limita la longitud máxima del campo `Nombre` a 100 caracteres.
- **`[Column(TypeName = "date")]`**: Configura el tipo de datos de la columna `FechaContratacion` para que sea de tipo `date` en la base de datos.
- **`[Table("EMPLEADOS", Schema = "RRHH")]`**: Define explícitamente el nombre de la tabla (`EMPLEADOS`) y el esquema (`RRHH`) en la base de datos. Esto es útil cuando el nombre de la clase no coincide con el nombre de la tabla o si la tabla está en un esquema específico.

:::info

[Conoce más sobre los atributos en la documentación oficial de Entity Framework Core](https://learn.microsoft.com/es-mx/ef/core/modeling/entity-properties?tabs=data-annotations%2Cwithout-nrt)

:::

### Mapeo de Entidad con Clave Compuesta

En muchos casos es común tener entidades que requieren múltiples llaves para asegurar la unicidad de los registros. El siguiente ejemplo muestra cómo crear una entidad llamada `EmpleadoContrato`, que tiene una clave primaria compuesta por varias propiedades:

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

/// <summary>
/// Entidad que representa los contratos de empleados en la base de datos.
/// </summary>
[Table("EMPLEADO_CONTRATOS", Schema = "RRHH")]
[PrimaryKey(nameof(IdEmpleado), nameof(FechaInicio), nameof(TipoContrato))]
public class EmpleadoContrato
{
    public int IdEmpleado { get; set; }
    public DateTime FechaInicio { get; set; }
    public string TipoContrato { get; set; }

    [Required]
    public DateTime FechaFin { get; set; }

    [Required]
    public decimal Salario { get; set; }
}
```

#### Caso Especial: Clave Compuesta con `[PrimaryKey]`

- **`[PrimaryKey(nameof(IdEmpleado), nameof(FechaInicio), nameof(TipoContrato))]`**: Define una clave primaria compuesta para la entidad `EmpleadoContrato`, que se compone de las propiedades `IdEmpleado`, `FechaInicio` y `TipoContrato`. Esto garantiza que cada combinación de `IdEmpleado`, `FechaInicio` y `TipoContrato` sea única en la base de datos.

Este enfoque es útil para representar entidades que dependen de múltiples atributos para definir su unicidad, como el historial de contratos de un empleado donde cada contrato tiene una fecha de inicio y un tipo específico.

## Configuración del Data Context

### Configuración de la Cadena de Conexión

La cadena de conexión se define en el archivo `appsettings.json`, lo cual permite configurar la conexión a la base de datos de forma centralizada. Es una buena práctica utilizar variables de entorno para la configuración en entornos de producción, lo que proporciona mayor seguridad al evitar exponer credenciales sensibles en archivos de configuración.

**Ejemplo de `appsettings.json`**:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MiBaseDeDatos;User Id=miUsuario;Password=miContraseña;"
  }
}
```

**Recomendación para Producción**: Para entornos de producción, se recomienda utilizar variables de entorno para definir la cadena de conexión, evitando así almacenar credenciales directamente en archivos de configuración.

### Registro en `Program.cs`

En el archivo `Program.cs`, se debe registrar el `DbContext` utilizando el método `AddDbContext` para que pueda ser inyectado en las clases que lo requieran:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Registrar el contexto de datos con la cadena de conexión
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

// Configurar el pipeline de la aplicación
app.MapControllers();
app.Run();
```

### Definición del `DbContext`

El `DbContext` es la clase principal para interactuar con la base de datos mediante Entity Framework. Se utiliza para gestionar las entidades y las consultas a la base de datos. La herencia de DbContext permite:

- Configurar la conexión a la base de datos.
- Definir y gestionar entidades y sus relaciones.
- Utilizar capacidades avanzadas como consultas LINQ, seguimiento de cambios y transacciones.

```csharp
using Microsoft.EntityFrameworkCore;

/// <summary>
/// Contexto de datos que gestiona la conexión con la base de datos y las entidades.
/// </summary>
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {
    }

    public DbSet<Empleado> Empleados { get; set; }

    public DbSet<EmpleadoResumenDTO> EmpleadosResumen { get; set; } // Mapeo de la vista
}
```

### Cómo Agregar un Mapeo al Data Context

El `DbSet` se utiliza para definir la representación de una entidad o una vista en la base de datos dentro del contexto de datos. En el ejemplo anterior, se utilizan `DbSet<Empleado>` para la tabla `Empleado` y `DbSet<EmpleadoResumenDTO>` para la vista `EmpleadoResumenDTO`. Esto le indica a Entity Framework cómo debe interactuar con estos datos en la base de datos.

## Mapeo de Vistas a DTOs con `Keyless`

Entity Framework permite mapear vistas de base de datos a clases, y esto se puede hacer usando el atributo `Keyless` cuando no hay una clave primaria definida en la vista. Esto es particularmente útil cuando se desea exponer datos agregados o resultados de consultas complejas como parte de la API.

```csharp
using Microsoft.EntityFrameworkCore;

/// <summary>
/// DTO que representa una vista de empleados con datos agregados.
/// </summary>
[Keyless]
public class EmpleadoResumenDTO
{
    public string Nombre { get; set; }
    public string Departamento { get; set; }
    public DateTime FechaContratacion { get; set; }
    public int ProyectosAsignados { get; set; }
}
```

Para mapear esta vista en el `DbContext`, se debe agregar una propiedad de tipo `DbSet` sin clave primaria:

```csharp
public DbSet<EmpleadoResumenDTO> EmpleadosResumen { get; set; }
```

### Explicación del Atributo `Keyless`

- **`[Keyless]`**: Se utiliza para indicar que la clase `EmpleadoResumenDTO` no tiene una clave primaria. Esto es necesario cuando se mapea una vista o un resultado de consulta que no tiene un identificador único, pero que aún necesita ser expuesto como parte del modelo de datos.

## Cómo Construir Consultas SQL

Al trabajar con bases de datos relacionales, existen dos formas principales de construir consultas: usando SQL directo o mediante Entity Framework Core (EF Core), que permite utilizar consultas LINQ integradas con el modelo de datos de tu aplicación.

### Opción 1: Consulta SQL Directa

Este enfoque utiliza comandos SQL tradicionales para interactuar con la base de datos. Por ejemplo, si quieres obtener todos los empleados del departamento de ventas, podrías escribir la siguiente consulta:

```sql SELECT * FROM Empleados WHERE Departamento = 'Ventas';```

De esta manera.

```csharp
var departamento = "Ventas";
var empleados = context.Empleados
    .FromSqlRaw("SELECT * FROM Empleados WHERE Departamento = {0}", departamento)
    .ToList();
```

En este ejemplo, {0} es un marcador de posición que se reemplaza con el valor seguro del parámetro departamento, previniendo ataques de inyección SQL.

:::info

Recuerda que es importante utilizar consultas parametrizadas. [Para más información](https://www.10x.gt/blog/impulsa-tu-codigo-19/importancia-de-las-consultas-parametrizadas-en-sql-6/)

:::

### Opción 2: Consulta con Entity Framework Core (EF Core)

Usando EF Core, puedes realizar la misma consulta con una expresión LINQ en C# de manera más sencilla:

```csharp
var empleados = context.Empleados
    .Where(e => e.Departamento == "Ventas")
    .ToList();
```

- En este caso: `context.Empleados` representa la tabla Empleados como una clase.
- `.Where(e => e.Departamento == "Ventas")` especifica el filtro de la consulta.
- `.ToList()` ejecuta la consulta y devuelve los resultados como una lista de objetos.

### Comparación de las Opciones

| Característica                | Consulta SQL Directa           | Consulta con EF Core             |
|-------------------------------|--------------------------------|----------------------------------|
| **Facilidad de uso**          | Requiere escribir SQL manualmente y de forma correcta. | Más intuitiva al usar clases y LINQ. |
| **Legibilidad**               | Depende del conocimiento de SQL. | Fácil de entender para desarrolladores C#. |
| **Mantenimiento**             | Cambios en la estructura afectan SQL. | Cambios se reflejan automáticamente. |
| **Flexibilidad**              | Muy alta.                      | Limitada para consultas muy complejas. |

## Librerías Populares para Bases de Datos

Dependiendo del proveedor de la base de datos que se esté utilizando, se pueden emplear diferentes librerías para conectarse y gestionar las operaciones de acceso a datos. A continuación se presentan las librerías más populares para algunos de los sistemas de gestión de bases de datos más comunes:

### Oracle

- **Oracle Entity Framework Core**: Para trabajar con Oracle mediante Entity Framework Core, se puede utilizar la librería `Oracle.EntityFrameworkCore` proporcionada por Oracle.

### MySQL

- **Pomelo.EntityFrameworkCore.MySql**: Es una librería popular y de código abierto para utilizar Entity Framework Core con MySQL.
- **MySql.Data.EntityFrameworkCore**: Proporcionada por Oracle, es otra opción para trabajar con MySQL en .NET.

### PostgreSQL

- **Npgsql.EntityFrameworkCore.PostgreSQL**: `Npgsql` es la librería más común para trabajar con PostgreSQL en .NET y soporta Entity Framework Core.

Estas librerías permiten que Entity Framework Core se comunique con diferentes bases de datos, asegurando una integración adecuada con el proveedor seleccionado.

## Resumen

- **Capa de Datos**: Responsable de la interacción directa con la base de datos, incluyendo la definición de entidades y la configuración del contexto de datos (`DbContext`).
- **Entity Framework Core (EF Core)**: Framework ORM para .NET que permite trabajar con bases de datos relacionales usando clases y objetos, eliminando la necesidad de escribir consultas SQL manuales.
- **Consultas SQL Directas vs EF Core**: Las consultas SQL directas ofrecen mayor flexibilidad, mientras que EF Core facilita el mantenimiento y mejora la legibilidad al usar expresiones LINQ.
- **Uso de `DbContext`**: Gestiona las entidades y sus mapeos, permitiendo la interacción con la base de datos. El `DbSet` se utiliza para definir la representación de una tabla o una vista dentro del contexto de datos.
- **Mapeo de Entidades y Vistas**: Incluye el uso de anotaciones como `Key`, `PrimaryKey`, `Keyless`, `Required`, y `MaxLength` para definir la estructura y características de las tablas y vistas, lo cual facilita la representación y manipulación de datos estructurados y no estructurados directamente desde la base de datos.

Con estos conceptos, puedes estructurar una capa de datos robusta que facilite la interacción con la base de datos de manera segura y eficiente, asegurando que los datos se gestionen adecuadamente a lo largo de las diferentes capas de la aplicación.

## Glosario

**Entity Framework Core** *(EF Core)* — ORM oficial de Microsoft para .NET que mapea clases a tablas y traduce LINQ a SQL.

**ORM** *(Object-Relational Mapper)* — herramienta que traduce entre objetos y registros de una base de datos relacional.

**`DbContext`** *(DbContext)* — clase que representa una sesión con la base de datos; gestiona conexión, seguimiento y operaciones.

**`DbSet`** *(DbSet)* — colección que representa una tabla o vista consultable dentro del `DbContext`.

**Clave compuesta** *(Composite key)* — clave primaria formada por más de una columna; se declara con `[PrimaryKey]`.

**`[Keyless]`** *(Keyless entity)* — atributo que permite mapear vistas o resultados sin clave primaria.

**LINQ** *(Language Integrated Query)* — sintaxis de consulta integrada en C# utilizada para traducir expresiones a SQL.

**Consulta parametrizada** *(Parameterized query)* — consulta cuyos valores se pasan como parámetros para evitar inyección SQL.

:::info Referencias primarias
- [Microsoft · .NET docs](https://learn.microsoft.com/en-us/dotnet/) — referencia del ecosistema .NET.
- [Entity Framework Core docs](https://learn.microsoft.com/en-us/ef/core/) — guía oficial de EF Core.
- [EF Core · Modeling](https://learn.microsoft.com/en-us/ef/core/modeling/) — modelado de entidades y mapeos.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** modelar la capa de datos de una API REST en .NET Core con Entity Framework Core aplicando buenas prácticas de mapeo y acceso.

**Entradas:**
- Modelo de dominio y relaciones entre entidades.
- Proveedor de base de datos (SQL Server, MySQL, PostgreSQL, Oracle).
- Cadena de conexión y política de configuración por entorno.
- Vistas o consultas agregadas que deban exponerse.

**Pasos:**
1. Crear entidades con atributos de mapeo (`[Key]`, `[Required]`, `[MaxLength]`, `[Column]`, `[Table]`).
2. Definir claves compuestas con `[PrimaryKey(...)]` cuando aplique.
3. Implementar el `DbContext` con los `DbSet` correspondientes y registrarlo en `Program.cs`.
4. Mapear vistas a DTOs con `[Keyless]` para consultas agregadas.
5. Escribir consultas LINQ preferentemente y parametrizar cualquier consulta SQL directa.
6. Configurar la cadena de conexión por variables de entorno en producción.
7. Seleccionar el provider adecuado (`Pomelo.EntityFrameworkCore.MySql`, `Npgsql`, `Oracle.EntityFrameworkCore`).

**Salidas:**
- Entidades y `DbContext` listos para operaciones CRUD.
- Estrategia de mapeo de vistas y DTOs de lectura.
- Configuración segura de conexiones por entorno.

**Errores comunes:**
- Concatenar parámetros en consultas SQL directas (riesgo de inyección).
- Exponer entidades `DbContext` como DTOs de la API.
- Almacenar la cadena de conexión con credenciales en `appsettings.json` de producción.
- Olvidar `[Keyless]` en vistas, causando errores de modelado.

**Referencias cruzadas:**
- [1.2.1 Arquitectura de Backend API Rest en .NET Core](./01-arquitectura-de-backend.md)
- [1.2.3 La Capa de Servicios en un Backend API REST](./03-capa-servicios.md)
- [1.2.5 Patrones de diseño en APIs REST](./05-patrones-de-diseno-en-apis-rest.md)
</div>
