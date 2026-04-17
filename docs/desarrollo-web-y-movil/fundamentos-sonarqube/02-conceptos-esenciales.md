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

:::tip TIP
Mantener la calidad y seguridad del código no es opcional, es una parte integral del desarrollo profesional de software.
:::
