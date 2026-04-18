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
```tsx
<Button variant="primary" size="md" loading={saving} onClick={handleSave}>
  Guardar cambios
</Button>
```

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
