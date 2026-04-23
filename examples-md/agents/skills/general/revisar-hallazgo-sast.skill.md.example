<!-- Destino sugerido: .claude/skills/revisar-hallazgo-sast.skill.md -->

---
name: revisar-hallazgo-sast
description: Triaje de un hallazgo de SAST o SCA (SonarQube, CodeQL, Snyk, Dependabot). Clasifica si es bloqueante, mitigable o falso positivo según contexto real — no según la severidad del scanner — y propone un plan con alternativa recomendada y esfuerzo estimado. Obliga a justificar toda supresión con fecha de revisión.
---

# Skill: revisar-hallazgo-sast

Un equipo que silencia hallazgos con `// nosonar` para aprobar el Quality Gate está firmando deuda de seguridad con el nombre del commiter. Al año siguiente aparece el mismo hallazgo en un módulo vecino, nadie recuerda por qué se suprimió el original, y la auditoría externa encuentra veintisiete supresiones sin justificación. La alternativa opuesta — parchar cada `Critical` que reporta el scanner — también falla: consume tiempo en falsos positivos, retrasa features reales y convierte al scanner en un enemigo. Este skill existe para romper ese falso binario: un triaje que decide **qué hacer con cada hallazgo**, con criterio explícito para bloquear, mitigar o suprimir con justificación escrita.

## Cuándo invocarme

Úsame cuando:
- El Quality Gate de un PR falló por un hallazgo de análisis estático y hay que decidir antes del merge.
- Aparece una vulnerabilidad nueva en dependencias (SCA) y hay que decidir si parchar, ignorar con justificación o sustituir la librería.
- Se revisa el backlog acumulado de issues marcados como "code smell" o "vulnerability" para priorizarlos en un sprint de saneamiento.
- Se prepara un reporte de auditoría y hay que documentar por qué ciertos hallazgos están abiertos o suprimidos.

**No me uses para:**
- Escribir el fix — mi trabajo termina con la decisión y el plan; el código lo hace quien tenga contexto del módulo.
- Suprimir el hallazgo sin análisis. El propósito es **decidir qué hacer**, no silenciar.
- Triaje de incidentes en producción (un exploit activo) — para eso se sigue el runbook de respuesta a incidentes, no este skill.

## Entradas

1. **El hallazgo**: regla del linter/scanner (ej. `java:S2076`, `CWE-89`, `CVE-2024-12345`), archivo y línea, severidad reportada (`Blocker` / `Critical` / `Major` / `Minor`).
2. **Contexto del código afectado**: qué hace la función, de dónde viene el input, dónde se consume el output, si la ruta es pública o interna.
3. **Si es SCA**: versión actual de la dependencia, versión parchada disponible, notas del release de la versión parchada, alcance del uso en el proyecto (producción / build / test).
4. **Política de seguridad del equipo** si existe (ej. "todo `Critical` en endpoints de auth es bloqueante").

Si falta el archivo/línea o el contexto del flujo de datos, pedirlo antes de triar. La severidad real depende del contexto, no de la regla.

## Reglas obligatorias

- **La severidad reportada es una sugerencia, no una sentencia.** Un `Critical` en código muerto es menos urgente que un `Major` en el endpoint de login.
- **SAST ≠ SCA.** SAST analiza código propio; SCA analiza código de terceros. Los fixes son distintos y las decisiones también.
- **Toda supresión lleva justificación escrita y fecha de revisión.** `// nosonar` a secas es inaceptable. El comentario debe explicar **por qué** y **cuándo volver a revisar**.
- **Alternativas en orden de preferencia estricto.** Corregir el código > actualizar dependencia > aislar la superficie > suprimir con justificación. No saltar niveles sin justificar.
- **Lenguaje llano en el reporte.** El plan lo van a leer desarrolladores, el líder técnico y — si hay auditoría — un compliance que no sabe qué es `XSS reflejado`. Explicar el riesgo en una frase humana.
- **Decisión antes de esfuerzo.** Primero definir si es bloqueante; después estimar el esfuerzo. Estimar primero sesga hacia "no es bloqueante porque cuesta mucho".

