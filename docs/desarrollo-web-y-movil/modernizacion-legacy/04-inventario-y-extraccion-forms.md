---
sidebar_position: 4
title: Inventario y extracción desde Oracle Forms
sidebar_label: 1.4.4 Inventario y extracción desde Oracle Forms
---

# Inventario y extracción desde Oracle Forms

El módulo anterior cerró con el stack destino elegido y la matriz firmada. Falta lo que decide si el proyecto cumple plazos o se vuelve un pozo: saber qué hay realmente en el legacy y dónde vive cada regla de negocio que ha sobrevivido veinte años. La promesa de "ya sabemos qué tiene el sistema" se desmorona la primera semana cuando alguien encuentra un reporte mensual que ningún área recordaba, un trigger que valida una regla regulatoria que nadie documentó, o una biblioteca compartida que tres pantallas invocan y que no aparecía en ningún diagrama.

Este módulo entrega el inventario verificable que la migración necesita como línea base: cómo listar formularios, librerías, menús y reportes en el filesystem; cómo cruzarlos con los objetos de base de datos que invocan; cómo clasificar la lógica embebida en triggers de Forms para decidir qué se queda en la base y qué se lleva al backend nuevo; y cómo organizar la migración pantalla por pantalla con operación en paralelo en lugar de un cutover de fe.

## Qué es un inventario verificable

Un inventario verificable es una lista construida con herramientas, no con memoria. Tiene dos lados que deben cruzarse: lo que existe en el filesystem (archivos fuente de Forms y Reports) y lo que existe en la base de datos (paquetes, procedimientos, funciones, vistas, triggers, dependencias). Cada lado prueba al otro. Si un archivo `.fmb` invoca un paquete que ya no existe en la base, el formulario está muerto desde hace tiempo aunque nadie lo haya apagado. Si la base tiene un paquete que ningún fuente referencia, alguien lo invoca por otra vía o quedó huérfano.

Un inventario que no cruza ambos lados es una lista. Un inventario verificable es una hipótesis comprobable contra el código y la base — y por eso sirve como línea base del proyecto.

## Por qué importa

El inventario no es una formalidad administrativa: es lo que permite cotizar, planificar y medir avance.

Sin inventario, **cotizar es adivinar**. El proveedor estima por intuición o por similitud con otros proyectos, y la primera factura de cambio aparece cuando se descubre que la "pantalla simple" depende de tres bibliotecas `.pll` y de un paquete que nadie había mencionado.

Sin inventario, **planificar es marketing**. Las gráficas de avance miden contra una percepción, no contra una lista. "Llevamos el 30 % migrado" suena bien hasta que alguien pregunta "¿el 30 % de cuántos formularios?" y la respuesta es "más o menos cien".

Sin inventario, **el retiro nunca llega**. El proyecto vive eternamente en modo "casi terminamos" porque cada semana aparece una pantalla más que nadie había contado. El cierre del proyecto exige una lista cerrada que se vaya marcando.

Con inventario verificable, cotización, planificación y avance se miden contra la misma realidad. La conversación con el cliente deja de ser sobre porcentajes y pasa a ser sobre archivos concretos.

## Objetivo

Al terminar este módulo, sabes producir un inventario cruzado de una aplicación Oracle Forms, clasificar los triggers de Forms en cinco categorías que deciden su destino, ejecutar la migración pantalla por pantalla con operación en paralelo verificable, y planificar el retiro del legacy con una ventana de respaldo en frío.

## Entradas

- Acceso a los archivos fuente de Forms: `.fmb` (formularios), `.pll` (librerías compartidas), `.mmb` (menús), `.rdf` (reportes).
- Oracle Forms Builder instalado, o al menos `frmf2xml` accesible para convertir `.fmb` a XML procesable.
- Usuario de base de datos con permiso de `SELECT` sobre `USER_OBJECTS`, `USER_DEPENDENCIES`, `USER_SOURCE`, `USER_TRIGGERS`, `USER_VIEWS`, `USER_PROCEDURES`.
- Decisión de stack destino ya tomada (matriz 10X firmada en [1.4.3](./03-migracion-desde-oracle-forms.md)).
- Inventario funcional declarado por las áreas de negocio, aunque sea una lista informal — sirve para cruzar con lo que se encuentre en el filesystem.

