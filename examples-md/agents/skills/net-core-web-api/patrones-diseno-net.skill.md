<!-- Destino sugerido: .claude/skills/patrones-diseno-net.skill.md -->

---
name: patrones-diseno-net
description: Decide cuándo introducir Strategy, Factory, Builder, Template Method o Máquina de Estados en un servicio .NET que creció. Aplica el patrón correcto en la carpeta correcta con los sufijos correctos (`XxxStrategy`, `XxxFactory`, `XxxBuilder`, `XxxServiceBase`, `XxxMaquinaEstados`), evita la introducción prematura, y documenta la decisión.
---

# Skill: patrones-diseno-net

Un patrón de diseño introducido el día uno — cuando hay un solo filtro, una sola creación, un solo flujo — es overhead disfrazado de buena práctica: cuatro archivos para lo que cabe en un método de diez líneas. Un patrón introducido el día cuatrocientos — cuando hay seis métodos `ApplyFilters` con el mismo `if` repetido, dos `XxxFactory` artesanales y una validación de estado rota por `if`-guards — es deuda técnica acumulada que ya costó bugs en producción. Entre medio está la zona donde los patrones pagan: **cuando el dolor es concreto, medible y repetido**. Este skill existe para decidir ese momento con criterio, no con intuición; y cuando la decisión es positiva, aplicarlo donde la arquitectura del proyecto lo espera (`Commons/Application/...`, `Modules/{Dominio}/StateMachine/`).

## Cuándo invocarme

Úsame cuando:
- Un servicio ya existente creció y tiene código duplicado observable (ej. cuatro servicios con el mismo bloque de `ApplyFilters`).
- Una entidad tiene más de dos estados y los `if`-guards de transición se multiplicaron (candidato a StateMachine).
- Un método de creación toma más de cinco parámetros o tiene validaciones/cálculos paso a paso (candidato a Builder).
- Se escribe el mismo CRUD en tres servicios distintos con mínimas diferencias (candidato a Template Method).
- Se revisa un PR que introduce un patrón y hay que verificar que es el correcto y está en la ubicación correcta.

**No me uses para:**
- Arrancar un servicio nuevo "ya con patrones listos" — YAGNI manda; introducir patrones sin dolor documentado infla la base.
- Decidir estructura de carpetas o namespaces (para eso: `estructura-proyecto-net`).
- Implementar el patrón paso a paso con el código completo; mi alcance es **decisión + ubicación + criterios**, el código concreto lo escribe quien conoce el dominio.
- Introducir patrones no cubiertos por este skill (Observer, Mediator, Repository). Esos requieren análisis aparte — no aplicarlos por analogía.

## Entradas

1. **Problema observado**: descripción concreta del dolor (ej. "ClienteService, ProductoService y ProveedorService tienen el mismo `ApplyFilters` con 15 líneas cada uno").
2. **Servicios/entidades afectados**: lista explícita, no "varios".
3. **Estructura del proyecto**: debe seguir el contrato de [`estructura-proyecto-net`](./estructura-proyecto-net.skill.md) — `Commons/Application/`, `Modules/{Dominio}/`, etc.
4. **Métricas de duplicación si existen**: líneas duplicadas, cantidad de servicios afectados, frecuencia con que se agrega un caso nuevo.

Sin problema observado, no aplico ningún patrón. "Por si acaso crecemos" no es entrada válida.

## Reglas obligatorias

- **Regla del tres.** No introducir un patrón hasta que el caso se repite tres veces. Dos es coincidencia; tres es patrón.
- **Patrón sigue a dolor, no al revés.** El disparador es código duplicado, `if`-ladder inmantenible o creación ambigua — no "porque es buena práctica".
- **Ubicación correcta según estructura del proyecto.** Los patrones transversales viven en `Commons/Application/{Builders,Strategies}/`; los específicos de un módulo viven en `Modules/{Dominio}/{SubDominio}/`. Nunca en la raíz.
- **Sufijos obligatorios.** `XxxStrategy`, `XxxFactory`, `XxxBuilder`, `XxxServiceBase` (Template Method), `XxxMaquinaEstados`. El sufijo es el contrato con lectores y agentes.
- **Un patrón no reemplaza tests.** Introducir un patrón es un refactor; los tests existentes deben seguir pasando y, si no existen, agregarlos antes del refactor.
- **Documentar la decisión en `CLAUDE.md` del repo.** Cada patrón introducido lista: qué problema resuelve, dónde vive, servicios que lo usan, cuándo agregar más casos.
- **No inventar patrones.** Si el problema no encaja con los cinco que cubre este skill (Strategy, Factory, Builder, Template Method, StateMachine), proponer análisis aparte con el arquitecto — no forzar un patrón que no aplica.

