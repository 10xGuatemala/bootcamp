# Recursos para el Desarrollo de Web APIs en .NET

Este artículo curso recursos esenciales para instalar .NET, aprender a construir APIs Web utilizando ASP.NET Core y seguir convenciones de código para mantener la calidad y consistencia en tus proyectos. Incluye guías de instalación, herramientas y enlaces útiles para desarrolladores que buscan iniciarse o mejorar sus habilidades en .NET.

---

## 1. Recursos de Instalación de .NET

Antes de comenzar a desarrollar, es fundamental tener instalado el framework .NET. A continuación, encontrarás recursos para descargar e instalar los componentes necesarios.

### 1.1 ¿Qué es el SDK?

El SDK (Software Development Kit) incluye todo lo necesario para desarrollar aplicaciones en .NET. Contiene el compilador, las bibliotecas principales y el CLI (Interfaz de Línea de Comandos) de .NET, que te permite compilar y ejecutar aplicaciones.

- [Página de descarga del SDK de .NET](https://dotnet.microsoft.com/download/dotnet)

### 1.2 ¿Qué es el Runtime?

El Runtime es el componente necesario para **ejecutar aplicaciones .NET** en entornos donde no se desarrollarán, como servidores de producción o máquinas cliente. Incluye únicamente lo esencial para ejecutar aplicaciones compiladas.

- [Página de descarga del Runtime de .NET](https://dotnet.microsoft.com/download/dotnet)

### 1.3 Instrucciones de Instalación

#### Instalación en Windows

- [Guía oficial para instalar .NET en Windows](https://learn.microsoft.com/es-mx/dotnet/core/install/windows)

#### Instalación en Linux

- [Guía oficial para instalar .NET en Linux](https://learn.microsoft.com/es-mx/dotnet/core/install/linux)

#### Instalación en macOS

- [Guía oficial para instalar .NET en macOS](https://learn.microsoft.com/es-mx/dotnet/core/install/macos)

Sigue las instrucciones detalladas en el enlace anterior para instalar el SDK o Runtime de .NET en tu sistema macOS. La guía cubre métodos de instalación utilizando el paquete oficial o administradores de paquetes como Homebrew.

---

## 2. Aprendizaje y Desarrollo de Web APIs

Para iniciar o reforzar tus conocimientos sobre la construcción de Web APIs en .NET, este curso interactivo es una excelente opción:

- [Construcción de una Web API con ASP.NET Core](https://learn.microsoft.com/es-mx/training/modules/build-web-api-aspnet-core/)  
  Este curso enseña los conceptos básicos de una API RESTful, incluyendo el uso de controladores, rutas, acciones y configuración básica.

---

## 3. Convención de Código en .NET

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
