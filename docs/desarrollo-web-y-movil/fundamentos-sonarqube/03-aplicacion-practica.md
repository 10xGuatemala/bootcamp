---
sidebar_position: 3
sidebar_label: 1.3.3 Aplicación Práctica
---

# Aplicación Práctica

En este módulo exploraremos cómo aplicar de forma práctica los componentes centrales de SonarQube: los **Quality Profiles** y los **Quality Gates**. Estos dos elementos trabajan en conjunto para garantizar que el código cumpla con los estándares definidos por la organización.

- Los **Quality Profiles** contienen el conjunto de reglas de análisis estático que se utilizan para evaluar la calidad del código. Son personalizables por lenguaje y por proyecto.
- Los **Quality Gates** definen las condiciones mínimas que el código debe cumplir (como ausencia de bugs críticos o cobertura mínima de pruebas) para ser considerado aceptable antes de integrarse o desplegarse.

La efectividad de estas herramientas no depende únicamente de su configuración técnica, sino del contexto donde se aplican. Por ello, es importante considerar:

- El nivel de madurez del equipo de desarrollo.
- El tipo de proyecto (prototipo, MVP, aplicación crítica).
- Las prioridades del negocio en relación con calidad, seguridad y tiempo de entrega.

:::warning Importante
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

## Glosario

**Quality Profile** *(Quality Profile)* — conjunto personalizado de reglas de análisis estático aplicado por lenguaje y proyecto ([SonarQube docs](https://docs.sonarsource.com/sonarqube/latest/)).

**Quality Gate** *(Quality Gate)* — conjunto de condiciones que el código debe cumplir para considerarse apto para integración o despliegue.

**Falso positivo** *(False positive)* — alerta del análisis que no corresponde a un problema real; aumenta la fatiga de alerta si no se gestiona.

**Fatiga de alerta** *(Alert fatigue)* — pérdida de sensibilidad del equipo frente a demasiadas notificaciones, sobre todo irrelevantes.

**Umbral** *(Threshold)* — valor mínimo o máximo que una métrica debe cumplir para pasar el Quality Gate.

**Bypass** *(Bypass)* — acto de saltar un control automático; cuando es manual debe registrarse como excepción trazable.

:::info Referencias primarias
- [SonarQube documentation](https://docs.sonarsource.com/sonarqube/latest/) — definición oficial de Quality Profile y Quality Gate.
- [OWASP Top 10](https://owasp.org/Top10/) — categorías de riesgo utilizables como base de condiciones.
- [CWE Top 25](https://cwe.mitre.org/top25/) — debilidades frecuentes priorizables.
- [NIST SSDF](https://csrc.nist.gov/Projects/ssdf) — marco para integrar seguridad en el desarrollo.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** configurar Quality Profiles y Quality Gates adaptados al contexto del proyecto y al nivel de madurez del equipo.

**Entradas:**
- Tipo de proyecto (prototipo, MVP, aplicación crítica).
- Lenguajes utilizados y reglas activas por defecto.
- Prioridades del negocio (tiempo, calidad, seguridad).
- Historial de falsos positivos y hallazgos recurrentes.

**Pasos:**
1. Revisar y ajustar las reglas activas por defecto en el Quality Profile.
2. Crear perfiles diferenciados por lenguaje y criticidad del entorno.
3. Definir condiciones del Quality Gate (bugs, vulnerabilidades, cobertura, duplicación).
4. Calibrar umbrales considerando el nivel de madurez del equipo.
5. Documentar cambios en perfiles y gates para mantener trazabilidad.
6. Incrementar la exigencia gradualmente a medida que el proyecto madura.

**Salidas:**
- Quality Profile adaptado por lenguaje y contexto.
- Quality Gate con condiciones operativas claras.
- Registro de cambios y justificación de ajustes.

**Errores comunes:**
- Hacer bypass manual del Quality Gate sin registrar la excepción.
- Comenzar con condiciones demasiado estrictas y frenar el avance.
- Replicar el mismo gate para todos los proyectos sin considerar criticidad.
- No revisar falsos positivos, provocando fatiga de alerta.

**Referencias cruzadas:**
- [1.3.2 Conceptos Esenciales de Calidad y Seguridad](./02-conceptos-esenciales.md)
- [1.3.4 Integración de SonarQube en el ciclo DevOps](./04-ciclo-devops.md)
- [1.3.5 SAST y SCA en la fase de validación](./05-sast-y-sca-en-validacion.md)
</div>
