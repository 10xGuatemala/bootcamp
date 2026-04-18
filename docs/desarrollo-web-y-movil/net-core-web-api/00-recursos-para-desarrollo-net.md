---
sidebar_position: 0
sidebar_label: 1.2.0 Recursos para el Desarrollo de Web APIs en .NET
---

# Recursos para el Desarrollo de Web APIs en .NET

Este artículo curso recursos esenciales para instalar .NET, aprender a construir APIs Web utilizando ASP.NET Core y seguir convenciones de código para mantener la calidad y consistencia en tus proyectos. Incluye guías de instalación, herramientas y enlaces útiles para desarrolladores que buscan iniciarse o mejorar sus habilidades en .NET.

---

## Recursos de Instalación de .NET

Antes de comenzar a desarrollar, es fundamental tener instalado el framework .NET. A continuación, encontrarás recursos para descargar e instalar los componentes necesarios.

### ¿Qué es el SDK?

El SDK (Software Development Kit) incluye todo lo necesario para desarrollar aplicaciones en .NET. Contiene el compilador, las bibliotecas principales y el CLI (Interfaz de Línea de Comandos) de .NET, que te permite compilar y ejecutar aplicaciones.

- [Página de descarga del SDK de .NET](https://dotnet.microsoft.com/download/dotnet)

### ¿Qué es el Runtime?

El Runtime es el componente necesario para **ejecutar aplicaciones .NET** en entornos donde no se desarrollarán, como servidores de producción o máquinas cliente. Incluye únicamente lo esencial para ejecutar aplicaciones compiladas.

- [Página de descarga del Runtime de .NET](https://dotnet.microsoft.com/download/dotnet)

### Instrucciones de Instalación

#### Instalación en Windows

- [Guía oficial para instalar .NET en Windows](https://learn.microsoft.com/es-mx/dotnet/core/install/windows)

#### Instalación en Linux

- [Guía oficial para instalar .NET en Linux](https://learn.microsoft.com/es-mx/dotnet/core/install/linux)

#### Instalación en macOS

- [Guía oficial para instalar .NET en macOS](https://learn.microsoft.com/es-mx/dotnet/core/install/macos)

Sigue las instrucciones detalladas en el enlace anterior para instalar el SDK o Runtime de .NET en tu sistema macOS. La guía cubre métodos de instalación utilizando el paquete oficial o administradores de paquetes como Homebrew.

---

## Aprendizaje y Desarrollo de Web APIs

Para iniciar o reforzar tus conocimientos sobre la construcción de Web APIs en .NET, este curso interactivo es una excelente opción:

- [Construcción de una Web API con ASP.NET Core](https://learn.microsoft.com/es-mx/training/modules/build-web-api-aspnet-core/)  
  Este curso enseña los conceptos básicos de una API RESTful, incluyendo el uso de controladores, rutas, acciones y configuración básica.

---

## Convención de Código en .NET

Seguir las convenciones de código oficiales de .NET asegura la mantenibilidad, legibilidad y calidad de tus proyectos. Estas convenciones abarcan estilos de codificación, nomenclatura, organización y otros aspectos esenciales.

### Convenciones de Nomenclatura

Adoptar estas prácticas ayuda a mantener un código limpio y profesional:

1. **Uso de PascalCase y camelCase**:
   - Usa **PascalCase** para:
     - Nombres de clases, métodos, constantes y miembros públicos.
   - Usa **camelCase** para:
     - Variables locales y parámetros de métodos.

2. **Reglas para nombres específicos**:
   - Los nombres de **interfaz** comienzan con `I` (por ejemplo, `IService`).
   - Los tipos de **atributo** terminan con `Attribute` (por ejemplo, `DataAnnotationAttribute`).
   - Los tipos de **enumeración**:
     - Usan sustantivos **singulares** para los que no son marcas.
     - Usan sustantivos **plurales** para los que son marcas.

3. **Campos**:
   - Los campos **privados** comienzan con un guion bajo `_` y usan `camelCase` (por ejemplo, `_contador`).
   - Los campos **estáticos** comienzan con `s_` (por ejemplo, `s_cache`).

4. **Buenas prácticas generales**:
   - Usa nombres **descriptivos y con significado** en lugar de abreviaturas o acrónimos innecesarios.
   - Prefiere la **claridad** sobre la brevedad.
   - Evita nombres de una sola letra, excepto para contadores de bucles (por ejemplo, `i` o `j`).

5. **Espacios de nombres y ensamblados**:
   - Usa espacios de nombres **descriptivos y significativos**, siguiendo la notación inversa del dominio (por ejemplo, `Company.Product.Module`).
   - El nombre del ensamblado debe reflejar su propósito principal.

Consulta más detalles en la guía oficial:  

- [Convenciones de Código en .NET: Nombres de Identificadores](https://learn.microsoft.com/es-es/dotnet/csharp/fundamentals/coding-style/identifier-names)

## Glosario

**SDK** *(Software Development Kit)* — conjunto de herramientas para desarrollar aplicaciones en .NET; incluye compilador, bibliotecas base y CLI.

**Runtime** *(Runtime)* — componente mínimo necesario para ejecutar aplicaciones .NET ya compiladas en servidores o clientes.

**CLI de .NET** *(.NET CLI)* — interfaz de línea de comandos (`dotnet`) para crear, compilar, probar y publicar proyectos .NET.

**ASP.NET Core** *(ASP.NET Core)* — marco de trabajo de Microsoft para construir APIs web y aplicaciones HTTP multiplataforma.

**PascalCase** *(PascalCase)* — convención de nombres que capitaliza la primera letra de cada palabra; usada para clases, métodos y miembros públicos.

**camelCase** *(camelCase)* — convención de nombres con primera letra minúscula; usada para variables locales y parámetros.

**Espacio de nombres** *(Namespace)* — agrupación lógica de tipos; sigue la notación inversa del dominio (p. ej. `Company.Product.Module`).

:::info Referencias primarias
- [Microsoft · .NET docs](https://learn.microsoft.com/en-us/dotnet/) — referencia oficial del ecosistema .NET.
- [ASP.NET Core docs](https://learn.microsoft.com/en-us/aspnet/core/) — guías y referencia de ASP.NET Core.
- [Convenciones de código de C#](https://learn.microsoft.com/es-es/dotnet/csharp/fundamentals/coding-style/identifier-names) — estilo oficial de identificadores.
:::

---

<div className="agent-block">

### Bloque estructurado para agentes

**Objetivo:** preparar el entorno de desarrollo .NET y alinear las convenciones iniciales para construir Web APIs.

**Entradas:**
- Sistema operativo del desarrollador (Windows, Linux, macOS).
- Nivel de experiencia previa con .NET.
- Tipo de aplicación a construir (SDK vs Runtime).
- Convenciones de código de la organización.

**Pasos:**
1. Identificar si se requiere SDK o únicamente Runtime según el rol del entorno.
2. Instalar la versión adecuada usando la guía oficial o un gestor de paquetes.
3. Validar la instalación con la CLI de .NET (`dotnet --info`).
4. Completar la ruta de aprendizaje recomendada para Web API con ASP.NET Core.
5. Adoptar las convenciones de nomenclatura (PascalCase, camelCase, prefijos `I`, `_`, `s_`).
6. Aplicar buenas prácticas de espacios de nombres y nombres de ensamblado.

**Salidas:**
- Entorno de desarrollo listo para compilar y ejecutar APIs en .NET.
- Documento base de convenciones para el equipo.
- Referencias oficiales centralizadas para el onboarding.

**Errores comunes:**
- Instalar solo Runtime cuando se requiere desarrollar.
- Mezclar convenciones ad hoc con las oficiales de Microsoft.
- Abreviar nombres sin justificación, afectando la legibilidad.
- No validar la versión del SDK instalada antes de iniciar un proyecto.

**Referencias cruzadas:**
- [1.2.1 Arquitectura de Backend API Rest en .NET Core](./01-arquitectura-de-backend.md)
- [1.2.2 La Capa de Controlador en un Backend API REST](./02-capa-controlador.md)
- [1.1.1 Introducción a Web Service de tipo API REST](../capacitacion-servicios-web-api-rest/01-introduccion-a-servicio-web-rest.md)
</div>