## Pasos

### Paso 1: Clasificar la severidad real, no la reportada

El scanner asigna severidad por la regla, no por el contexto. Reevaluar con tres preguntas:

1. ¿La ruta afectada es alcanzable desde un usuario externo no autenticado?
2. ¿El dato manipulado cruza una frontera de confianza (input del usuario, archivo subido, llamada a servicio externo)?
3. ¿La función afectada toca datos sensibles (credenciales, PII, tokens, datos financieros)?

Tres "sí" → severidad real alta, aunque el scanner diga `Minor`. Tres "no" → severidad real baja, aunque diga `Critical`.

- Mal: *"El scanner dice Critical, es bloqueante."* No analiza si el código está vivo, si la ruta es pública, si hay sanitización aguas arriba.
- Bien: *"El scanner reporta Critical (SQL Injection, `java:S3649`), pero la ruta está detrás de `[Authorize(Roles = \"Admin\")]` y el input pasa por un parser que normaliza a `Guid` antes de la query. Severidad real: Medium. No bloqueante, abrir issue para parametrizar la query."*

**Valor para el agente:** sin reevaluación el scanner manda la agenda del sprint. El equipo trabaja lo que la herramienta reporta en vez de lo que de verdad importa.

### Paso 2: Verificar si es falso positivo antes que asumir que es real

Trazar el flujo del input/output afectado. Preguntas puntuales:

- ¿El input está sanitizado aguas arriba (DTO con validación, middleware, librería de parsing)?
- ¿La ruta es alcanzable? (Código muerto detrás de un flag desactivado en producción no es explotable).
- ¿El scanner conoce la anotación/attribute que marca la sanitización? Algunos scanners no interpretan custom sanitizers.

- Mal: declarar "falso positivo" porque el desarrollador jura que no es explotable. Sin evidencia.
- Bien: declarar "falso positivo: el input pasa por `InputSanitizer.Normalize()` en la línea 42 antes de llegar a la query de la línea 89; el scanner no reconoce el sanitizer porque es custom". Con referencia a líneas concretas.

**Valor para el agente:** un falso positivo verificado se suprime con confianza; un "yo creo que es falso positivo" se suprime con riesgo. El triaje exige evidencia.

### Paso 3: Evaluar alternativas en orden de preferencia

Alternativas ordenadas de más a menos deseable:

1. **Corregir el código.** Preferido. Elimina la causa raíz.
2. **Actualizar la dependencia (SCA).** Cuando el hallazgo es una CVE de librería. Revisar notas del release para detectar breaking changes antes de actualizar.
3. **Aislar la superficie.** Mover el endpoint detrás de auth, restringir por IP, quitar del público. Mitiga sin eliminar.
4. **Suprimir con justificación escrita y fecha de revisión.** Último recurso. Solo cuando las tres anteriores no aplican (ej. falso positivo verificado, dependencia sin versión parchada).

- Mal: *"Actualizar la librería de `4.1` a `5.0` soluciona la CVE."* Sin ver las notas del release, puede romper la API pública y trabar el merge de todos.
- Bien: *"La CVE está parchada en `4.1.7`. La `5.0` tiene breaking changes (ver `CHANGELOG` de la librería). Recomiendo bump a `4.1.7` y agendar la migración a `5.x` como iniciativa aparte."*

**Valor para el agente:** saltar al "suprimir" sin pasar por corregir/actualizar es la forma más común de acumular deuda de seguridad. El orden obliga a justificar por qué no se toma la opción más fuerte.

### Paso 4: Aplicar la matriz de decisión bloqueante vs no bloqueante

