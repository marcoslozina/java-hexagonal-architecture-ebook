# Plantilla Profesional Java + Gradle

![Portada](./images/portada-gradle-pro.png)

> *Guía técnica moderna para crear una base sólida con Gradle, Spring Boot y Kotlin DSL.*

## Índice

1. [¿Por qué usar Gradle en proyectos profesionales?](#1-por-qué-usar-gradle-en-proyectos-profesionales)
2. [Diferencias entre Gradle y Maven](#2-diferencias-entre-gradle-y-maven)
3. [Configuración de entorno](#3-configuración-de-entorno)
4. [Estructura mínima de un proyecto Gradle moderno](#4-estructura-mínima-de-un-proyecto-gradle-moderno)
5. [Archivos clave de configuración](#5-archivos-clave-de-configuración)
6. [Uso de buildSrc y organización de dependencias](#6-uso-de-buildsrc-y-organización-de-dependencias)
7. [Gestión de dependencias con Spring Boot y BOMs](#7-gestión-de-dependencias-con-spring-boot-y-boms)
8. [Buenas prácticas y errores comunes](#8-buenas-prácticas-y-errores-comunes)
9. [Enlace a repositorio base](#9-enlace-a-repositorio-base)
10. [Próximo paso: Arquitectura Hexagonal](#10-próximo-paso-arquitectura-hexagonal)

## 1. ¿Por qué usar Gradle en proyectos profesionales?

Gradle es una herramienta moderna de automatización de builds que permite una mayor flexibilidad, velocidad y mantenibilidad frente a otras alternativas como Maven o Ant. Es ideal para entornos complejos, proyectos multimódulo y configuraciones personalizadas.

**Ventajas clave:**

* DSL en Kotlin o Groovy: DSL significa *Domain Specific Language*, un lenguaje diseñado para describir tareas específicas de forma declarativa. En el contexto de Gradle, se usa para definir configuraciones de build con una sintaxis más expresiva y concisa. Kotlin DSL se prefiere en proyectos modernos porque es tipado, ofrece autocompletado en IDEs como IntelliJ, reduce errores en tiempo de compilación y mantiene mayor consistencia en proyectos empresariales. *Domain Specific Language*, un lenguaje de configuración que permite describir comportamientos de manera concisa. Gradle permite usar DSL en Kotlin (moderno, tipado y más seguro) o en Groovy (más dinámico y tradicional).
* Soporte nativo para proyectos multimódulo
* Integración con herramientas modernas (CI/CD, linters, test frameworks)
* Incremental build: solo compila lo necesario

## 2. Diferencias entre Gradle y Maven

| Característica            | Gradle                             | Maven                      |
| ------------------------- | ---------------------------------- | -------------------------- |
| Lenguaje de configuración | Kotlin/Groovy (DSL flexible)       | XML (rigidez estructural)  |
| Velocidad de compilación  | Alta (build incremental y caching) | Lenta en proyectos grandes |
| Modularización            | Soporte avanzado                   | Limitado                   |
| Personalización           | Alta (hooks, tareas, plugins)      | Baja                       |

**Benchmark sugerido:**

> Gradle es hasta un 2-3x más rápido que Maven en builds incrementales complejos, gracias a su sistema de caché y ejecución incremental. [Fuente: Gradle Build Tool Performance](https://gradle.org/performance/)

> En pruebas independientes como las realizadas por [LinkedIn Engineering](https://engineering.linkedin.com/blog/2019/01/how-we-improved-our-gradle-build-times-by-over-40-percent) se observó una mejora significativa en tiempos de build al migrar de Maven a Gradle en proyectos grandes.

| Característica            | Gradle                             | Maven                      |
| ------------------------- | ---------------------------------- | -------------------------- |
| Lenguaje de configuración | Kotlin/Groovy (DSL flexible)       | XML (rigidez estructural)  |
| Velocidad de compilación  | Alta (build incremental y caching) | Lenta en proyectos grandes |
| Modularización            | Soporte avanzado                   | Limitado                   |
| Personalización           | Alta (hooks, tareas, plugins)      | Baja                       |

**Benchmark sugerido:**

> Gradle es hasta un 2-3x más rápido que Maven en builds incrementales complejos. [Fuente: Gradle Build Tool Performance](https://gradle.org/performance/)

## 3. Configuración de entorno

### Objetivo de esta sección

Antes de comenzar con la estructura del proyecto y el uso de Gradle, es importante preparar correctamente el entorno de desarrollo. Este setup te permitirá compilar, ejecutar y depurar proyectos Java modernos sin inconvenientes.

### Requisitos del entorno

* **JDK 23**: usaremos Java 23 como entorno base para el proyecto. Asegurate de que esté correctamente instalado y activo en tu sistema.
* **Gradle 8.7 o superior**: se requiere para trabajar con Kotlin DSL y configuración modular.
* **IDE recomendado**: IntelliJ IDEA (Community o Ultimate). Brinda soporte completo para Kotlin, Gradle y detección automática de scripts `.kts`.

### Instalación paso a paso (Linux/macOS)

```bash
# Descargar Gradle manualmente
wget https://services.gradle.org/distributions/gradle-8.7-bin.zip

# Extraer y mover a /opt
sudo unzip gradle-8.7-bin.zip -d /opt/gradle

# Exportar variable de entorno temporal
export PATH=$PATH:/opt/gradle/gradle-8.7/bin
```

Para que esté disponible permanentemente, agregalo a tu `~/.bashrc`, `~/.zshrc` o `~/.profile`:

```bash
export GRADLE_HOME=/opt/gradle/gradle-8.7
export PATH=$PATH:$GRADLE_HOME/bin
```

Haz lo mismo con Java si no está configurado:

```bash
export JAVA_HOME=/usr/lib/jvm/java-23
export PATH=$PATH:$JAVA_HOME/bin
```

Verificación:

```bash
java -version
# Resultado esperado: versión 23.x

source ~/.bashrc  # o ~/.zshrc o ~/.profile según corresponda

# Verificar que las variables estén cargadas
echo $JAVA_HOME
# Resultado esperado: /usr/lib/jvm/java-23

echo $GRADLE_HOME
# Resultado esperado: /opt/gradle/gradle-8.7

gradle -v
# Verifica que Gradle está accesible desde cualquier ubicación
```

> ⚠️ Es importante ejecutar `source ~/.bashrc` o su equivalente para recargar las variables de entorno sin reiniciar el sistema.

### Configuración en IntelliJ IDEA

#### Tabla de atajos y comandos útiles en IntelliJ para Gradle

| Acción                          | Atajo o ubicación en IntelliJ                                                                      |
| ------------------------------- | -------------------------------------------------------------------------------------------------- |
| Importar proyecto Gradle        | Al abrir: "Import Project" desde menú                                                              |
| Recargar configuración Gradle   | Botón "Refresh Gradle Project" en Gradle Tool Window o clic derecho en proyecto → "Reload project" |
| Ejecutar tareas Gradle          | Gradle Tool Window → doble clic en tarea                                                           |
| Ver tareas disponibles          | View → Tool Windows → Gradle                                                                       |
| Configurar JDK del proyecto     | File → Project Structure → Project SDK                                                             |
| Configurar variables de entorno | Run → Edit Configurations → Environment Variables                                                  |
| Ejecutar build completo         | `Ctrl + F9` o `Build → Rebuild Project`                                                            |

1. Abrí IntelliJ IDEA y seleccioná "Open" o "Import Project"
2. Asegurate de seleccionar la carpeta raíz del proyecto
3. IntelliJ detectará automáticamente `build.gradle.kts` y te pedirá importar el proyecto como Gradle
4. En **Project Structure**, verificá que esté usando:

    * JDK 23
    * Gradle desde sistema (no wrapper si usás instalación global)
    * Kotlin DSL (no Groovy)

Esto garantiza que IntelliJ interprete correctamente las configuraciones, dependencias y tareas Gradle.

### Comandos Gradle útiles desde terminal

| Comando                       | Descripción                                            |
| ----------------------------- | ------------------------------------------------------ |
| `./gradlew build`             | Compila el proyecto y ejecuta tests                    |
| `./gradlew clean`             | Elimina archivos generados por compilaciones previas   |
| `./gradlew test`              | Ejecuta únicamente los tests                           |
| `./gradlew dependencies`      | Muestra el árbol de dependencias del proyecto          |
| `./gradlew tasks`             | Lista todas las tareas disponibles                     |
| `./gradlew bootRun`           | Ejecuta una aplicación Spring Boot directamente        |
| `./gradlew dependencyUpdates` | Muestra nuevas versiones disponibles (requiere plugin) |

### Errores comunes

* ❌ **No configurar JAVA\_HOME o GRADLE\_HOME**: genera fallos al compilar desde terminal o dentro del IDE.
* ❌ **Múltiples versiones instaladas**: pueden interferir entre sí. Usá `which java` y `which gradle` para verificar la ruta activa.
* ❌ **No marcar la raíz del proyecto como raíz Gradle**: el IDE no podrá reconocer las tareas.
* ❌ **No sincronizar el proyecto Gradle**: ejecutá `Reload Gradle Project` desde IntelliJ tras cambios en dependencias.
* ❌ **Usar SDKMAN con versiones no estables de Java**: puede generar problemas con herramientas como Gradle si aún no están soportadas.

### Requisitos:

* JDK 23
* Gradle 8.7 o superior

Este proceso es prácticamente idéntico en sistemas Linux y macOS. Ambos utilizan shells compatibles y sistemas de archivos similares, por lo que los comandos funcionan igual. En Windows se recomienda utilizar Git Bash o WSL (Windows Subsystem for Linux) para una experiencia similar y evitar conflictos de entorno.

```bash
# Descargar Gradle
wget https://services.gradle.org/distributions/gradle-8.7-bin.zip

# Extraer y mover a /opt
sudo unzip gradle-8.7-bin.zip -d /opt/gradle

# Agregar a la variable PATH
export PATH=$PATH:/opt/gradle/gradle-8.7/bin
```

Verificá la instalación con:

```bash
gradle -v
```

### Errores comunes:

* ❌ Usar SDKMAN con JDK 23 puede dar errores si aún no está oficialmente soportado.
* ❌ Olvidar agregar Gradle al PATH generará errores como "command not found".
* ❌ Tener múltiples versiones de Gradle o Java instaladas y activas al mismo tiempo puede generar conflictos inesperados.

### Requisitos:

* JDK 23
* Gradle 8.7 o superior

Este proceso es igual para Linux y macOS. En Windows se recomienda usar Git Bash o WSL para evitar errores de entorno.

```bash
# Descargar Gradle
wget https://services.gradle.org/distributions/gradle-8.7-bin.zip

# Extraer y mover a /opt
sudo unzip gradle-8.7-bin.zip -d /opt/gradle

# Agregar a la variable PATH
export PATH=$PATH:/opt/gradle/gradle-8.7/bin
```

Verificá la instalación con:

```bash
gradle -v
```

**Errores comunes:**

* Usar SDKMAN con JDK 23 puede traer problemas si aún no está completamente soportado.

## 4. Estructura mínima de un proyecto Gradle moderno

```
java-hexagonal-architecture-ebook/
├── buildSrc/                  # dependencias centralizadas
├── settings.gradle.kts       # definición de módulos
├── build.gradle.kts          # configuración raíz
├── app/                      # módulo principal
│   └── build.gradle.kts
└── README.md
```

## 5. Archivos clave de configuración

Gradle organiza su configuración principal a través de archivos Kotlin Script (`.kts`) o Groovy (`.gradle`). Kotlin Script es una versión tipada, segura y moderna del DSL de Gradle, recomendada para proyectos nuevos. Es importante conocer las diferencias entre los archivos `.kt` y `.kts`:

### ¿Qué son `.kt` y `.kts`?

* `.kt` → Archivos de código fuente de Kotlin usados en clases, lógica de aplicación, o dentro de `buildSrc`
* `.kts` → Kotlin Script, usados para definir la configuración del proyecto Gradle, como `build.gradle.kts` o `settings.gradle.kts`

En resumen: `.kt` se usa para lógica de negocio o utilidades, mientras que `.kts` se utiliza exclusivamente para scripts de configuración Gradle.

### ¿Qué son `.kt` y `.kts`?

* `.kt` → archivo de código Kotlin
* `.kts` → archivo de script Kotlin Script, usado para configuración (equivalente a `.gradle` en Groovy)

### `settings.gradle.kts`

Define el nombre del proyecto raíz y los módulos que componen la solución:

```kotlin
rootProject.name = "java-hexagonal"
include("app")
```

### `build.gradle.kts` (raíz)

Configura plugins, variables comunes y metadatos del proyecto:

```kotlin
plugins {
    id("org.gradle.toolchains.foojay-resolver-convention") version "0.7.0"
}

allprojects {
    group = "com.miempresa"
    version = "1.0.0"
}
```

### `buildSrc/src/main/kotlin/Dependencies.kt`

Archivo para centralizar versiones de dependencias y simplificar su gestión en proyectos grandes.

## 6. Uso de buildSrc y organización de dependencias

`buildSrc/` permite centralizar versiones y dependencias para todos los módulos. Es una carpeta especial reconocida automáticamente por Gradle que permite mover lógica de build a código Kotlin reutilizable. Es especialmente útil en proyectos grandes con múltiples módulos, donde se desea mantener consistencia y evitar duplicación.

> 📚 Referencia oficial: [Gradle - Organizing Build Logic with buildSrc](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources) Esto hace que sea fácil de mantener y escalar.

> 📚 Referencia: [Gradle - Organizing Build Logic](https://docs.gradle.org/current/samples/sample_buildsrc_plugins.html)

### Ejemplo:

```kotlin
object Versions {
    const val springBoot = "3.2.1"
    const val junit = "5.10.0"
}

object Deps {
    const val junit = "org.junit.jupiter:junit-jupiter:${Versions.junit}"
}
```

Y luego en los módulos:

```kotlin
dependencies {
    testImplementation(Deps.junit)
}
```

**Errores comunes:**

* Usar cadenas de texto sueltas para dependencias en múltiples archivos
* No actualizar `buildSrc` al cambiar versiones

## 7. Gestión de dependencias con Spring Boot y BOMs

### Compatibilidad entre Spring Boot y BOMs

Cada versión de Spring Boot incluye un BOM oficial que define las versiones compatibles de sus módulos y dependencias transitivas. Esto garantiza la estabilidad de todas las bibliotecas del ecosistema.

> 📚 Referencia: [Spring Boot Dependency Versions](https://docs.spring.io/spring-boot/docs/current/reference/html/dependency-versions.html)

### Configuración mínima funcional (starter template)

A continuación te comparto una plantilla base funcional que podés copiar y pegar en tu `build.gradle.kts` para iniciar rápidamente un proyecto Spring Boot web:

```kotlin
plugins {
    id("org.springframework.boot") version "3.2.1"
    id("io.spring.dependency-management") version "1.1.4"
    kotlin("jvm") version "1.9.10"
    kotlin("plugin.spring") version "1.9.10"
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}

repositories {
    mavenCentral()
}
```

Esta configuración es suficiente para crear una aplicación básica REST en Kotlin usando Spring Boot. Combinado con un archivo `Application.kt`, el proyecto estará listo para compilar y ejecutarse con `./gradlew bootRun`.

```kotlin
// src/main/kotlin/com/tuempresa/Application.kt
package com.tuempresa

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class Application

fun main(args: Array<String>) {
    runApplication<Application>(*args)
}
```

```java
// src/main/java/com/tuempresa/Application.java
package com.tuempresa;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Spring Boot recomienda usar un **BOM (Bill of Materials)** para alinear versiones de todas sus dependencias y evitar conflictos. Además, Gradle permite gestionar distintos tipos de dependencias para adaptar el alcance y visibilidad del código compartido.

### ¿Qué es un BOM?

Un BOM define versiones coordinadas de dependencias. Gradle lo interpreta automáticamente cuando usamos el plugin `io.spring.dependency-management` o el plugin oficial de Spring Boot, permitiendo declarar dependencias sin versiones explícitas.

### Cómo funciona la jerarquía:

* La versión de Spring Boot define un BOM
* El BOM fija versiones de starters y bibliotecas comunes (Spring Web, Spring Security, etc.)
* Tu proyecto solo define qué dependencias usar, y hereda versiones del BOM

### Ejemplo:

```kotlin
plugins {
    id("org.springframework.boot") version "3.2.1"
    id("io.spring.dependency-management") version "1.1.4"
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
}
```

👉 No es necesario declarar versiones manualmente: el BOM las resuelve por vos.

### Tipos de dependencias:

| Tipo                 | Uso                                                           |
| -------------------- | ------------------------------------------------------------- |
| `implementation`     | Se usa internamente en el módulo; no se expone a consumidores |
| `api`                | Se expone a otros módulos que dependan de este                |
| `compileOnly`        | Solo necesaria para compilación; no se incluye en el runtime  |
| `runtimeOnly`        | Solo necesaria en tiempo de ejecución                         |
| `testImplementation` | Disponible solo durante el testeo                             |

> 📌 Recomendación: Usar `implementation` por defecto para encapsular dependencias y mantener el acoplamiento bajo entre módulos.

### Proceso profesional de actualización de dependencias

1. Consultar la versión estable de Spring Boot desde [start.spring.io](https://start.spring.io)
2. Revisar el changelog oficial de Spring para breaking changes
3. Actualizar `org.springframework.boot` y el plugin de dependency management
4. Ejecutar `./gradlew dependencyUpdates` con el plugin `com.github.ben-manes.versions` para detectar nuevas versiones
5. Verificar que los cambios no rompan tests ni comportamiento
6. Automatizar este flujo con GitHub Actions o RenovateBot si aplica

> 📘 Más info: [Spring Boot Gradle Plugin Reference](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/)

### Errores comunes:

* Usar versiones fijas sin BOM, lo que puede causar conflictos de clase (`ClassNotFoundException`, `NoSuchMethodError`)
* Mezclar dependencias internas (`org.springframework.*`) con versiones externas no alineadas
* Declarar `api` cuando no se requiere propagación, lo que aumenta el acoplamiento innecesario

Spring Boot recomienda usar un **BOM (Bill of Materials)** para alinear versiones de todas sus dependencias y evitar conflictos.

### ¿Qué es un BOM?

Un BOM define versiones coordinadas de dependencias. Gradle lo interpreta automáticamente cuando usamos el plugin `io.spring.dependency-management` o cuando configuramos correctamente el plugin de Spring Boot.

### Cómo funciona la jerarquía:

* La versión de Spring Boot define un BOM
* El BOM fija las versiones de dependencias comunes (Spring Web, Spring Security, etc.)
* Tu proyecto solo define qué dependencias usar, sin preocuparse por las versiones

### Ejemplo:

```kotlin
plugins {
    id("org.springframework.boot") version "3.2.1"
    id("io.spring.dependency-management") version "1.1.4"
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
}
```

👉 No es necesario declarar versiones manualmente: el BOM las gestiona por vos.

### Tipos de dependencias:

| Tipo               | Uso                                                        |
| ------------------ | ---------------------------------------------------------- |
| implementation     | Dependencias necesarias solo dentro del módulo actual      |
| api                | Expone la dependencia a otros módulos que usen este módulo |
| runtimeOnly        | Se requiere solo en tiempo de ejecución                    |
| compileOnly        | Solo para compilación, no en tiempo de ejecución           |
| testImplementation | Solo usada durante los tests                               |

### Proceso profesional de actualización

1. Verificar versión estable del BOM de Spring Boot en [https://start.spring.io](https://start.spring.io)
2. Actualizar la versión del plugin en `build.gradle.kts`
3. Ejecutar `./gradlew dependencies` para revisar árbol
4. Testear regresión automatizada
5. Aplicar nuevas dependencias o refactors necesarios

## 8. Buenas prácticas y errores comunes

### ✅ Buenas prácticas

* **Centralizar dependencias en `buildSrc`**: evita duplicación y facilita el mantenimiento. Si necesitas actualizar una versión, lo hacés en un solo lugar.
* **Separar configuración común con `allprojects {}` o `subprojects {}`**: mantiene consistencia entre módulos y evita errores de omisión.
* **Usar Kotlin DSL (`.kts`)**: proporciona tipado estático, autocompletado y validación de errores en tiempo de edición.
* **Evitar definir versiones directamente en `dependencies {}`** cuando se utiliza BOM: reduce el riesgo de conflictos y mejora la mantenibilidad.
* **Versionar los plugins de forma explícita en `plugins {}`**: mejora la reproducibilidad y previene cambios silenciosos de comportamiento entre builds.

### ❌ Errores comunes y por qué evitarlos

* **Usar SDKMAN con JDK 23 (inestable)**: algunas herramientas como Gradle pueden no estar completamente preparadas para manejar cambios recientes del JDK, lo que provoca fallos de compatibilidad.
* **No definir `JAVA_HOME` o `GRADLE_HOME` correctamente**: impide que herramientas como IntelliJ y Gradle funcionen correctamente desde terminal o IDE.
* **Usar `api` en vez de `implementation` sin necesidad**: expone innecesariamente dependencias a otros módulos, aumentando el acoplamiento y tiempo de compilación.
* **No sincronizar el proyecto Gradle al hacer cambios**: provoca errores como "dependency not found" aunque el código sea correcto.
* **Definir dependencias sin utilizar BOM**: genera conflictos de versiones y errores difíciles de depurar como `NoSuchMethodError` o `ClassCastException`.
* **No limpiar el proyecto (`./gradlew clean`) entre builds importantes**: puede arrastrar errores o comportamientos no reproducibles por residuos de compilación anteriores.

### ✅ Buenas prácticas:

* Usar `buildSrc` para centralizar configuración
* Separar configuración común en `subprojects {}` o `allprojects {}`
* Mantener scripts limpios y tipados (DSL en Kotlin)

### ❌ Errores comunes:

* Usar SDKMAN con Java 23 (inestable)
* No fijar versiones de plugins o dependencias
* No usar tareas personalizadas reutilizables

## 9. Enlace a repositorio base

Podés ver este proyecto funcionando en:
👉 [github.com/marcoslozina/java-hexagonal-architecture-ebook](https://github.com/marcoslozina/java-hexagonal-architecture-ebook)

## Recursos adicionales

* [Documentación oficial de Gradle](https://docs.gradle.org/current/userguide/)
* [Guía de Spring Boot con Kotlin](https://spring.io/guides/tutorials/spring-boot-kotlin/)
* [Spring Initializr - start.spring.io](https://start.spring.io/)
* [Guía oficial de Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html)

## 10. Próximo paso: Arquitectura Hexagonal

En el siguiente módulo profundizaremos en cómo separar las responsabilidades del sistema usando **Arquitectura Hexagonal**, creando un dominio aislado y desacoplado de la infraestructura.

---

📅 Versión: 1.0
🌐 Idioma: Español
📁 Módulo: 01-gradle-setup
