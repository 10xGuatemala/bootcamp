<!-- Destino sugerido: .claude/skills/nueva-entidad-ef-core.skill.md -->

---
name: nueva-entidad-ef-core
description: Agrega una entidad nueva a un proyecto Entity Framework Core sincronizando Model, DbContext y scripts DDL/DML, con Data Annotations sobre Fluent API, nombres de columna en snake_case y convenciones consistentes entre C# y SQL.
---

# Skill: nueva-entidad-ef-core

Agregar una tabla parece trivial hasta que dos meses después alguien cambia un `MaxLength` en la clase Model y olvida el `VARCHAR(n)` en el script SQL. El bug se manifiesta en producción cuando un usuario escribe una descripción larga: el formulario acepta el texto, EF Core también, y MySQL lo trunca sin avisar. La fuente del bug es la desincronía silenciosa entre código y esquema. Este skill existe para que cada entidad nueva entre con las convenciones y controles que evitan ese tipo de error.

## Cuándo invocarme

Úsame cuando:
- Se agrega una tabla nueva al dominio y hay que crear `Model` + scripts + registro en `DbContext`.
- Se modifica una entidad existente (campo nuevo, relación, índice) y hay que mantener la sincronía entre código y esquema.
- Se audita una entidad existente para verificar que sus `[Column]`, su DDL y su DbContext coinciden.

**No me uses para:**
- Diseñar el modelo de datos — ese análisis lo hace un humano antes de invocarme.
- Escribir el endpoint que la expone — usa `nuevo-endpoint-rest-net`.
- Definir estrategias de migración entre bases existentes — eso requiere plan de rollback que no es alcance del skill.

## Entradas

1. **Nombre de la entidad** y módulo al que pertenece (ej. `ClienteModel` en `Modules/Ventas/`).
2. **Campos**: nombre, tipo, si es nullable, tamaño máximo para strings, precisión para decimales.
3. **Relaciones**: FKs a otras entidades, cardinalidad, comportamiento `ON DELETE`/`ON UPDATE`.
4. **Índices** necesarios — FKs, campos de búsqueda frecuente, combinaciones para `UNIQUE`.
5. **Proveedor de DB** del proyecto (MySQL, PostgreSQL, SQL Server). Cambia el tipo de columna en algunos casos: `JSON` vs `JSONB`, `DATETIME` vs `TIMESTAMP`, `UUID` vs `CHAR(36)`.
6. **Convención de zona horaria** del proyecto — si hay utilitario de fecha, el `DateTime` de la entidad debe alinearse.

## Regla clave: Data Annotations sobre Fluent API

El módulo [1.2.4 La Capa de Datos](../../../../docs/desarrollo-web-y-movil/net-core-web-api/04-capa-datos.md) muestra las dos opciones: atributos en la clase (Data Annotations) o configuración en `OnModelCreating` (Fluent API). La preferencia es **Data Annotations**, por tres razones concretas:

1. **La documentación vive junto al campo.** Cuando un desarrollador lee la clase, ve el tamaño, nullability y nombre de columna sin saltar a otro archivo.
2. **El diff es local.** Un cambio en un campo toca una línea en la clase; en Fluent API, toca un archivo compartido que también configura otras entidades.
3. **El agente lo procesa mejor.** Leer anotaciones es lectura estructural; inferirlas desde una cadena de llamadas fluidas en `OnModelCreating` requiere ejecutar mentalmente el builder.

- **Mal** (configuración que podía ir en la clase, duplicada en `OnModelCreating`):
  ```csharp
  // ClienteModel.cs — sin anotaciones
  public class ClienteModel
  {
      public Guid Id { get; set; }
      public string NombreComun { get; set; }
      public string? RazonSocial { get; set; }
  }

  // DbContext.OnModelCreating — configuración dispersa
  modelBuilder.Entity<ClienteModel>(e =>
  {
      e.ToTable("clientes");
      e.HasKey(c => c.Id);
      e.Property(c => c.NombreComun).HasColumnName("nombre_comun").HasMaxLength(150).IsRequired();
      e.Property(c => c.RazonSocial).HasColumnName("razon_social").HasMaxLength(250);
  });
  ```

