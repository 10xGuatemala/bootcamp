# Aplicación Práctica

En este módulo exploraremos cómo aplicar de forma práctica los componentes centrales de SonarQube: los **Quality Profiles** y los **Quality Gates**. Estos dos elementos trabajan en conjunto para garantizar que el código cumpla con los estándares definidos por la organización.

- Los **Quality Profiles** contienen el conjunto de reglas de análisis estático que se utilizan para evaluar la calidad del código. Son personalizables por lenguaje y por proyecto.
- Los **Quality Gates** definen las condiciones mínimas que el código debe cumplir (como ausencia de bugs críticos o cobertura mínima de pruebas) para ser considerado aceptable antes de integrarse o desplegarse.

La efectividad de estas herramientas no depende únicamente de su configuración técnica, sino del contexto donde se aplican. Por ello, es importante considerar:

- El nivel de madurez del equipo de desarrollo.
- El tipo de proyecto (prototipo, MVP, aplicación crítica).
- Las prioridades del negocio en relación con calidad, seguridad y tiempo de entrega.

:::warning Imporntante
Una regla demasiado estricta en un equipo que recién comienza podría frenar el avance. Del mismo modo, reglas demasiado laxas en sistemas críticos pueden poner en riesgo la operación. Encontrar el equilibrio es clave.
:::

## Quality Profile

Un **Quality Profile** es un conjunto personalizado de reglas utilizadas para evaluar la calidad del código en un proyecto específico. Estas reglas se dividen en categorías como:

- Bugs
- Vulnerabilidades
- Estilo de código
- Complejidad

Los perfiles se pueden adaptar a las necesidades del equipo, activando o desactivando reglas según las prioridades del proyecto. Por ejemplo, puedes tener un perfil estricto para producción y otro más flexible para entornos de desarrollo.

### Buenas prácticas al configurar Quality Profiles

- Revisa las reglas activas por defecto y desactiva aquellas que no aplican a tu **contexto**.
- Utiliza distintos perfiles según el lenguaje de programación utilizado.
- Ajusta reglas que causan falsos positivos para evitar fatiga de alerta.
- Documenta los cambios realizados al perfil para mantener trazabilidad.

## Quality Gate

Un **Quality Gate** es un conjunto de condiciones que debe cumplir el código para ser considerado apto para su integración o despliegue.

Condiciones comunes en un Quality Gate:

- Cero bugs críticos o bloqueantes
- Cero vulnerabilidades de alta severidad
- Cobertura de pruebas mínima (por ejemplo, 80 %)
- Porcentaje de duplicación de código por debajo de un umbral

Si alguna condición no se cumple, el Quality Gate falla y se detiene el proceso de integración o entrega continua.

### Buenas prácticas con Quality Gates

- Define condiciones realistas para no bloquear innecesariamente el flujo de trabajo.
- Refuerza las condiciones más críticas (bugs, vulnerabilidades) y deja como opcionales las métricas más subjetivas (code smells).
- Ajusta los Quality Gates de forma incremental al madurar el proyecto.

### Errores comunes a evitar

- Ignorar los resultados del Quality Gate y hacer bypass manual del merge.
- Configurar condiciones demasiado estrictas desde el inicio.
- Usar el mismo Quality Gate para todos los proyectos sin considerar su naturaleza o criticidad.