## Los cinco patrones — cuándo cada uno

### Strategy Pattern — filtros, políticas intercambiables

**Usar cuando:**
- Más de dos servicios tienen lógica de filtrado similar o duplicada (ej. `ApplyFilters` en `ClienteService`, `ProductoService`, `ProveedorService`).
- La composición de filtros varía por endpoint y necesita ser flexible.
- Hay que integrar filtrado con paginación de forma consistente.

**No usar cuando:**
- Un solo servicio con un solo filtro simple.
- Filtros con 1-2 condiciones que se leen mejor inline.

**Dónde vive:**
- Genérico reutilizable: `Commons/Application/Strategies/FilterStrategies/`.
- Específico de un módulo: `Modules/{Dominio}/Strategies/` (raro — usualmente todo lo reutilizable).
- Contexto de ejecución: `Commons/Application/Strategies/FilterContext.cs`.

**Sufijo:** `XxxFilterStrategy` (para filtros) o `XxxStrategy` (general).

**Ejemplo de decisión real:**

- Mal: *"Voy a crear una strategy para mi primer filtro de Clientes."* No hay duplicación aún — es dos archivos para lo que es una línea.
- Bien: *"ClienteService, ProductoService, ProveedorService y CotizacionService tienen cada uno un `ApplyFilters` con `TerminoBusqueda`, `Estado` y `RangoFechas`. Tres casos repetidos → introducir `FilterContext` + strategies en `Commons/Application/Strategies/`."*

**Valor para el agente:** la strategy materializa una decisión que antes estaba copiada. Cada filtro nuevo es un archivo nuevo, no una modificación en cuatro servicios.

### Factory Pattern — creación con validación multipaso

**Usar cuando:**
- La creación de un objeto requiere más de tres validaciones o llamadas a servicios.
- Varios servicios crean el mismo tipo con lógica casi idéntica (candidato clarísimo).
- Hay subtipos del mismo objeto (ej. `ServicioCloud`, `ServicioDesarrollo`) que comparten 70% de la creación.

**No usar cuando:**
- Creación con `new` directo o un constructor claro.
- Objetos sin validaciones ni dependencias externas.

**Dónde vive:**
- Transversal: `Commons/Services/{Xxx}Factory.cs`.
- Específico de módulo: `Modules/{Dominio}/{SubDominio}/Services/{Xxx}Factory.cs`.

**Sufijo:** `XxxFactory`.

**Ejemplo de decisión real:**

- Mal: *"Factory para crear un `ClienteRequest` DTO."* Un DTO no necesita factory — su constructor trivial o `with`-expression alcanza.
- Bien: *"`AsignarServicioCloudAClienteAsync`, `AsignarServicioDesarrolloAClienteAsync`, `AsignarServicioCiberseguridadAClienteAsync` repiten las mismas cinco validaciones. Extraer `ServicioNegocioFactory.CrearServicioContratadoAsync` en `Commons/Services/`."*

**Valor para el agente:** la factory centraliza validaciones que antes se copiaban. Agregar un tipo nuevo de servicio es implementar la extensión en la factory, no duplicar quinientas líneas.

### Builder Pattern — construcción paso a paso con validación

**Usar cuando:**
- Un objeto requiere más de cinco parámetros para construirse correctamente.
- La construcción tiene cálculos intermedios (ej. subtotales, impuestos, totales).
- Las validaciones ocurren durante la construcción, no solo al final.
- El objeto tiene múltiples "formas válidas" según el escenario (ej. cotización con o sin viáticos).

**No usar cuando:**
- Objetos con 2-3 propiedades que se construyen con un constructor obvio.
- Objetos sin validación cruzada entre propiedades.

**Dónde vive:**
- Transversal (entidades de negocio reutilizadas): `Commons/Application/Builders/{Xxx}Builder.cs`.
- Específico de un escenario de módulo: `Modules/{Dominio}/{SubDominio}/Services/ScenarioBuilders/{Xxx}Builder.cs`.

**Sufijo:** `XxxBuilder`.

**Ejemplo de decisión real:**