- **Bien** (toda la configuración declarada en la clase):
  ```csharp
  [Table("clientes")]
  public class ClienteModel
  {
      [Key]
      [Column("id_cliente")]
      public Guid IdCliente { get; set; }

      [Required, MaxLength(150)]
      [Column("nombre_comun")]
      public string NombreComun { get; set; } = string.Empty;

      [MaxLength(250)]
      [Column("razon_social")]
      public string? RazonSocial { get; set; }

      [Column("credito_disponible", TypeName = "decimal(18,2)")]
      public decimal CreditoDisponible { get; set; }

      [Column("datos_extra", TypeName = "json")]
      public JsonDocument? DatosExtra { get; set; }

      [Required]
      [Column("fecha_creacion")]
      public DateTime FechaCreacion { get; set; }

      [Column("id_empresa")]
      public Guid IdEmpresa { get; set; }

      [ForeignKey(nameof(IdEmpresa))]
      public EmpresaModel Empresa { get; set; } = null!;
  }
  ```

**Valor para el agente:** cuando toda la configuración vive en la clase, el agente que agrega un campo nuevo edita un solo archivo y no tiene que recordar actualizar `OnModelCreating`.

### Cuándo sí usar Fluent API

No siempre Data Annotations alcanzan. Ve a `OnModelCreating` cuando:

- Hay **claves compuestas** que no se pueden expresar con `[PrimaryKey]` (aunque desde EF Core 7 sí se puede — usa la anotación primero).
- Hay **relaciones many-to-many con tabla intermedia explícita** y campos adicionales en la tabla de unión.
- Hay **índices compuestos, filtrados o con nombre específico** que no caben en `[Index]`.
- La configuración **depende del proveedor** (ej. `HasAnnotation("MySql:Charset", "utf8mb4")`).

```csharp
// En DbContext.OnModelCreating, solo cuando Data Annotations no alcanzan:
modelBuilder.Entity<ClienteModel>()
    .HasIndex(c => new { c.IdEmpresa, c.NombreComun })
    .HasDatabaseName("ix_clientes_empresa_nombre");
```

Un proyecto sano termina con Data Annotations cubriendo el 90% de la configuración, y el `OnModelCreating` usado solo para el resto.

## Convenciones de naming

Un proyecto coherente mantiene la misma convención en todos los lados donde se nombra la entidad. Las decisiones típicas:

| Elemento | Convención | Ejemplo |
|---|---|---|
| Clase Model | `PascalCase` con sufijo `Model` | `ClienteModel` |
| Propiedad C# | `PascalCase` | `NombreComun` |
| Tabla SQL | `snake_case` plural vía `[Table(...)]` | `clientes` |
| Columna SQL | `snake_case` vía `[Column(...)]` | `nombre_comun` |
| PK | `id_<entidad_propia>` | `id_cliente` en tabla `clientes` |
| FK | `id_<entidad_referida>` | `id_empresa` en tabla `clientes` |
| Índice | `ix_<tabla>_<campos>` | `ix_clientes_empresa_nombre` |
| UNIQUE | `uq_<tabla>_<campo>` | `uq_clientes_correo` |
| FK constraint | `fk_<tabla_origen>_<tabla_destino>` | `fk_clientes_empresa` |

**Valor para el agente:** con la tabla anterior, un agente puede inferir el nombre correcto de cualquier artefacto SQL sin consultar. Si ve `nombre_comun` en el DDL, sabe que la propiedad en C# es `NombreComun`.

## Pasos

### Paso 1: Escribir la clase Model con Data Annotations completas

Cada campo lleva el conjunto mínimo de atributos para describir su contrato completo:

- `[Column("...")]` siempre — fija el nombre de la columna y evita que EF lo infiera.
- `[Required]` para campos no nulos.
- `[MaxLength(n)]` para strings — define el `VARCHAR(n)` del DDL.
- `[Column(TypeName = "...")]` para tipos específicos — `decimal(18,2)`, `json`, `date`, `time`.
- `[Key]` en la PK. Si la clave es compuesta, `[PrimaryKey(...)]` a nivel de clase (EF Core 7+).
- `[ForeignKey(nameof(...))]` en la propiedad de navegación, vinculando con la propiedad FK.

**Valor para el agente:** cada atributo es una restricción declarada. El agente que genera el DDL lee los atributos y traduce uno a uno sin interpretar.

### Paso 2: Registrar el DbSet en el DbContext correcto

Si el proyecto tiene múltiples `DbContext` (ej. uno de auth, uno de negocio), registra la entidad en el que corresponde. Un error común es agregar la entidad al `DbContext` equivocado y recibir un `InvalidOperationException` en runtime.

```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options) { }

    public DbSet<ClienteModel> Clientes { get; set; }
    public DbSet<EmpresaModel> Empresas { get; set; }
    // ...
}
```

- Mal: agregar `DbSet<ClienteModel>` a `AuthDbContext` porque fue el primer archivo que se abrió.
- Bien: identificar el `DbContext` del módulo de negocio al que pertenece la entidad y registrar ahí.

### Paso 3: Escribir el script DDL que coincida exactamente con el Model

El DDL debe ser fiel reflejo de los atributos del Model. Cada divergencia entre Model y DDL es un bug latente:

```sql
-- Database/DDL/015-clientes.sql
CREATE TABLE clientes (
    id_cliente CHAR(36) NOT NULL,
    id_empresa CHAR(36) NOT NULL,
    nombre_comun VARCHAR(150) NOT NULL,
    razon_social VARCHAR(250) NULL,
    credito_disponible DECIMAL(18,2) NOT NULL DEFAULT 0,
    datos_extra JSON NULL,
    fecha_creacion DATETIME NOT NULL,
    CONSTRAINT pk_clientes PRIMARY KEY (id_cliente),
    CONSTRAINT fk_clientes_empresa
        FOREIGN KEY (id_empresa) REFERENCES empresas(id_empresa)
        ON DELETE RESTRICT ON UPDATE CASCADE,
    INDEX ix_clientes_empresa_nombre (id_empresa, nombre_comun),
    UNIQUE KEY uq_clientes_correo (correo)
);
```

Reglas del DDL:
- **`NOT NULL` refleja `[Required]`** en el Model. Si uno cambia, ambos cambian.
- **`VARCHAR(n)` refleja `[MaxLength(n)]`**. No uses `TEXT` o `VARCHAR(255)` "por si acaso".
- **`DECIMAL(p,s)` refleja `[Column(TypeName = "decimal(p,s)")]`**. La precisión y escala deben coincidir.
- **FKs con `ON DELETE`/`ON UPDATE` explícitos**. Nunca dejes el comportamiento al default del proveedor, cambia entre motores.
- **Índices en todas las FKs**. La mayoría de los motores no crea índice automático para la FK, y las consultas por pertenencia se degradan sin él.

**Valor para el agente:** un DDL completo y consistente con el Model es auditable automáticamente. Un diff entre el archivo `.cs` y el `.sql` revela desincronías al instante.

### Paso 4: Agregar script DML para datos semilla si aplica

Algunas entidades son catálogos con valores iniciales conocidos (estados, tipos, roles, países). Esos valores viven en un script DML versionado:

```sql
-- Database/DML/015-estados-iniciales.sql
INSERT INTO estados (id_estado, codigo, nombre) VALUES
    (UUID(), 'ACT', 'Activo'),
    (UUID(), 'INA', 'Inactivo'),
    (UUID(), 'PEN', 'Pendiente');
```

- Mal: hacer los inserts desde la aplicación al arrancar — acopla datos de referencia al arranque, difícil de auditar.
- Bien: DML versionado, ejecutado como parte del despliegue, con historial claro.

### Paso 5: Decidir si merece tabla dedicada o cabe en un catálogo genérico

Muchos proyectos tienen un **catálogo genérico** (tabla `catalogos` + `catalogo_items`) para referencias simples. Antes de crear una tabla nueva, pregunta:

| Preguntas | Decisión |
|---|---|
| ¿Los campos se reducen a `codigo`, `nombre`, `orden`? | Catálogo genérico |
| ¿Hay campos específicos de negocio (ej. `porcentaje_impuesto`, `dias_validez`)? | Tabla dedicada |
| ¿Otra entidad lo referencia por FK directa? | Tabla dedicada (FK a catálogo genérico es frágil) |
| ¿Los valores cambian rara vez y son conocidos al arranque? | Catálogo genérico |
| ¿Hay lógica de negocio que depende del código (ej. "solo el rol ADMIN puede X")? | Tabla dedicada, con código estable como constante |

- Mal: crear `tipos_documento`, `tipos_pago`, `tipos_cliente`, `tipos_contrato` — cuatro tablas con la misma forma.
- Bien: un catálogo genérico con filas agrupadas por `tipo_catalogo`, y tabla dedicada solo para los que tienen campos o lógica propia.

**Valor para el agente:** esta decisión es verificable con las preguntas de la tabla. No requiere intuición.

### Paso 6: Verificar sincronía Model ↔ DDL ↔ DbContext

Antes de marcar la entidad como lista, recorre la checklist de sincronía:

```bash
# Campos en el Model
grep -E "\[Column|\[Required|\[MaxLength" Models/ClienteModel.cs

# Columnas en el DDL
grep -E "^\s+(id_|nombre_|fecha_)" Database/DDL/015-clientes.sql

# Registro en DbContext
grep "DbSet<ClienteModel>" Data/ApplicationDbContext.cs
```

Cada atributo del Model debe tener su contraparte en el DDL con el mismo tipo, tamaño y nullability. El `DbSet<>` debe existir una sola vez.

## Prompt de auditoría

Para revisar una entidad existente contra estas reglas, copia este prompt:

```
Audita la entidad <NombreModel> contra el skill `nueva-entidad-ef-core`. Revisa:

1. ¿Cada `[MaxLength(n)]` del Model coincide con `VARCHAR(n)` en el DDL?
2. ¿Cada `[Required]` del Model coincide con `NOT NULL` en el DDL?
3. ¿Los nombres de columnas y tabla siguen `snake_case` y coinciden exactamente?
4. ¿Las FKs tienen `ON DELETE` y `ON UPDATE` explícitos?
5. ¿Existen índices en todas las FKs y en campos de búsqueda frecuente?
6. ¿La configuración vive en Data Annotations o está dispersa entre la clase y `OnModelCreating`?
7. ¿La entidad está registrada en el `DbContext` correcto?

Salida: tabla con columnas "Regla", "Estado", "Diferencia", "Corrección sugerida".
```

## Antes de entregar, verifico

- [ ] El Model usa Data Annotations; `OnModelCreating` solo para lo que no se puede expresar con atributos.
- [ ] Nombres de columna y tabla en `snake_case`, coincidentes entre Model y DDL.
- [ ] Tipos de columna en el DDL coinciden con los atributos (`decimal(18,2)`, `varchar(150)`, `json`).
- [ ] `NOT NULL` en el DDL y `[Required]` en el Model están sincronizados.
- [ ] Todas las FKs tienen `ON DELETE` y `ON UPDATE` explícitos.
- [ ] Hay índice en cada FK y en los campos frecuentemente consultados.
- [ ] Si el proyecto usa múltiples `DbContext`, la entidad se registra en el correcto.
- [ ] Las propiedades de navegación están declaradas (`= null!` o nullable explícito).
- [ ] Si la entidad tiene `DateTime`, hay convención clara de zona horaria.
- [ ] Si es un catálogo, se decidió conscientemente entre tabla dedicada y catálogo genérico.
- [ ] Existe DML de datos semilla si corresponde.

## Salidas