| Condición | Decisión |
|---|---|
| Afecta datos sensibles, auth o input de usuario externo | **Bloqueante**: arreglar antes del merge. |
| CVE con exploit público y la dependencia está en producción | **Bloqueante**: parchar o reemplazar. |
| Code smell que afecta mantenibilidad pero no seguridad | **No bloqueante**: crear issue, priorizar en el sprint. |
| Falso positivo verificado con evidencia | **No bloqueante**: suprimir con comentario que explique *por qué* y *cuándo revisar*. |
| Vulnerabilidad en dependencia de build/test sin alcance en producción | **No bloqueante**: documentar y planificar. |
| CVE sin exploit público ni vector claro en tu flujo | **No bloqueante**: monitorear, actualizar en el próximo ciclo de parches. |

Si la matriz no cubre el caso, registrar el gap en el reporte y pedir decisión al líder técnico — no improvisar.

- Mal: *"Es `Major` en el backlog, lo dejamos."* Sin pasar por la matriz.
- Bien: aplicar matriz → "Afecta el input del formulario de login, que está expuesto sin auth previa → bloqueante".

**Valor para el agente:** la matriz es el criterio compartido del equipo. Sin ella, cada desarrollador triaje distinto y las decisiones no son auditables.

### Paso 5: Estimar el esfuerzo de la alternativa recomendada

Estimación relativa, no hora-precisa:

- **< 1 h**: cambio localizado (reemplazar una llamada insegura por la segura).
- **1–4 h**: cambio con testing y revisión (actualizar una dependencia con tests de regresión).
- **> 4 h**: refactor o migración (cambiar de librería, rediseñar una capa de sanitización).

La estimación alimenta la priorización, no la decisión. Un bloqueante de > 4 h sigue siendo bloqueante; lo que cambia es la planificación.

### Paso 6: Si se suprime, documentar con rigor

Toda supresión lleva:

1. **Justificación escrita** en el código, al lado de la supresión: *por qué* es falso positivo o por qué no se puede corregir.
2. **Fecha de revisión** explícita (ej. `// Revisar en 2026-10-01`).
3. **Referencia al issue** o ticket donde se registra la decisión.

```csharp
// Mal
// nosonar
var query = $"SELECT * FROM users WHERE id = {userId}";

// Bien
// SonarQube java:S3649 - Supresión justificada:
// `userId` proviene de `[FromRoute] Guid userId` y el framework ya
// garantiza que es Guid válido antes de llegar acá. No hay concatenación
// de strings externos.
// Revisar en 2026-10-01 (ticket SEC-432) — si cambia la firma a string, reevaluar.
var query = $"SELECT * FROM users WHERE id = '{userId}'";
```

**Valor para el agente:** la auditoría del próximo año pregunta "¿por qué está suprimido esto?". Un comentario con razón y fecha responde en diez segundos. `// nosonar` a secas convierte la pregunta en una investigación de tres horas.

## Formato de salida

Reporte estructurado con tres bloques obligatorios:

**Hallazgo**
- Regla: `{scanner}:{rule-id}` (ej. `SonarQube:java:S2076`).
- Ubicación: `path/to/file.ext:LINE`.
- Severidad reportada: `{Blocker|Critical|Major|Minor}`.
- Severidad real: `{High|Medium|Low}` según el análisis de contexto.
- Una frase en lenguaje llano: qué riesgo representa.

**Análisis**
- Flujo del input/output afectado (de dónde viene, qué lo toca, dónde se consume).
- Si es falso positivo: evidencia concreta (líneas del código que sanitizan, capa que bloquea, flag que inhabilita la ruta).
- Si es real: escenario de explotación plausible descrito en una o dos frases.

**Plan**
- Decisión: `bloqueante` / `no bloqueante` / `falso positivo`.
- Alternativa recomendada y esfuerzo estimado (`< 1 h`, `1–4 h`, `> 4 h`).
- Si se suprime: justificación escrita que va al código + fecha de revisión + ticket.
- Si es bloqueante: responsable propuesto y fecha objetivo.

## Antes de entregar, verifico