- Mal: *"Builder para `EstadoDto`."* Un enum wrapper no justifica builder.
- Bien: *"`CotizacionModel` tiene 18 propiedades con interdependencias (totales calculados, cuentas bancarias opcionales, notas por destinatario). El constructor de 18 argumentos es ilegible; tres servicios construyen cotizaciones con lógica duplicada. Introducir `CotizacionBuilder` en `Commons/Application/Builders/`."*

API fluida:

```csharp
var cotizacion = new CotizacionBuilder()
    .ConEmpresa(idEmpresa)
    .ConCliente(idCliente)
    .ConFechas(DateOnly.FromDateTime(DateTime.Now), validezDias: 30)
    .ConMontos(subtotal: 1000, descuentoTotal: 50, impuestos: 150)
    .AgregarDetalle(...)
    .Build(); // Valida y retorna el Model
```

**Valor para el agente:** el builder convierte construcción ambigua en una secuencia que se lee como prosa. `.Build()` es el único punto donde la validación fuerte vive — no hay que revisar cada llamada al constructor.

### Template Method Pattern — CRUD común con hooks

**Usar cuando:**
- Tres o más servicios implementan el mismo CRUD (típicamente catálogos de solo lectura: `TipoServicioService`, `TipoIdentificacionService`, `RegimenFiscalService`).
- El flujo es idéntico pero con pequeñas diferencias (filtro por ID distinto, relaciones a incluir distintas).
- Se agrega un servicio de catálogo nuevo cada mes y duplicar es caro.

**No usar cuando:**
- Servicios con flujos completamente distintos.
- Servicios que no comparten el ciclo base (crear, leer, actualizar, eliminar).
- Cuando la variación entre implementaciones es tan alta que los hooks terminan sobrescribiendo casi todo.

**Dónde vive:**
- Base transversal: `Commons/Services/{Xxx}ServiceBase.cs`.
- Implementaciones concretas: en su módulo respectivo, heredando del base.

**Sufijo:** `XxxServiceBase` (para la clase abstracta); los concretos mantienen su nombre habitual (`TipoServicioService`).

**Ejemplo de decisión real:**

- Mal: *"TemplateMethod para `ClienteService`."* Un servicio con lógica rica no encaja — hay demasiada variación entre métodos.
- Bien: *"Ya tenemos `TipoServicioService`, `TipoIdentificacionService`, `RegimenFiscalService`, `BancoService` — cuatro servicios de catálogo de solo lectura con el mismo patrón (listar, obtener por id, incluir navegación opcional). Introducir `CatalogoServiceBase<TModel, TResponse, TId>` en `Commons/Services/`."*

```csharp
public class TipoServicioService : CatalogoServiceBase<TipoServicioModel, TipoServicioResponse, short>
{
    protected override DbSet<TipoServicioModel> DbSet => _dbContext.TiposServicios;
    protected override string EntityNotFoundMessage => ModulesMsg.TipoServicioNoEncontrado;
    protected override Expression<Func<TipoServicioModel, bool>> BuildIdFilter(short id)
        => t => t.IdTipoServicio == id;
    protected override IQueryable<TipoServicioModel> IncludeNavigations(IQueryable<TipoServicioModel> query)
        => query; // sin includes
}
```

**Valor para el agente:** agregar un catálogo nuevo pasa de 120 líneas de copia-pega a 15 líneas con tres overrides. Las correcciones al flujo base se propagan automáticamente.

### Máquina de Estados — transiciones controladas

**Usar cuando:**
- Una entidad tiene más de dos estados y **no todas las transiciones son válidas**.
- Los `if`-guards en servicios para validar transiciones se repiten en múltiples puntos.
- Las transiciones inválidas deben rechazarse con mensajes contextuales (no excepciones genéricas).
- Hay lógica distinta según el estado actual (auditoría, timestamps, efectos secundarios).

**No usar cuando:**
- Un flag booleano (`activo` / `inactivo`) — dos estados no justifican motor.
- Estados con transiciones libres (cualquier estado puede ir a cualquier otro).

**Dónde vive:**
- Motor genérico: `Commons/StateMachine/MaquinaEstados.cs`. **No modificar** — recibe transiciones, estado actual y disparador; retorna siguiente estado.
- Máquina específica de entidad: `Modules/{Dominio}/StateMachine/{Entidad}MaquinaEstados.cs`.