- Clase Model en `Modules/<Modulo>/Models/<Entidad>Model.cs` con Data Annotations completas.
- `DbSet<>` registrado en el `DbContext` correspondiente.
- Script DDL en `Database/DDL/<NNN>-<tabla>.sql` sincronizado con el Model.
- Script DML en `Database/DML/` si la entidad requiere datos semilla.
- Decisión documentada de tabla dedicada vs catálogo genérico cuando aplique.

## Errores comunes

- **Desincronía silenciosa Model ↔ DDL.** Alguien cambia `[MaxLength(200)]` en C#, EF acepta strings de 200, MySQL los trunca a 150 sin error visible. El bug se manifiesta semanas después.
- **Configuración dispersa en `OnModelCreating`.** La clase queda limpia, pero la realidad vive en otro archivo. Próximo cambio olvida actualizar uno de los dos.
- **Crear tabla nueva para un catálogo trivial.** Tres meses después el proyecto tiene quince tablas con la misma forma (`codigo`, `nombre`, `orden`) y ningún beneficio.
- **FK sin `ON DELETE` explícito.** El comportamiento depende del proveedor. En MySQL InnoDB por default es `RESTRICT`, en PostgreSQL es `NO ACTION` — parece igual hasta que no lo es.
- **FK sin índice.** La tabla crece, las consultas por pertenencia ("todos los clientes de esta empresa") bajan de 5 ms a 500 ms sin causa evidente.
- **`DateTime` sin convención de zona horaria.** Un endpoint guarda UTC, otro guarda local, el reporte los mezcla. Aparece el bug "la fecha cambió un día".
- **`[Column(TypeName = "decimal")]` sin precisión.** EF Core usa el default del proveedor (`decimal(18,2)` en SQL Server, `decimal(65,30)` en MySQL). Al migrar de motor cambia silenciosamente la escala.
- **Registrar `DbSet<>` dos veces** en distintos `DbContext` — EF lanza `InvalidOperationException` al inicializar.

## Glosario

**Data Annotations** *(Data Annotations)* — atributos en la clase Model (`[Table]`, `[Column]`, `[Required]`, `[MaxLength]`) que declaran el mapeo a la base de datos. Referencia en [EF Core · Entity properties](https://learn.microsoft.com/en-us/ef/core/modeling/entity-properties?tabs=data-annotations).

**Fluent API** *(Fluent API)* — configuración del modelo dentro de `OnModelCreating` usando el `ModelBuilder`. Alternativa a Data Annotations para casos que los atributos no cubren.

**DDL** *(Data Definition Language)* — subconjunto de SQL para definir estructura (`CREATE`, `ALTER`, `DROP`). Versionado en el repo.

**DML** *(Data Manipulation Language)* — subconjunto de SQL para manipular datos (`INSERT`, `UPDATE`, `DELETE`). Usado para datos semilla de catálogos.

**Clave compuesta** *(Composite primary key)* — PK formada por más de una columna; se declara con `[PrimaryKey(...)]` a nivel de clase (EF Core 7+).

**Catálogo genérico** *(Generic lookup table)* — tabla única (`catalogos` + `catalogo_items`) que aloja múltiples catálogos simples para evitar proliferación de tablas con la misma forma.

**Propiedad de navegación** *(Navigation property)* — propiedad en el Model que representa la relación con otra entidad (`public EmpresaModel Empresa { get; set; }`) y permite consultas con `.Include(...)`.

:::info Referencias primarias
- [EF Core · Entity properties](https://learn.microsoft.com/en-us/ef/core/modeling/entity-properties) — atributos y configuración de propiedades.
- [EF Core · Relationships](https://learn.microsoft.com/en-us/ef/core/modeling/relationships) — modelado de FKs y cardinalidad.
- [EF Core · Indexes](https://learn.microsoft.com/en-us/ef/core/modeling/indexes) — atributos `[Index]` y configuración avanzada.
- [Martin Fowler · Database Refactoring](https://martinfowler.com/books/refactoringDatabases.html) — referencia clásica sobre evolución de esquemas.
- [1.2.4 La Capa de Datos](../../../../docs/desarrollo-web-y-movil/net-core-web-api/04-capa-datos.md) — módulo del bootcamp del que este skill es la versión operativa.
:::
