---
sidebar_position: 2
sidebar_label: 1.3.2 Conceptos Esenciales de Calidad y Seguridad
---

# Conceptos Esenciales de Calidad y Seguridad

## Ediciones de SonarQube

SonarQube está disponible en diferentes ediciones según el nivel de funcionalidad requerido por tu organización:

- **[Community Edition](https://www.sonarsource.com/open-source-editions/sonarqube-community-edition/):** Gratuita y open-source. Soporta análisis básico para varios lenguajes y es ideal para proyectos pequeños o individuales.
- **[Developer Edition](https://www.sonarsource.com/plans-and-pricing/developer/):** Incluye características adicionales como soporte para análisis de ramas, detección avanzada de vulnerabilidades y soporte para lenguajes como **PL/SQL**, Swift, Kotlin y más.
- **[Enterprise Edition](https://www.sonarsource.com/plans-and-pricing/enterprise/):** Pensada para organizaciones más grandes, esta edición incluye soporte para **APEX**, herramientas de gobernanza, análisis de portafolios, y soporte para múltiples proyectos y equipos a escala empresarial.
- **[Data Center Edition](https://www.sonarsource.com/plans-and-pricing/data-center/):** Para instalaciones altamente disponibles y distribuidas, orientada a grandes organizaciones con alta demanda de disponibilidad.
- **[SonarCloud](https://www.sonarsource.com/products/sonarcloud/):** Opción cloud completamente gestionada que ofrece análisis continuo de calidad de código sin necesidad de instalar ni mantener una infraestructura propia.

## Conceptos clave

- 🪲 **Bug:** Un error de programación que puede provocar fallos o comportamientos inesperados en tiempo de ejecución.
- 🛡️ **Vulnerabilidad:** Punto débil en el código que puede ser explotado por un atacante.
- 🔍 **Security Hotspots:** Partes del código que requieren revisión manual por su sensibilidad en términos de seguridad.
- 🧹 **Code Smells:** Problemas que no causan fallos directamente, pero dificultan el mantenimiento, comprensión o escalabilidad del código.
- 💸 **Deuda técnica:** Costo acumulado por decisiones de desarrollo que priorizan entregas rápidas sobre calidad, generando trabajo correctivo futuro. En otras palabras, es el estimado del trabajo adicional necesario para solucionar problemas en el código, como bugs, vulnerabilidades y code smells.
- 📊 **Cobertura de código:** Porcentaje del código fuente cubierto por pruebas unitarias o de integración.
- 🧪 **Cobertura de pruebas:** Grado en que los tests ejercitan las diferentes rutas del código y su lógica.
- 🧬 **Duplicación de código:** Fragmentos repetidos innecesariamente, lo cual incrementa la complejidad y el esfuerzo de mantenimiento.

## Importancia de la seguridad y calidad del código

SonarQube permite detectar automáticamente:

- Inyecciones SQL
- Problemas de autenticación y autorización
- Fugas de información
- Código duplicado
- Problemas de diseño
- Baja mantenibilidad o legibilidad

## Estadísticas relevantes

- 🔐 El **96%** de las aplicaciones analizadas contienen al menos una vulnerabilidad crítica (Veracode).
- 🧪 El **85%** de los problemas de calidad provienen de la falta de pruebas y revisiones (Gartner).
- 💥 El **93%** de las brechas de seguridad tienen origen en errores de programación (SANS Institute).
- 📊 El **68%** de las aplicaciones analizadas por CAST contenían al menos una vulnerabilidad crítica.
- ⚠️ Según **OWASP**, las inyecciones SQL siguen siendo la vulnerabilidad más común en aplicaciones web.

:::tip
Mantener la calidad y seguridad del código no es opcional, es una parte integral del desarrollo profesional de software.
:::

## Glosario

**Security Hotspot** *(Security Hotspot)* — fragmento de código sensible en términos de seguridad que requiere revisión manual para determinar si es realmente vulnerable.

**Deuda técnica** *(Technical debt)* — costo estimado del trabajo adicional necesario para corregir decisiones de desarrollo que priorizaron velocidad sobre calidad.

**Cobertura de código** *(Code coverage)* — porcentaje del código ejercitado por las pruebas automatizadas.

**Duplicación de código** *(Code duplication)* — fragmentos de código repetidos innecesariamente; incrementa complejidad y esfuerzo de mantenimiento.

**SonarCloud** *(SonarCloud)* — versión gestionada en la nube de SonarQube.

**Inyección SQL** *(SQL injection)* — vulnerabilidad clásica en la que entradas sin sanitizar se ejecutan como parte de una consulta; listada en [OWASP Top 10](https://owasp.org/Top10/).

:::info Referencias primarias
- [SonarQube documentation](https://docs.sonarsource.com/sonarqube/latest/) — glosario oficial de métricas y conceptos.
- [OWASP Top 10](https://owasp.org/Top10/) — riesgos críticos en aplicaciones web.
- [CWE Top 25](https://cwe.mitre.org/top25/) — debilidades más frecuentes.
- [NIST SSDF](https://csrc.nist.gov/Projects/ssdf) — marco de desarrollo seguro.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** dominar los conceptos esenciales de calidad y seguridad del código que utiliza SonarQube para evaluar un proyecto.

**Entradas:**
- Código fuente del proyecto.
- Reportes previos de análisis estático (si existen).
- Estándares de cobertura y calidad definidos.
- Contexto del negocio respecto a riesgos aceptables.

**Pasos:**
1. Diferenciar bugs, vulnerabilidades, security hotspots y code smells.
2. Cuantificar deuda técnica acumulada como indicador operativo.
3. Medir cobertura de código y de pruebas como insumo del análisis.
4. Detectar duplicación y problemas de diseño que incrementen el esfuerzo de mantenimiento.
5. Priorizar hallazgos según severidad y frecuencia.

**Salidas:**
- Mapa inicial de hallazgos por categoría.
- Línea base de deuda técnica y cobertura.
- Lista priorizada de remediaciones.

**Errores comunes:**
- Confundir code smells con bugs y demorar correcciones críticas.
- Ignorar security hotspots por considerarlos informativos.
- Medir cobertura sin validar calidad de las pruebas.
- Tratar todos los hallazgos con la misma severidad.

**Referencias cruzadas:**
- [1.3.1 Introducción a SonarQube](./01-introduccion-sonarqube.md)
- [1.3.3 Aplicación Práctica](./03-aplicacion-practica.md)
- [1.3.5 SAST y SCA en la fase de validación](./05-sast-y-sca-en-validacion.md)
</div>