- [ ] La severidad real está argumentada, no copiada de la reportada.
- [ ] El análisis cita archivo y línea concretas, no afirmaciones genéricas.
- [ ] La decisión usa la matriz bloqueante/no bloqueante.
- [ ] Si es supresión, el comentario tiene justificación + fecha de revisión + ticket.
- [ ] El reporte distingue explícitamente SAST vs SCA si aplica.
- [ ] No propuse un fix concreto si no me lo pidieron — el skill triaje, no implementa.
- [ ] Si pasé directo a "suprimir" sin evaluar corregir/actualizar/aislar, justifiqué por qué.

## Errores comunes

- **Confundir "el scanner dice Critical" con "es crítico para este proyecto".** La severidad real depende del contexto: ruta pública, dato sensible, alcance del blast radius.
- **Suprimir con `// nosonar` sin justificación.** Cualquier supresión debe explicar *por qué* es falso positivo o por qué no se puede corregir, y *cuándo* volver a revisar.
- **Aceptar el primer parche de dependencia sin ver las notas del release.** A veces la versión parchada introduce breaking changes que rompen más cosas de las que arregla.
- **Tratar SAST y SCA como equivalentes.** SAST analiza tu código — el fix es tuyo. SCA analiza código que no escribiste — el fix suele ser un bump de versión, y a veces no hay parche disponible.
- **Empezar por el esfuerzo antes que por la decisión.** Estimar primero sesga hacia "no es bloqueante porque cuesta mucho". Decide primero, estima después.
- **Confundir falso positivo con "no me quiero meter".** Falso positivo exige evidencia; "no prioritario" exige matriz. No son sinónimos.
- **Abrir el reporte con jerga.** El líder técnico y el compliance también lo leen. Una frase en lenguaje llano al inicio de cada hallazgo evita tres reuniones de traducción.

## Glosario

**SAST** *(Static Application Security Testing)* — análisis del código fuente propio sin ejecutarlo, para detectar patrones inseguros (inyecciones, uso de APIs peligrosas, manejo de criptografía). Herramientas típicas: SonarQube, CodeQL, Semgrep.

**SCA** *(Software Composition Analysis)* — análisis de las dependencias de terceros para detectar vulnerabilidades conocidas (CVE). Herramientas típicas: Snyk, Dependabot, Renovate, npm audit.

**CVE** *(Common Vulnerabilities and Exposures)* — identificador canónico de una vulnerabilidad pública (ej. `CVE-2024-12345`). Mantenido por [MITRE](https://cve.mitre.org/).

**CWE** *(Common Weakness Enumeration)* — taxonomía de debilidades de código (ej. `CWE-89` = SQL Injection). Útil para clasificar familias de hallazgos, no incidentes individuales.

**Quality Gate** *(Quality Gate)* — conjunto de umbrales que un PR debe pasar para mergear (cobertura mínima, hallazgos `Blocker` = 0, duplicación máxima). SonarQube lo popularizó; otras herramientas tienen el equivalente.

**Falso positivo verificado** *(Verified false positive)* — hallazgo marcado como no explotable con evidencia concreta (código que sanitiza, ruta inalcanzable, flag desactivado). Requisito para suprimir sin culpa.

**Supresión con justificación** *(Justified suppression)* — decisión explícita de ignorar un hallazgo con comentario en el código que explica razón y fecha de revisión. Nunca `// nosonar` a secas.

:::info Referencias primarias
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) — categorías estándar de riesgo para aplicaciones web. Marco canónico para discutir severidad real.
- [CWE](https://cwe.mitre.org/) — taxonomía de debilidades de código. Útil para clasificar hallazgos.
- [CVE](https://cve.mitre.org/) — identificadores canónicos de vulnerabilidades. Toda supresión de hallazgo SCA debe citar el CVE correspondiente.
- [SonarQube Rules](https://rules.sonarsource.com/) — base de datos de reglas con ejemplos de fix. Útil para no inventar la corrección desde cero.
- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) — herramienta SCA de referencia abierta.
:::