**Sufijo:** `XxxMaquinaEstados` (para la máquina específica), `DisparadorXxx` (para el enum de disparadores).

**Ejemplo de decisión real:**

- Mal: *"Máquina de estados para `Cliente.Estado` (Activo/Inactivo)."* Dos estados, transición libre — un flag alcanza.
- Bien: *"`BoletaModel.Estado` tiene cuatro estados (Borrador, Emitida, Aceptada, Anulada) y solo transiciones específicas son válidas: `Borrador→Emitida`, `Emitida→Aceptada`, `Emitida→Anulada`. Hoy tenemos tres `if` repetidos en `BoletaService`. Introducir `BoletaMaquinaEstados` en `Modules/RRHH/StateMachine/`."*

Patrón genérico:

```csharp
// Commons/StateMachine/MaquinaEstados.cs (no modificar)
MaquinaEstados<TEstado, TDisparador>.ObtenerSiguienteEstado(
    transiciones,      // IReadOnlyDictionary<(TEstado, TDisparador), TEstado>
    estadoActual,
    disparador,
    mensajeError);     // contextual, vía ModulesMsg
```

```csharp
// Modules/RRHH/StateMachine/BoletaMaquinaEstados.cs
public static BoletaEstado ObtenerSiguienteEstado(BoletaEstado actual, DisparadorBoleta disparador)
    => MaquinaEstados<BoletaEstado, DisparadorBoleta>.ObtenerSiguienteEstado(
        Transiciones, actual, disparador, MensajeInvalido);
```

Reglas adicionales:

- Los `if`/`switch` en servicios se reemplazan por una sola llamada a `ObtenerSiguienteEstado`.
- Efectos secundarios (timestamps, auditoría) van en el servicio **después** de obtener el nuevo estado.
- Mensajes de error contextual viven en `{Area}/Configurations/ModulesMsg.cs`, nunca hardcodeados en la máquina.
- El motor genérico no se modifica por lógica de dominio — eso va en la máquina específica.

**Valor para el agente:** la máquina de estados reemplaza `if`-ladders inmantenibles por una tabla declarativa de transiciones válidas. Agregar un estado nuevo es una fila en el diccionario, no un refactor en cinco servicios.

## Matriz de decisión — qué patrón para qué dolor

| Dolor observado | Patrón | Ubicación |
|---|---|---|
| Código duplicado de `ApplyFilters` en 3+ servicios | Strategy | `Commons/Application/Strategies/FilterStrategies/` |
| Creación con validaciones multipaso repetida en 3+ servicios | Factory | `Commons/Services/` o `Modules/{Dominio}/Services/` |
| Constructor con 5+ parámetros y cálculos intermedios | Builder | `Commons/Application/Builders/` o `Modules/{Dominio}/Services/ScenarioBuilders/` |
| CRUD de catálogo duplicado en 3+ servicios | Template Method | `Commons/Services/{Xxx}ServiceBase.cs` |
| Entidad con 3+ estados y `if`-guards en servicios | StateMachine | `Modules/{Dominio}/StateMachine/` |
| Acoplamiento entre módulos (A llama a B directamente) | **Fuera de alcance** — analizar Event-Driven / Mediator con arquitecto |
| Repetir queries de acceso a datos entre servicios | **Fuera de alcance** — analizar Repository con arquitecto |

## Pasos para introducir un patrón

### Paso 1: Confirmar la regla del tres

Antes de tocar código, contar **ocurrencias reales** del problema:

- ¿Cuántos archivos tienen el bloque duplicado? Listarlos con `grep` o `Find Usages`.
- ¿Cuántas veces se agregó un caso nuevo en los últimos seis meses?
- ¿El dolor va a crecer con features ya planificadas?

Si el conteo es **2 o menos**, no introducir el patrón. Documentar el dolor en un ticket y esperar al tercer caso. La introducción prematura infla la base sin pagar.

- Mal: introducir Strategy para filtros cuando solo dos servicios los tienen y son levemente distintos.
- Bien: esperar al tercer servicio con filtros; cuando llega, introducir el patrón y migrar los tres juntos.

**Valor para el agente:** la regla del tres es el filtro contra patrones por ego. Si no hay tres casos, el patrón es bienvenido en el backlog, no en `main`.

### Paso 2: Decidir si el patrón es transversal o de módulo

- **Transversal** (dos o más módulos lo usan o podrían usarlo): vive en `Commons/`.
- **Específico de un módulo** (solo un módulo lo necesita, pero dentro del módulo se repite): vive en `Modules/{Dominio}/`.