## Pasos

### Paso 1: Inventariar los componentes Forms del filesystem

El primer corte se hace contra el filesystem. Listar `.fmb`, `.pll`, `.mmb` y `.rdf` ya entrega el universo de archivos que el legacy contiene.

```bash
find . -name "*.fmb" -o -name "*.pll" -o -name "*.mmb" -o -name "*.rdf" \
  | sort | tee inventario-fuentes-forms.txt

# Convertir los .fmb a XML para que el contenido sea procesable por agentes y scripts.
frmf2xml *.fmb
```

El archivo `inventario-fuentes-forms.txt` queda como artefacto del proyecto. Cualquier afirmación posterior — "migramos cinco pantallas más" — se mide contra esa lista, no contra una percepción.

- Mal: tomar la lista de pantallas que el área de negocio recuerda y considerarla "el inventario". Quedan fuera utilitarios internos, reportes que solo corre contabilidad a fin de mes y procedimientos huérfanos que algún trigger invoca.
- Bien: combinar lo que el área recuerda con lo que `find` encuentra, y marcar como pregunta abierta cualquier archivo presente en el filesystem que nadie reconoce.

**Valor para el agente:** un agente que opera sobre el `.txt` sabe que la lista es la verdad de referencia. Cuando reporte "completé 12 de 47 archivos", el 47 es verificable.

### Paso 2: Inventariar los objetos de base que el legacy consume

El segundo corte se hace contra la base. La aplicación Forms invoca paquetes, procedimientos, funciones y vistas en la base; conocerlos es indispensable para decidir qué se queda y qué se mueve.

```sql
-- Triggers que la aplicación tiene registrados sobre tablas.
SELECT trigger_name, table_name, triggering_event, status
FROM   user_triggers
ORDER  BY table_name, trigger_name;

-- Paquetes, procedimientos y funciones que Forms invoca.
SELECT object_type, object_name, status, last_ddl_time
FROM   user_objects
WHERE  object_type IN ('PACKAGE','PROCEDURE','FUNCTION')
ORDER  BY object_type, object_name;

-- Vistas que las pantallas consumen como fuente de datos.
SELECT view_name FROM user_views ORDER BY view_name;

-- Dependencias: quién depende de quién.
SELECT name, type, referenced_name, referenced_type
FROM   user_dependencies
WHERE  referenced_owner = USER;
```

El resultado de las cuatro consultas se exporta a un CSV (`inventario-objetos-bd.csv`) que pasa a formar parte del artefacto del proyecto, junto con `inventario-fuentes-forms.txt`.

El cruce entre ambos lados es el momento más informativo del inventario. Un `.fmb` que invoca un paquete que no aparece en `user_objects` significa que la pantalla está rota o que el paquete vive en otro esquema. Un paquete activo que ningún `.fmb` referencia significa que alguien lo invoca por otra vía — probablemente un job programado o una integración externa que no aparecía en el alcance.

**Valor para el agente:** las discrepancias entre fuente y base son las preguntas correctas para el equipo cliente. Sin el cruce, esas preguntas no se hacen.

### Paso 3: Clasificar los triggers de Forms

Los triggers de Forms (`WHEN-VALIDATE-ITEM`, `WHEN-BUTTON-PRESSED`, `POST-QUERY`, `KEY-COMMIT`, entre otros) son el lugar donde décadas de reglas de negocio quedaron escritas. Migrar el formulario sin extraer estas reglas es perder la migración.

Por cada `.fmb` convertido a XML, listar los triggers que contiene:

```bash
grep -A2 '<Trigger ' formulario.xml | grep 'Name=' | sort -u
```

Y por cada trigger, clasificarlo en una de estas cinco categorías que deciden su destino:

| Categoría del trigger | Qué hacer en la migración |
|---|---|
| Validación de un campo (formato, rango, existencia) | Migrar al backend nuevo como validación de DTO o regla de servicio. Mantener en la base solo si la regla protege integridad transaccional. |
| Cálculo derivado (totales, fechas calculadas) | Migrar al backend si el dato no necesita persistirse. Si se persiste, dejar en un trigger de tabla o columna virtual. |
| Llamada a procedimiento de un paquete | Mantener el procedimiento en la base; el backend lo invoca. No reescribir el procedimiento en C# o Java. |
| Navegación entre pantallas (`GO_BLOCK`, `GO_ITEM`) | Descartar. La navegación pertenece al frontend nuevo y no se traduce. |
| Manejo de UI (mensajes, colores, foco) | Descartar. Pertenece al diseño del frontend, no a la lógica de negocio. |

