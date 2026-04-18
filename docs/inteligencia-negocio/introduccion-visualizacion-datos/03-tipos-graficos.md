---
sidebar_position: 3
sidebar_label: 2.2.3 Tipos de Gráficos
---

# Tipos de Gráficos

La selección del tipo de gráfico correcto es esencial para comunicar de manera efectiva el mensaje de los datos. Cada tipo de gráfico tiene un propósito particular y puede ser más adecuado dependiendo del contexto y del tipo de información que desees presentar. A continuación, veremos algunos de los tipos de gráficos más comunes y sus características clave

## Gráficos de barras

El propósito de este tipo de gráficos es comparar cantidades entre diferentes categorías. Son ideales cuando se desea mostrar diferencias claras entre grupos.

- Ejemplo: Supongamos que tenemos datos de ventas de diferentes productos para el último trimestre:
  - •Producto A: 400 unidades
  - •Producto B: 320 unidades
  - •Producto C: 280 unidades
  - •Producto D: 600 unidades

    ![ejemplo gráfica de barra](./img/graf-01.png)

    Los gráficos de barras permiten ver de un vistazo qué productos se vendieron mejor que otros. En este caso, podemos ver que el Producto D fue el más vendido.

## Gráficos de líneas

El propósito de este tipo de gráficos es mostrar tendencias y cambios a lo largo del tiempo. Son útiles para visualizar cómo evolucionan los datos en periodos continuos.

- Ejemplo: Supongamos que tenemos datos de ventas mensuales para el año 2022:
  - •Enero: $120,000
  - •Febrero: $110,000
  - •Marzo: $130,000
  - •Abril: $125,000
  - •Mayo: $140,000

    ![ejemplo gráfica de linea](./img/graf-02.png)

    En este gráfico, podemos observar cómo las ventas fluctúan cada mes. Este tipo de gráfico es ideal para identificar patrones, como aumentos o caídas estacionales.

## Gráficos circulares (pie charts)

El propósito de este tipo de gráficos es mostrar la proporción de partes de un todo. Son útiles cuando se desea mostrar cómo se distribuye un conjunto total en diferentes componentes.

- Ejemplo: Supongamos que el presupuesto anual de una empresa se distribuye de la siguiente manera:
  - •Marketing: 40%
  - •Desarrollo: 30%
  - •Administración: 20%
  - •Otros: 10%

    ![ejemplo gráfica de pie](./img/graf-03.png)

    Los gráficos circulares permiten visualizar qué proporción del presupuesto se asigna a cada área. En este ejemplo, podemos ver que la mayor parte del presupuesto se destina a Marketing.

## Gráficos de área

El propósito de este tipo de gráficos es mostrar la magnitud y tendencia de una o más series a lo largo del tiempo, similar a los gráficos de líneas, pero enfatizando el volumen.

- Ejemplo: Supongamos que tenemos datos de ingresos mensuales para dos productos durante el primer trimestre:
  - •Producto A: [2000,2200,2100] [2000,2200,2100]
  - •Producto B: [1800,1600,1700] [1800,1600,1700]

    ![ejemplo gráfica de area](./img/graf-04.png)

    Los gráficos de área permiten comparar cómo evolucionan los ingresos de los productos a lo largo del tiempo, mostrando claramente las diferencias en magnitud y tendencia.

## Histogramas

El propósito de este tipo de gráficos es mostrar la distribución de una variable numérica, permitiendo visualizar cómo los datos se agrupan en diferentes rangos.

- Ejemplo: Supongamos que tenemos datos de edades en una empresa:
  - Edades: [25,30,35,40,45,50,55,60] [25,30,35,40,45,50,55,60]

  ![ejemplo histograma](./img/graf-05.png)

    El propósito de este tipo de gráficos es mostrar la distribución de una variable numérica, permitiendo visualizar cómo los datos se agrupan en diferentes rangos.

## Glosario

**Gráfico de barras** *(Bar chart)* — representa comparaciones entre categorías discretas.

**Gráfico de líneas** *(Line chart)* — muestra la evolución de una serie a lo largo del tiempo.

**Gráfico circular** *(Pie chart)* — representa proporciones de partes respecto a un todo; eficaz con pocas categorías.

**Gráfico de área** *(Area chart)* — combina tendencia y magnitud en el tiempo, enfatizando volumen.

**Histograma** *(Histogram)* — muestra la distribución de una variable numérica agrupada en rangos.

**Serie temporal** *(Time series)* — conjunto de valores ordenados cronológicamente para observar patrones y tendencias.

:::info Referencias primarias
- [Edward Tufte · The Visual Display of Quantitative Information](https://www.edwardtufte.com/tufte/books_vdqi) — referencia clásica.
- [Storytelling with Data](https://www.storytellingwithdata.com/) — selección de gráficos según mensaje.
- [Datawrapper Academy · Which chart](https://academy.datawrapper.de/category/105-chart-type) — guía práctica.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** seleccionar el tipo de gráfico adecuado según el mensaje y la naturaleza de los datos.

**Entradas:**
- Tipo de dato (categórico, numérico, temporal, distribución).
- Mensaje a comunicar (comparación, tendencia, proporción, distribución).
- Volumen de datos y cantidad de categorías.
- Audiencia y canal donde se mostrará el gráfico.

**Pasos:**
1. Clasificar los datos por tipo y objetivo analítico.
2. Elegir gráfico de barras para comparar cantidades entre categorías.
3. Elegir gráfico de líneas para tendencias en el tiempo.
4. Elegir gráfico circular para proporciones de un todo con pocas categorías.
5. Elegir gráfico de área para magnitud y tendencia combinadas.
6. Elegir histograma para distribuciones de variables numéricas.
7. Validar la elección con un par de usuarios antes de publicar.

**Salidas:**
- Gráfico alineado al mensaje y al tipo de datos.
- Justificación del tipo de gráfico seleccionado.
- Criterios para casos similares futuros.

**Errores comunes:**
- Usar gráficos circulares con muchas categorías.
- Representar tendencias temporales con gráficos de barras aisladas.
- Confundir histograma con gráfico de barras categóricas.
- Saturar un solo gráfico con múltiples mensajes.

**Referencias cruzadas:**
- [2.2.2 Elementos Clave para una Presentación de Datos](./02-elementos-clave.md)
- [2.2.4 Informes y Tableros (Dashboard)](./04-informes-tableros.md)
- [2.2.1 Introducción a la Visualización de Datos](./01-introduccion.md)
</div>
