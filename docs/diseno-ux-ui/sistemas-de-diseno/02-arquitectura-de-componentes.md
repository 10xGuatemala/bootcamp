---
sidebar_position: 2
sidebar_label: 3.2.2 Arquitectura de componentes
---

# Arquitectura de componentes

:::tip Archivos copiables
La plantilla de brief y las implementaciones de `Button` en React y Blazor viven como archivos reales en [`examples/`](https://github.com/10xGuatemala/bootcamp/tree/main/docs/diseno-ux-ui/sistemas-de-diseno/examples).
:::

Un sistema de diseño sólido no se mide por cuántos componentes tiene, sino por **cuántas pantallas puede construir sin inventar uno nuevo**. Llegar ahí requiere pensar en componentes como piezas compuestas: átomos que se combinan en moléculas, moléculas que forman organismos, organismos que se ensamblan en plantillas y pantallas.

Este módulo adopta el modelo de **Atomic Design** de Brad Frost como vocabulario compartido, lo aterriza en decisiones prácticas (API, estados, límites) y entrega una plantilla de brief para especificar cualquier componente antes de implementarlo — sea en React, Vue, o un proyecto .NET con Blazor/Razor Components.

## Atomic Design en 60 segundos

| Nivel | Qué es | Ejemplos |
|-------|--------|----------|
| **Átomo** | Elemento más pequeño con propósito propio | `Button`, `Input`, `Label`, `Icon` |
| **Molécula** | Grupo de átomos que forma una unidad funcional | `SearchBar` (Input + Button), `FormField` (Label + Input + HelperText) |
| **Organismo** | Conjunto de moléculas y átomos que forma una sección de UI | `Header` (Logo + Nav + SearchBar + UserMenu), `DataTable` |
| **Plantilla** | Layout de página sin contenido real | Plantilla de dashboard, plantilla de detalle |
| **Página** | Plantilla con datos reales | `/dashboard` con datos de Juan, `/productos/123` |

El valor no está en clasificar religiosamente cada componente, sino en **forzar la pregunta**: "¿este nuevo componente es un átomo, o ya existe uno que debería componerse?". Equipos que se saltan la pregunta terminan con tres `Button` distintos que nadie sabe cuándo usar.

## Reglas de composición

Cuatro reglas que mantienen el árbol sano:

1. **Un átomo no depende de otro átomo.** `Button` no importa `Input`. Si lo necesita, uno de los dos no es átomo.
2. **Una molécula existe solo si se reutiliza.** `FormField` es molécula si lo usan 3+ pantallas. Si solo aparece en una, es código inline de esa pantalla.
3. **Un organismo no conoce su contexto.** `DataTable` no sabe que vive en `/facturas`. Recibe datos por props, no importa el store global.
4. **Las plantillas no contienen lógica de negocio.** Saben cómo es el layout, no de dónde viene la data.

:::tip Señal de alerta: el átomo "dios"
Si tu `Button` tiene 15 props (variant, size, loading, icon, iconPosition, tooltip, badge, fullWidth, rounded, elevated, pulsing...), no es un átomo: es tres componentes disfrazados. Divide en `Button`, `IconButton` y `LinkButton` antes de que el mantenimiento se vuelva imposible.
:::

## API de un componente — qué define tu contrato

Un componente reutilizable expone un contrato. Todo lo que **no está en el contrato** es comportamiento privado que puedes cambiar sin romper consumidores. El contrato tiene cinco partes:

1. **Props** — entradas tipadas, con default cuando hay un valor obvio.
2. **Eventos/callbacks** — salidas: `onClick`, `onChange`, `onSubmit`.
3. **Slots** — zonas de contenido que el consumidor controla (`children`, `header`, `footer`).
4. **Estados observables** — qué muestra el componente en cada condición (default, hover, focus, loading, error, disabled).
5. **Efectos secundarios** — qué hace el componente al mundo: navegar, abrir modales, emitir analytics.

Un componente con contrato claro puede refactorizarse sin avisar. Un componente sin contrato rompe a todos sus consumidores cada vez que toca.

## Plantilla de brief de componente

Usa esta plantilla antes de implementar. Si el brief no se puede llenar, el componente no está listo para código. Funciona igual para humanos, diseñadores y agentes.

```markdown
# Brief: [NombreComponente]

## Propósito
Qué problema resuelve en una oración. Si requiere dos oraciones, probablemente son dos componentes.

## Nivel atómico
Átomo | Molécula | Organismo. Justifica en una línea.

## API — propiedades
| Prop | Tipo | Default | Descripción |
|------|------|---------|-------------|
| variant | `"primary" \| "secondary" \| "ghost"` | `"primary"` | Jerarquía visual |
| size | `"sm" \| "md" \| "lg"` | `"md"` | Área táctil y tipografía |
| loading | `boolean` | `false` | Deshabilita y muestra spinner |
| disabled | `boolean` | `false` | No interactivo, contraste reducido |

## Eventos
| Evento | Payload | Cuándo se dispara |
|--------|---------|-------------------|
| onClick | `MouseEvent` | Usuario pulsa el botón y no está disabled/loading |

## Slots
- `children`: etiqueta del botón. Acepta texto o icono + texto.

## Estados visuales
- **Default** — reposo, listo para interacción.
- **Hover** — cambio de fondo o borde; no de layout.
- **Focus** — anillo visible (`color.brand.primary`, 2px offset).
- **Active** — feedback táctil breve.
- **Disabled** — contraste reducido a AA mínimo, cursor not-allowed.
- **Loading** — deshabilita interacción, reemplaza label por spinner inline.

## Accesibilidad
- Elemento semántico: `<button>` (no `<div onClick>`).
- Role implícito correcto; no forzar `role`.
- Texto accionable ("Guardar cambios", no "OK").
- Si el label es solo icono: `aria-label` obligatorio.
- Navegable por teclado: activar con Enter y Space.

## Ejemplo de uso
\`\`\`tsx
<Button variant="primary" size="md" loading={saving} onClick={handleSave}>
  Guardar cambios
</Button>
\`\`\`

## Qué NO hace
- No navega (para eso, `<Link>`).
- No abre modales sin confirmación en acciones destructivas.
- No maneja estado de formulario; el padre lo controla.

## Pruebas mínimas
- Render por defecto con label.
- Callback `onClick` se dispara al pulsar.
- No dispara `onClick` si `disabled` o `loading`.
- Activable por teclado (Enter y Space).
- Atributo `aria-busy` presente durante loading.
```

## Del brief al código — multiplataforma

El mismo brief se materializa en stacks distintos sin reescribirse. Lo único que cambia es la sintaxis de la plataforma; el contrato es idéntico.

### React + TypeScript

```tsx
type Variant = "primary" | "secondary" | "ghost";
type Size = "sm" | "md" | "lg";

interface ButtonProps {
  variant?: Variant;
  size?: Size;
  loading?: boolean;
  disabled?: boolean;
  children: React.ReactNode;
  onClick?: (e: React.MouseEvent<HTMLButtonElement>) => void;
}

export function Button({
  variant = "primary",
  size = "md",
  loading = false,
  disabled = false,
  children,
  onClick,
}: ButtonProps) {
  return (
    <button
      className={`btn btn--${variant} btn--${size}`}
      disabled={disabled || loading}
      aria-busy={loading}
      onClick={onClick}
    >
      {loading ? <Spinner size={size} /> : children}
    </button>
  );
}
```

### Blazor (C#)

```razor
@* Button.razor *@
<button
    class="btn btn--@Variant btn--@Size"
    disabled="@(Disabled || Loading)"
    aria-busy="@Loading"
    @onclick="HandleClick">
    @if (Loading)
    {
        <Spinner Size="@Size" />
    }
    else
    {
        @ChildContent
    }
</button>

@code {
    [Parameter] public string Variant { get; set; } = "primary";
    [Parameter] public string Size { get; set; } = "md";
    [Parameter] public bool Loading { get; set; }
    [Parameter] public bool Disabled { get; set; }
    [Parameter] public RenderFragment? ChildContent { get; set; }
    [Parameter] public EventCallback<MouseEventArgs> OnClick { get; set; }

    private async Task HandleClick(MouseEventArgs e)
    {
        if (Disabled || Loading) return;
        await OnClick.InvokeAsync(e);
    }
}
```

:::note Mismo contrato, dos implementaciones
Ambas versiones tienen las mismas 6 props, los mismos estados visuales, la misma accesibilidad. Si migraras una app de React a Blazor, los consumidores del botón no cambian conceptualmente — solo reemplazan la sintaxis. Ese es el valor real del brief.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** definir y mantener una arquitectura de componentes coherente donde cada pieza tiene contrato claro, nivel atómico justificado y límites explícitos.

**Entradas:**
- Inventario actual de componentes (si existe).
- Pantallas objetivo que se construirán con el sistema.
- Stack técnico (React, Vue, Blazor, etc.).

**Pasos:**
1. Clasificar los componentes actuales por nivel atómico; identificar los que son "molécula disfrazada de átomo".
2. Escribir un brief para cada componente reutilizado (prioridad: los 10 más usados).
3. Extraer reglas de composición específicas a `DESIGN.md`.
4. Detectar átomos "dios" (>8 props) y dividir.
5. Establecer una puerta de revisión: ningún componente nuevo entra al sistema sin brief aprobado.

**Salidas:**
- Briefs versionados para los componentes núcleo.
- Inventario clasificado por nivel atómico.
- Reducción medible de duplicación (ej. pasar de 3 `Button` a uno).

**Errores comunes:**
- Prematura atomización: convertir todo texto en un átomo `Text` cuando basta con una clase CSS.
- Átomos dios: `Button` con 15 props en lugar de tres componentes dedicados.
- Componentes sin "Qué NO hace": crecen por acumulación silenciosa.
- Organismos que conocen su ruta o el store: destruye la reutilización.

**Referencias cruzadas:**
- [3.2.1 Design tokens y estándares](./01-design-tokens-y-estandares.md)
- [3.2.3 Handoff técnico](./03-handoff-tecnico.md)

</div>

---

## Glosario

**Atomic Design** *(atomic design)* — metodología propuesta por Brad Frost que clasifica la UI en átomos, moléculas, organismos, plantillas y páginas.

**Átomo** *(atom)* — componente más pequeño con propósito propio (botón, input, icono). No depende de otros componentes del sistema.

**Molécula** *(molecule)* — combinación de átomos que forma una unidad funcional reutilizable (barra de búsqueda, campo de formulario).

**Organismo** *(organism)* — sección completa de UI compuesta por moléculas y átomos (header, tabla de datos).

**Contrato de componente** *(component contract)* — conjunto de props, eventos, slots y estados observables que define cómo los consumidores interactúan con el componente.

**Slot** *(slot)* — zona de contenido que el componente padre controla, permitiendo inyectar hijos arbitrarios (`children` en React, `RenderFragment` en Blazor).

:::info Referencias primarias
- [Atomic Design — Brad Frost (libro completo, gratis)](https://atomicdesign.bradfrost.com/)
- [React — Thinking in React](https://react.dev/learn/thinking-in-react)
- [Blazor — Razor Components](https://learn.microsoft.com/aspnet/core/blazor/components/)
- [Nielsen Norman Group — Design Systems Glossary](https://www.nngroup.com/articles/design-systems-101/)
:::

---

<AuthorCredit />