- Mal: copiar el cuerpo de un `WHEN-VALIDATE-ITEM` y traducirlo línea por línea a C# preservando hasta los nombres de variables. El resultado es código nuevo con cara de moderno y deuda técnica antigua adentro.
- Bien: leer el trigger, identificar la regla concreta ("el correlativo de envío no puede ser menor que el último emitido") y modelarla con la API del stack destino. Documentar la regla extraída en el backlog del módulo nuevo.

**Valor para el agente:** la tabla convierte la decisión en un ejercicio verificable. El agente no especula con cada trigger — clasifica y aplica el destino correspondiente.

### Paso 4: Migrar pantalla por pantalla con paralelo

Cada migración de pantalla sigue el mismo ciclo. La consistencia del ciclo es lo que hace medible el avance del proyecto.

1. **Documentar el contrato de la pantalla vieja**: qué datos lee, qué escribe, qué reglas valida, qué reportes dispara.
2. **Implementar la pantalla nueva** consumiendo la misma base de datos, vía API del stack destino.
3. **Operar en paralelo**: el área de negocio empieza a usar la pantalla nueva, pero la vieja queda disponible como red de seguridad.
4. **Reconciliar diariamente**: un job compara registros creados o modificados por ambos sistemas. Cero discrepancias durante el período acordado es la señal de luz verde.
5. **Cutover por pantalla** cuando la reconciliación es estable: la pantalla vieja queda en solo lectura o se retira.

- Mal: cutover "big bang" — un fin de semana se apaga la pantalla vieja y se enciende la nueva. Sin red de seguridad, cualquier regla no extraída se descubre con un cliente molesto, no con un test.
- Bien: operar dos meses con ambas pantallas en paralelo, reconciliando datos a diario, antes de retirar la vieja. La operación nunca se detiene y el equipo aprende del comportamiento real antes del compromiso final.

**Valor para el agente:** el ciclo es repetible. Cada pantalla migrada produce las mismas cinco salidas verificables, y el avance se mide pantalla por pantalla — no por porcentajes que nadie puede auditar.

### Paso 5: Planificar el retiro del legacy

El cutover no es un evento; es el resultado acumulado de pantallas migradas en serie. Aun así, retirar Forms requiere un plan explícito que la dirección del proyecto firma:

- **Inventario de pantallas restantes** que aún no migraron. Las últimas suelen ser las raras (reportes anuales, mantenimientos esporádicos). Decidir si migrarlas o aceptar que sobrevivirán en el legacy operadas por excepción.
- **Bibliotecas `.pll` huérfanas**: si una librería ya no es invocada por ninguna pantalla viva, marcarla para retiro junto con sus procedimientos asociados.
- **Documentación de lo que quedó en la base**: vistas, procedimientos y triggers que ahora invoca el backend nuevo. El equipo DBA hereda esta lista y le da mantenimiento.
- **Ventana de respaldo en frío**: entre 30 y 90 días después del retiro, mantener el código Forms en un servidor cold para poder volver atrás si aparece una regla no detectada. Cumplido el plazo sin incidentes, se archiva.

**Valor para el agente:** el cutover sin retiro es una migración que no terminó. Forzar el plan de retiro desde el inicio mantiene el proyecto orientado al cierre.

## Salidas

- **`inventario-fuentes-forms.md`** — lista cruda de `.fmb`, `.pll`, `.mmb`, `.rdf` encontrados en el filesystem.
- **`inventario-objetos-bd.md`** — paquetes, procedimientos, funciones, vistas, triggers de la base, con su `LAST_DDL_TIME` y dependencias.
- **`triggers-clasificados.md`** — por cada trigger de Forms, su clasificación (validación / cálculo / paquete / navegación / UI) y destino.
- **`backlog-migracion-pantallas.md`** — pantallas en orden de migración, con contrato documentado y criterio de cutover por pantalla.
- **`plan-retiro-legacy.md`** — pantallas restantes, librerías huérfanas, objetos heredados al equipo DBA, ventana de cold storage.

