# Spec: [NombrePantalla]

## Propósito
Qué hace el usuario en esta pantalla. Una oración.

## Ruta y permisos
- Ruta: /facturas
- Requiere: rol "Administrador" o "Contador"
- Si el usuario no tiene permisos: redirigir a /sin-permiso

## Estructura
Tree de componentes con tokens de spacing:

- Header (padding: spacing.4)
  - Título (font.size.2xl)
  - Botón primario "Nueva factura"
- Filtros (margin-top: spacing.6)
  - SearchBar
  - DateRangePicker
  - Select "Estado"
- Tabla (margin-top: spacing.4)
  - Columnas: Número, Cliente, Fecha, Monto, Estado, Acciones

## Datos
- Fuente: GET /api/facturas?estado=&desde=&hasta=
- Paginación: 25 por página, server-side
- Orden default: fecha descendente

## Estados
- Cargando: skeleton de tabla (5 filas)
- Vacío: ilustración + "No hay facturas" + botón "Crear primera"
- Error de red: toast + botón "Reintentar"
- Sin resultados tras filtro: "No hay facturas que coincidan con los filtros"

## Responsive
- Desktop (≥1024): filtros horizontales, tabla completa
- Tablet (640-1023): filtros colapsables, tabla con scroll horizontal
- Mobile (<640): filtros en modal, tabla reemplazada por cards

## Accesibilidad
- Foco inicial: en el campo de búsqueda
- Tab order: Buscar → Fecha → Estado → Nueva factura → Tabla
- Contraste AA en toda la pantalla
- Tabla navegable por teclado (flechas entre celdas)

## Analytics
- Evento: "facturas_view" al cargar
- Evento: "filtro_aplicado" con payload {filtro, valor}

## Qué NO hace esta pantalla
- No permite editar facturas (eso vive en /facturas/:id)
- No muestra totales agregados (eso vive en /dashboard)
