---
sidebar_position: 1
sidebar_label: 1.3.1 Introducción a SonarQube
---

# Introducción a SonarQube

## ¿Qué es SonarQube?

SonarQube™ es la herramienta líder para inspeccionar continuamente la calidad y seguridad del código, potenciando a los equipos de desarrollo. Soporta más de 25 lenguajes populares como Java, C#, VB.Net, JavaScript, TypeScript, C++, PHP, APEX entre otros.

### Funcionalidades principales

- Análisis estático del código fuente
- Evaluación según estándares de calidad definidos
- Identificación de bugs, vulnerabilidades, puntos sensibles de seguridad y problemas de mantenibilidad.

### ¿Cómo funciona SonarQube?

- El código fuente es analizado por el scanner, que aplica reglas estáticas configuradas previamente.
- Los resultados son enviados al servidor, donde se presentan métricas, alertas y dashboards visuales.
- Los resultados pueden utilizarse para permitir o no la publicación de la aplicación, o bien por los equipos que usan la información para mejorar continuamente la calidad y seguridad de su código.
  
![Funcionamiento de SonarQube](./img/introduccion-sonarqube-01.png)

## ¿Qué es SonarScanner?

SonarScanner es una herramienta de línea de comando utilizada para analizar el código fuente localmente y enviar resultados a SonarQube.

### ¿Cómo funciona?

1. El desarrollador ejecuta el análisis con SonarScanner desde su máquina local o desde un pipeline de CI/CD.
2. SonarScanner analiza el código estáticamente, generando un conjunto de métricas y hallazgos.
3. Estos resultados son enviados al servidor de SonarQube (SonarQube Server) donde son almacenados, organizados y visualizados a través de la interfaz web.

- [Descargar SonarScanner](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/)
- [SonarScanner para .NET](https://docs.sonarqube.org/latest/analyzing-source-code/scanners/sonarscanner-for-dotnet/)
- [SonarScanner para Maven](https://docs.sonarqube.org/latest/analyzing-source-code/scanners/sonarscanner-for-maven/)
- [Ver todos los lenguajes soportados](https://docs.sonarsource.com/sonarqube-server/latest/analyzing-source-code/languages/overview/)

## ¿Qué es SonarQube Server?

Sonar-Server es la plataforma centralizada que almacena los resultados del análisis estático, configura perfiles de calidad (Quality Profiles) y criterios de evaluación (Quality Gates), y ofrece reportes completos para la gestión del proyecto.

## ¿Qué es SonarLint?

SonarLint es una extensión para IDEs que permite detectar y corregir problemas en el código en tiempo real, antes de enviarlo a SonarQube.
[Instalar SonarLint](https://www.sonarlint.org/)

### ¿Cómo ayuda?

- SonarLint actúa como un analizador en vivo dentro del editor de código, alertando al desarrollador inmediatamente sobre bugs, code smells o posibles vulnerabilidades mientras escribe el código.
- Evita tener que esperar a que el análisis se ejecute en el servidor SonarQube, permitiendo una retroalimentación inmediata.
- Puede integrarse con SonarQube para mantener consistencia entre los resultados locales y los de servidor.

## Glosario

**SonarQube** *(SonarQube)* — plataforma de inspección continua de calidad y seguridad del código ([SonarQube docs](https://docs.sonarsource.com/sonarqube/latest/)).

**Análisis estático** *(Static code analysis / SAST)* — inspección del código fuente sin ejecutarlo para detectar bugs, code smells y vulnerabilidades.

**SonarScanner** *(SonarScanner)* — herramienta CLI que ejecuta el análisis y envía los resultados al servidor SonarQube.

**SonarLint** *(SonarLint)* — extensión para IDEs que entrega análisis en vivo durante la escritura del código.

**Bug** *(Bug)* — defecto que provoca comportamiento incorrecto del programa.

**Vulnerabilidad** *(Vulnerability)* — debilidad que puede ser explotada para comprometer la seguridad del sistema.

**Code smell** *(Code smell)* — señal de que el código, aunque funcione, presenta problemas de mantenibilidad.

:::info Referencias primarias
- [SonarQube documentation](https://docs.sonarsource.com/sonarqube/latest/) — guía oficial del producto.
- [OWASP Top 10](https://owasp.org/Top10/) — riesgos críticos de aplicaciones web.
- [CWE Top 25](https://cwe.mitre.org/top25/) — debilidades más comunes en software.
- [NIST SSDF](https://csrc.nist.gov/Projects/ssdf) — marco de desarrollo seguro.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** comprender el ecosistema SonarQube y seleccionar los componentes adecuados para instrumentar calidad y seguridad del código.

**Entradas:**
- Stack tecnológico y lenguajes del proyecto.
- Pipeline de CI/CD existente o previsto.
- Políticas internas de calidad y seguridad.
- IDEs usados por el equipo de desarrollo.

**Pasos:**
1. Identificar la edición de SonarQube o SonarCloud más acorde al tamaño y necesidades del proyecto.
2. Configurar SonarLint en los IDEs para feedback temprano al desarrollador.
3. Integrar SonarScanner en el pipeline para cada push o pull request.
4. Centralizar resultados en el SonarQube Server para seguimiento consolidado.
5. Definir responsables de revisión de hallazgos y frecuencia de seguimiento.

**Salidas:**
- Arquitectura de análisis estático definida para el proyecto.
- Configuración base de SonarLint, SonarScanner y SonarQube Server.
- Roles y flujos de revisión establecidos.

**Errores comunes:**
- Usar solo SonarLint sin consolidar resultados en el servidor.
- Configurar el scanner sin asociarlo al pipeline de CI.
- Dejar sin dueño los hallazgos reportados por SonarQube.
- Elegir una edición sin revisar la cobertura del lenguaje usado.

**Referencias cruzadas:**
- [1.3.2 Conceptos Esenciales de Calidad y Seguridad](./02-conceptos-esenciales.md)
- [1.3.4 Integración de SonarQube en el ciclo DevOps](./04-ciclo-devops.md)
- [1.3.5 SAST y SCA en la fase de validación](./05-sast-y-sca-en-validacion.md)
</div>