Regla práctica: si el patrón referencia tipos de un solo módulo (ej. `CotizacionModel`, `CotizacionDetalleModel`), probablemente vive dentro de ese módulo. Si referencia tipos genéricos (`TEntity`, `TFilter`), vive en `Commons/`.

- Mal: poner `CotizacionBuilder` en `Modules/Finanzas/Cotizador/` cuando varios módulos de negocio usarán cotizaciones.
- Bien: `CotizacionBuilder` en `Commons/Application/Builders/` (transversal); `CotizadorCloudScenarioBuilder` en `Modules/Finanzas/Cotizador/Services/ScenarioBuilders/` (específico de un escenario de módulo).

### Paso 3: Aplicar el sufijo correcto y la ubicación correcta

Verificar contra la tabla de ubicaciones de este skill y contra `estructura-proyecto-net`. Un patrón en la carpeta equivocada es peor que no tenerlo — confunde lectores que esperan la convención.

- Mal: `Commons/ClienteFilterStrategy.cs` (falta sub-carpeta).
- Bien: `Commons/Application/Strategies/FilterStrategies/ClienteFilterStrategy.cs`.

### Paso 4: Migrar el código existente al patrón en un solo PR

La introducción del patrón debe mover **todos los casos actuales** al patrón. Dejar uno sin migrar crea "el viejo y el nuevo" conviviendo — la mayor parte del costo de duplicación sigue ahí.

- Mal: introducir `FilterContext` y migrar solo `ClienteService`. `ProductoService` y `ProveedorService` se quedan con la versión vieja → ahora hay tres formas en vez de dos.
- Bien: un PR que introduce el patrón y migra los tres servicios. Tests pasan para los tres.

### Paso 5: Documentar en `CLAUDE.md` del repo

Al introducir el patrón, agregar sección en el `CLAUDE.md` del proyecto:

```markdown
### Strategy Pattern - Filtrado de consultas

**Cuándo usar:** múltiples servicios con lógica de filtrado similar o duplicada.
**Dónde vive:** `Commons/Application/Strategies/FilterStrategies/`.
**Servicios que lo usan:** ClienteService, ProductoService, ProveedorService.
**Para agregar un filtro nuevo:** crear clase en `FilterStrategies/` implementando `IFilterStrategy<TEntity, TFilter>`; registrarla en el `FilterContext` del servicio consumidor.
```

**Valor para el agente:** el `CLAUDE.md` permite que agentes futuros detecten el patrón sin leer el código. Cuando llega un caso nuevo, el agente va directo al patrón existente en vez de reinventar.

### Paso 6: Agregar tests del patrón

Cada strategy, factory, builder, máquina de estados debe tener tests unitarios aislados. El beneficio del patrón es testabilidad — aprovecharlo:

- Strategy: test por cada `Apply()` con input representativo.
- Factory: test por cada camino de creación (feliz + fallido).
- Builder: test que verifica `Build()` valida correctamente (antes y después de llamar a métodos obligatorios).
- Template Method: test en las clases concretas; el base se prueba vía las concretas.
- StateMachine: test matriz de transiciones válidas + matriz de transiciones inválidas (cada una debe lanzar con mensaje esperado).

## Antes de entregar, verifico

- [ ] La regla del tres está verificada — el patrón responde a 3+ ocurrencias concretas.
- [ ] El patrón está en la carpeta correcta según `estructura-proyecto-net` (`Commons/Application/...` o `Modules/{Dominio}/...`).
- [ ] El sufijo es el correcto (`Strategy`, `Factory`, `Builder`, `ServiceBase`, `MaquinaEstados`).
- [ ] Si es transversal, no referencia tipos de un solo módulo.
- [ ] Todos los casos existentes fueron migrados al patrón en el mismo PR.
- [ ] Hay tests unitarios que cubren el patrón aislado.
- [ ] `CLAUDE.md` del repo describe: cuándo usar, dónde vive, quién lo usa, cómo extender.
- [ ] No introduje un patrón no cubierto por este skill (Repository, Mediator, Observer) — esos requieren análisis aparte.

## Errores comunes