Las cinco plantillas, junto con el slash command `/migrar-forms` y los scripts SQL/bash que las pueblan, están listos para copiar desde [`examples-md/project/forms-migration/`](https://github.com/10xGuatemala/bootcamp/tree/main/examples-md/project/forms-migration) en el repositorio público del bootcamp.

## Errores comunes

- **Saltarse el inventario porque "ya sabemos qué tiene el sistema".** Quien hace la migración rara vez sabe; quien sabía ya no está. Para humanos: el proyecto descubre pantallas a los seis meses. Para agentes: cualquier reporte de avance contra una lista incompleta es ruido.
- **Inventariar solo el filesystem o solo la base.** Cada lado prueba al otro; sin cruce, las discrepancias se descubren en producción.
- **Tratar los triggers como código a traducir.** Copiar PL/SQL a C# o Java línea por línea genera deuda nueva con cara de moderna. La migración es la ocasión de extraer la regla, no de duplicar la implementación.
- **Cutover big bang.** Sin operación en paralelo, cualquier regla no detectada cae como incidente en producción.
- **Olvidar las bibliotecas `.pll`.** Migrar pantallas que dependen de `.pll` no migradas duplica la biblioteca de forma silenciosa en el backend nuevo.
- **Confundir lógica de UI con lógica de negocio en los triggers.** Mensajes y colores van a la basura; las validaciones cruzan al backend. Distinguirlas exige leer el trigger, no asumir por el nombre.
- **Cerrar el proyecto sin plan de retiro.** Forms queda vivo "por si acaso" y la organización mantiene dos sistemas en paralelo indefinidamente.

## Prompt de auditoría

Para auditar el inventario de un proyecto en curso, copia este prompt en la sesión del agente:

```
Audita el inventario del proyecto de migración Forms contra el módulo
"Inventario y extracción desde Oracle Forms". Reporta:

1. ¿Existe inventario-fuentes-forms.txt? ¿Es consistente con find sobre el repo
   actual? Reportar discrepancias.
2. ¿Existe inventario-objetos-bd.csv? ¿Las dependencias en user_dependencies
   cuadran con los referenced objects?
3. ¿Cuántos triggers están clasificados en triggers-clasificados.md vs. el
   total que aparece en los .xml convertidos de .fmb?
4. ¿Cada pantalla del backlog tiene contrato documentado y criterio de cutover?
5. ¿Existe plan-retiro-legacy.md con pantallas restantes, .pll huérfanas y
   ventana de cold storage?

Salida: tabla por artefacto con columnas "Esperado", "Encontrado", "Brecha",
"Acción sugerida".
```

:::tip El inventario es la línea base, no un entregable
La tentación es producir el inventario al inicio y nunca volver a abrirlo. Mantenlo vivo: cada pantalla migrada, cada `.pll` retirado, cada paquete heredado al equipo DBA actualiza el inventario en el mismo PR del cambio. Un inventario que envejece es marketing; uno que se actualiza es una herramienta.
:::

## Puente al siguiente módulo

Con el inventario cruzado, los triggers clasificados y el ciclo de migración pantalla por pantalla en marcha, queda una decisión técnica que define la calidad del código nuevo desde el primer commit: cómo construye el sistema migrado sus consultas SQL. El módulo siguiente cubre la conversión sistemática del SQL dinámico concatenado — endémico en Forms y en sus reemplazos apresurados — a consultas parametrizadas que cierran inyección y mejoran el rendimiento al mismo tiempo.

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** producir un inventario cruzado de una aplicación Oracle Forms, clasificar los triggers, ejecutar la migración pantalla por pantalla con paralelo verificable y planificar el retiro del legacy con ventana de respaldo.

**Entradas:**
- Archivos fuente Forms (`.fmb`, `.pll`, `.mmb`, `.rdf`) y `frmf2xml` para convertirlos a XML.
- Acceso de lectura a `USER_OBJECTS`, `USER_DEPENDENCIES`, `USER_SOURCE`, `USER_TRIGGERS`, `USER_VIEWS`, `USER_PROCEDURES`.
- Matriz 10X de stack destino firmada (módulo 1.4.3).
- Inventario funcional declarado por las áreas de negocio.

**Pasos:**
1. Listar `.fmb`, `.pll`, `.mmb`, `.rdf` del filesystem y convertir los `.fmb` a XML.
2. Exportar paquetes, procedimientos, funciones, vistas, triggers y dependencias de la base a CSV.
3. Cruzar ambos inventarios; marcar discrepancias como preguntas abiertas para el cliente.
4. Clasificar cada trigger de Forms en las cinco categorías (validación / cálculo / paquete / navegación / UI) y asignar su destino.
5. Documentar el contrato de cada pantalla antes de migrarla y operar en paralelo durante el período acordado.
6. Reconciliar diariamente registros creados o modificados por ambos sistemas; cutover por pantalla cuando hay cero discrepancias estables.
7. Planificar el retiro: pantallas restantes, `.pll` huérfanas, objetos heredados al equipo DBA, ventana de cold storage de 30–90 días.

**Salidas:**
- Inventario de fuentes (`inventario-fuentes-forms.txt`) y de objetos de base (`inventario-objetos-bd.csv`).
- Triggers clasificados (`triggers-clasificados.md`).
- Backlog de migración por pantalla con contrato y criterio de cutover.
- Plan de retiro firmado con ventana de respaldo.

**Errores comunes:**
- Saltarse el inventario por confianza en la memoria del equipo.
- Inventariar un solo lado (filesystem o base) sin cruzarlo con el otro.
- Tratar los triggers como código a traducir línea por línea.
- Cutover big bang sin operación en paralelo.
- Cerrar el proyecto sin plan de retiro explícito.

**Referencias cruzadas:**
- [1.4.3 Migración desde Oracle Forms](./03-migracion-desde-oracle-forms.md)
- [1.4.5 Consultas parametrizadas en migraciones](./05-consultas-parametrizadas-en-migracion.md)
- [1.4.2 Migración progresiva (strangler fig)](./02-migracion-progresiva.md)

</div>

---

## Glosario

**`.fmb` / `.pll` / `.mmb` / `.rdf`** — formulario, biblioteca compartida de PL/SQL, menú y reporte de Oracle Forms/Reports respectivamente. El `.fmb` se convierte a XML con `frmf2xml` para procesamiento.

**`frmf2xml`** — utilitario de Oracle Forms Builder que convierte un `.fmb` binario a XML legible. Es la puerta de entrada al inventario automatizable.

**`WHEN-VALIDATE-ITEM`** *(trigger de Forms)* — punto de extensión donde se ejecuta PL/SQL al validar un campo. Sitio típico de reglas de negocio enterradas.

**`USER_DEPENDENCIES`** *(vista de diccionario)* — vista de Oracle que reporta qué objetos del esquema actual dependen de qué otros objetos. Pieza central del cruce de inventario.

**Inventario cruzado** — inventario que combina lo que existe en el filesystem (archivos fuente) con lo que existe en la base de datos (objetos), y reporta las discrepancias entre ambos lados como preguntas abiertas.

**Operación en paralelo** — período en que la pantalla nueva y la pantalla vieja están ambas operativas, con reconciliación diaria de registros. Es la red de seguridad que evita el cutover de fe.

**Cold storage** — almacenamiento en frío del código legacy retirado, por un período acordado (30–90 días), para permitir volver atrás si aparece una regla no detectada.

:::info Referencias primarias

- [Oracle · Forms-to-APEX migration](https://www.oracle.com/tools/apex-forms-modernization.html) — referencia oficial cuando el destino es APEX.
- [Oracle · Data Dictionary Views](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/static-data-dictionary-views.html) — referencia sobre `USER_OBJECTS`, `USER_DEPENDENCIES` y demás vistas usadas en el inventario.
- [1.4.3 Migración desde Oracle Forms](./03-migracion-desde-oracle-forms.md) — estrategia, matriz de stack destino y principios de coexistencia que este módulo aplica.
- [1.4.5 Consultas parametrizadas en migraciones](./05-consultas-parametrizadas-en-migracion.md) — práctica concreta que aplica a todo el código nuevo producido por la migración.

:::

---

<AuthorCredit />
