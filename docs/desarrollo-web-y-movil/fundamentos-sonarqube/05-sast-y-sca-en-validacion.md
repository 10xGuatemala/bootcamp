---
sidebar_position: 5
title: SAST y SCA en la fase de validación
sidebar_label: 1.3.5 SAST y SCA en la fase de validación
---

# SAST y SCA en la fase de validación

Validar una entrega no significa revisar solo si "funciona". También conviene revisar si el cambio introduce riesgos técnicos o de seguridad. En ese punto aparecen dos prácticas complementarias: `SAST` y `SCA`.

:::info Ver también
Este módulo complementa [Integración de SonarQube en el ciclo DevOps](./04-ciclo-devops.md).
:::

## Dónde encajan en el ciclo

```mermaid
flowchart LR
    D[Desarrollo] --> PR[Pull Request]
    PR --> SAST[SAST<br/>análisis de código]
    PR --> SCA[SCA<br/>análisis de dependencias]
    SAST --> QG{Quality Gate}
    SCA --> QG
    QG -- Pasa --> M[Merge]
    QG -- Falla --> F[Corregir<br/>o documentar excepción]
    F --> PR
    classDef node fill:#ffffff,stroke:#0d4d92,stroke-width:2px,color:#0d4d92,rx:8,ry:8
    classDef ok fill:#0d4d92,color:#ffffff,stroke:#0b417b,rx:8,ry:8
    classDef decide fill:#ef9b50,color:#1a2f4d,stroke:#b56a2a,rx:8,ry:8
    class D,PR,SAST,SCA,F node
    class M ok
    class QG decide
```

## Qué aporta SAST

`Static Application Security Testing` analiza el código fuente o artefactos relacionados sin ejecutar la aplicación. Su objetivo es encontrar patrones de riesgo antes de que lleguen a producción.

SAST ayuda a detectar, por ejemplo:

- Errores de validación.
- Uso inseguro de datos.
- Patrones vulnerables conocidos.
- Problemas de mantenibilidad que también afectan la calidad.

## Qué no cubre SAST

SAST no resuelve todo. No reemplaza:

- Pruebas funcionales.
- Validación con usuarios.
- Revisión de arquitectura.
- Monitoreo en producción.
- Análisis de dependencias de terceros.

Por eso conviene verlo como parte de la validación, no como su totalidad.

## Dónde entra SAST en el ciclo

Una ubicación práctica para SAST es la fase de **Validación**, antes del merge o antes del despliegue. El objetivo es que el pipeline ayude a responder:

- ¿El cambio cumple estándares mínimos?
- ¿Existen hallazgos críticos o bloqueantes?
- ¿Se puede avanzar con seguridad razonable?

## Quality Gate como criterio operativo

Un `quality gate` convierte reglas técnicas en una decisión operativa. No reemplaza el juicio humano, pero ayuda a mantener consistencia.

Por ejemplo, puede bloquear avances cuando:

- Hay vulnerabilidades críticas.
- La calidad mínima definida por el equipo no se cumple.
- Aparecen hallazgos que requieren revisión antes del release.

## Qué aporta SCA

`Software Composition Analysis` se enfoca en las dependencias que una aplicación utiliza, incluidas las transitivas. Esto importa porque una aplicación puede ser segura en su código propio y aun así heredar riesgo desde librerías externas.

SCA ayuda a detectar:

- Dependencias vulnerables.
- Versiones obsoletas con alertas conocidas.
- Riesgos heredados por dependencias transitivas.
- Necesidad de actualizar, reemplazar o justificar excepciones.

## Dependencias transitivas

Una dependencia transitiva es una librería que tu proyecto no instaló directamente, pero que llega como requisito de otra. Ese detalle importa porque el riesgo puede existir aunque nadie la haya agregado “a mano”.

Por eso no basta con revisar solo `package.json`, `csproj` o archivos equivalentes. También hay que considerar el árbol completo de dependencias.

## Qué hacer ante una alerta

No todas las alertas se resuelven del mismo modo, pero el flujo básico suele ser:

1. Confirmar el impacto real.
2. Actualizar la dependencia si existe una versión segura compatible.
3. Sustituirla si ya no es mantenible o segura.
4. Documentar una excepción temporal si no hay alternativa inmediata.

La excepción no debe convertirse en olvido. Debe quedar visible y con contexto.

## Integración típica en forjas y pipelines

Hoy es común que plataformas de desarrollo y CI ofrezcan integración con análisis de código y dependencias. Lo relevante no es la marca de la herramienta, sino el flujo:

- Ejecutar análisis en cada cambio relevante.
- Mostrar resultados en el pipeline o pull request.
- Definir umbrales claros para bloquear o permitir avance.
- Mantener evidencia del riesgo aceptado cuando exista excepción.

## Validación completa: usuarios + SAST + SCA

Una validación madura combina varias miradas:

- **Usuarios o negocio:** confirman utilidad y comprensión del cambio.
- **SAST:** revisa riesgos en el código y hallazgos estáticos.
- **SCA:** revisa riesgo en dependencias y componentes reutilizados.

Juntas, estas revisiones reducen puntos ciegos.

## Utilidad para agentes y automatización

Para un agente, esta fase ofrece señales estructuradas sobre riesgo:

- Qué hallazgos son bloqueantes.
- Qué dependencias requieren atención.
- Qué excepciones están documentadas.
- Qué parte del pipeline valida calidad y seguridad.

Eso mejora la capacidad de generar resúmenes, checklists o skills operativas con contexto suficiente.

## Resumen para agentes

- **Objetivo:** validar seguridad y calidad antes de implementar.
- **Entradas comunes:** código fuente, dependencias directas y transitivas, reglas del pipeline.
- **Controles clave:** SAST, SCA, quality gate, excepción documentada.
- **Salidas esperadas:** decisión informada de avanzar, corregir o documentar riesgo.
- **Errores frecuentes:** confiar solo en pruebas funcionales, ignorar dependencias transitivas, aceptar alertas sin contexto.

## Glosario

**SAST** *(Static Application Security Testing)* — análisis estático del código fuente para detectar patrones de riesgo y vulnerabilidades sin ejecutar la aplicación.

**SCA** *(Software Composition Analysis)* — análisis de dependencias de terceros para identificar vulnerabilidades conocidas (CVE) y licencias incompatibles.

**Dependencia transitiva** *(Transitive dependency)* — dependencia incluida indirectamente por otras librerías del proyecto.

**CVE** *(Common Vulnerabilities and Exposures)* — identificador único público de una vulnerabilidad conocida ([CVE](https://www.cve.org/)).

**Excepción documentada** *(Documented exception)* — aceptación explícita y trazable de un riesgo cuando no es posible remediarlo de inmediato.

**Pull request check** *(PR check)* — verificación automática asociada a un pull request; puede bloquear el merge si falla.

:::info Referencias primarias
- [SonarQube documentation](https://docs.sonarsource.com/sonarqube/latest/) — referencias sobre SAST y Quality Gates.
- [OWASP Top 10](https://owasp.org/Top10/) — categorías base para SAST.
- [CWE Top 25](https://cwe.mitre.org/top25/) — debilidades críticas.
- [NIST SSDF](https://csrc.nist.gov/Projects/ssdf) — prácticas seguras en el ciclo de desarrollo.
- [CVE Program](https://www.cve.org/) — catálogo público de vulnerabilidades.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** aplicar SAST y SCA en la fase de validación para reducir riesgo antes del merge o despliegue.

**Entradas:**
- Código fuente y dependencias directas y transitivas.
- Reglas del pipeline de CI y del Quality Gate.
- Historial de vulnerabilidades conocidas en el stack.
- Excepciones documentadas y vigentes.

**Pasos:**
1. Ejecutar SAST sobre cada pull request para detectar patrones de riesgo en el código.
2. Ejecutar SCA sobre el árbol completo de dependencias, incluidas transitivas.
3. Evaluar hallazgos contra un Quality Gate y decidir avance, corrección o excepción.
4. Para alertas: confirmar impacto, actualizar, sustituir o documentar excepción.
5. Complementar con validación funcional con usuarios o negocio.
6. Registrar excepciones con contexto, dueño y fecha de revisión.

**Salidas:**
- Resultado del Quality Gate por pull request.
- Lista de dependencias vulnerables con acción asociada.
- Registro trazable de excepciones aceptadas.

**Errores comunes:**
- Confiar solo en pruebas funcionales y omitir SAST/SCA.
- Ignorar dependencias transitivas presentes solo en el árbol.
- Aceptar alertas sin evaluar impacto real.
- Convertir una excepción temporal en permanente por falta de seguimiento.

**Referencias cruzadas:**
- [1.3.3 Aplicación Práctica](./03-aplicacion-practica.md)
- [1.3.4 Integración de SonarQube en el ciclo DevOps](./04-ciclo-devops.md)
- [1.1.4 Autenticación y Autorización en APIs RESTful](../capacitacion-servicios-web-api-rest/04-autenticacion-autorizacion-rest.md)
</div>

---

<AuthorCredit />