- **Introducir patrones el día 1** "para estar preparados". Infla la base sin pagar — patrón sigue a dolor, no al revés.
- **Ignorar la regla del tres.** Dos casos es coincidencia; generalizar con dos casos lleva a abstracciones incorrectas que hay que rediseñar cuando llega el tercero.
- **Ubicación incorrecta** (patrón en la raíz, o en `Modules/` cuando es transversal). Rompe la búsqueda predecible; lectores nuevos tardan el doble.
- **Sufijo inconsistente** (`ClienteFiltros` en vez de `ClienteFilterStrategy`). `grep "class.*Strategy"` deja de encontrar todos los casos.
- **Migración parcial.** Introducir el patrón y migrar solo un servicio. Ahora hay tres formas: la vieja, la nueva a medias y la nueva completa.
- **Máquina de estados donde alcanza un flag.** Overhead sin beneficio; dos estados con transición libre no justifican motor.
- **Builder para objetos triviales** (DTOs con tres propiedades). El constructor se lee mejor.
- **Factory que solo reemplaza `new`.** Si la creación no tiene validaciones ni ramas, la factory no agrega valor — solo indirección.
- **Strategy para un solo filtro complejo** que no se repite. Un método privado alcanza.
- **Saltar la documentación en `CLAUDE.md`.** El próximo agente llega, no conoce el patrón, implementa la nueva feature duplicando el flujo viejo. El patrón existe pero no se usa.
- **Modificar el motor genérico de StateMachine por lógica de dominio.** La máquina específica es el único punto donde vive el dominio; el motor queda estable.
- **Introducir patrones sin tests.** El patrón es un refactor; sin tests que cubran el comportamiento pre-refactor, se introduce regresión silenciosa.

## Glosario

**Regla del tres** *(Rule of three)* — heurística que dice: refactorizar hacia una abstracción reutilizable solo cuando el caso se repite tres veces. Dos es coincidencia; tres es patrón.

**Strategy** *(Strategy pattern)* — familia de algoritmos intercambiables encapsulados detrás de una interfaz común; cada algoritmo es un objeto seleccionable en runtime. En una API modular, suele aplicar para filtros de queries o reglas seleccionables por contexto.

**Factory** *(Factory pattern)* — encapsulación de la creación de objetos en un método o clase dedicada que centraliza validaciones, pasos y variantes. Reduce duplicación en los puntos que crean el mismo tipo.

**Builder** *(Builder pattern)* — construcción paso a paso de un objeto complejo con interfaz fluida; `Build()` valida y retorna el resultado final. Usado para objetos con muchos parámetros y cálculos intermedios.

**Template Method** *(Template method pattern)* — clase base abstracta que define el esqueleto de un algoritmo y delega pasos específicos a subclases mediante métodos virtuales (hooks). Útil para CRUD común de catálogos cuando varias entidades comparten el mismo flujo.

**Máquina de estados** *(State machine)* — modelo que define estados válidos de una entidad y las transiciones permitidas entre ellos, rechazando transiciones inválidas con error contextual. En una API modular puede implementarse como un motor genérico + máquinas específicas por entidad.

**Disparador** *(Trigger)* — en una máquina de estados, la acción del dominio que provoca una transición (ej. `DisparadorBoleta.Aceptar`). Forma parte de la clave del diccionario de transiciones junto al estado actual.

**Transición** *(Transition)* — paso de un estado a otro provocado por un disparador. Las transiciones válidas se declaran en un diccionario inmutable.

:::info Referencias primarias
- [Refactoring Guru — Design Patterns](https://refactoring.guru/design-patterns) — referencia pedagógica de los 23 patrones GoF con ejemplos en C#.
- [Microsoft — Design Patterns in C#](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-principles) — principios arquitectónicos que enmarcan cuándo aplicar patrones.
- [Martin Fowler — Is Design Dead?](https://martinfowler.com/articles/designDead.html) — ensayo canónico sobre cuándo introducir abstracciones (refactoring en lugar de diseño upfront).
- [1.2.5 Patrones de diseño en APIs REST](../../../../docs/desarrollo-web-y-movil/net-core-web-api/05-patrones-de-diseno-en-apis-rest.md) — módulo del bootcamp que cubre los cinco patrones que este skill materializa.
- [Skill `estructura-proyecto-net`](./estructura-proyecto-net.skill.md) — define las carpetas donde este skill coloca los patrones.
- [Skill `nuevo-endpoint-rest-net`](./nuevo-endpoint-rest-net.skill.md) — base desde la cual un endpoint puede crecer al punto de necesitar un patrón.
:::
