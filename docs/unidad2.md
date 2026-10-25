~~# Sprint 2. El viaje del código: compilación, máquinas virtuales y dependencias

---

## Semana 4. Código fuente, bytecode y JVM

---

### Día 7 - 2 sesiones

---

#### Primera sesión: teoría. La transformación del software: de las palabras humanas al bytecode universal

#### 1. Caso guía en AzaharTech
Son las tres de la tarde del lunes 5 de octubre de 2026. En la sede de **AzaharTech** en Castellón de la Plana, el equipo de desarrollo inicia la primera jornada del **Sprint 2**. 

Tras haber cerrado el primer sprint con las bases algorítmicas secuenciales y el taller digital con Git, **Laia Claramunt** conecta su equipo al proyector de la sala de reuniones. En la pantalla aparece un correo del responsable técnico del **IES El Caminàs**:

> *«Equipo, entramos en una nueva etapa del proyecto. El coordinador de informática del IES El Caminàs nos plantea una duda fundamental: en el centro educativo tienen servidores con Linux, ordenadores de gestión en conserjería con Windows y terminales portátiles variados. Nos preguntan si tendremos que programar tres versiones distintas de la aplicación para que funcione en cada uno de sus sistemas.*
>
> *La respuesta es un no rotundo, y la razón técnica reside en **cómo viaja y se transforma el código en Java**.*
>
> *En los proyectos que vuestros equipos están desarrollando de la bolsa de proyectos ocurre lo mismo: tanto si estáis con la **Aventura conversacional**, el **Motor de recomendación**, el **Simulador de físicas 2D** o la **Bóveda de contraseñas**, el software debe ser independiente de la máquina física.*
>
> *Hoy aprenderemos a diferenciar con precisión el **código fuente**, el **código objeto (bytecode)** y el **código máquina ejecutable**. Descubriremos qué hace realmente el compilador cuando pulsamos un botón y miraremos por primera vez dentro de un archivo compilado»*.

---

#### 2. La cadena de transformación del código
Para que una orden escrita en lenguaje humano llegue a mover los circuitos electrónicos de un procesador, el software atraviesa tres estados claramente diferenciados:

```
┌─────────────────────────┬─────────────────────────┬─────────────────────────┐
│ 1. Código fuente        │ 2. Código objeto        │ 3. Código ejecutable    │
│    (Source Code)        │    (Bytecode intermedio)│    (Machine Code)       │
├─────────────────────────┼─────────────────────────┼─────────────────────────┤
│ Texto plano en inglés   │ Instrucciones binarias  │ Instrucciones binarias  │
│ comprensible por el     │ optimizadas para una    │ nativas ejecutadas por  │
│ desarrollador (.java).  │ máquina virtual (.class)│ la CPU física (0 y 1).  │
└────────────┬────────────┴────────────┬────────────┴────────────┬────────────┘
│                         │                         │
│   Compilación (javac)   │   Interpretación / JVM  │
└────────────────────────►│────────────────────────►│
```

##### A. Código fuente (*Source code*)
Es el archivo de texto editable que escribe el programador en el IDE (`ControlAccesoQR.java` o `MiProyecto.java`). Contiene palabras reservadas (`public`, `class`, `int`, `double`), identificadores y comentarios. La CPU no puede ejecutar este archivo directamente porque no entiende texto plano.

##### B. Código objeto intermedio (*Bytecode*)
Es el resultado de someter el código fuente al proceso de compilación mediante la herramienta `javac`.
* No es código máquina específico de Intel, AMD o ARM.
* Es un conjunto de instrucciones binarias compactas diseñadas para un procesador abstracto e ideal: la **Máquina Virtual de Java**.
* Se almacena en archivos con extensión **`.class`**.

##### C. Código ejecutable (*Machine code*)
Es el código binario final formado por ceros y unos (`0` y `1`) que la arquitectura física de una CPU concreta (x86_64, ARM) sabe descodificar en sus registros electrónicos.

---

#### 3. Tecnologías de virtualización: ¿por qué Java no compila a binario directo?
Los lenguajes tradicionales como C o C++ compilan el código fuente directamente a código máquina nativo del sistema operativo donde se compila.

```text
MODELO TRADICIONAL (C / C++):
[ Código C ] ──(Compilador Windows)──► [ ejecutable.exe (Solo funciona en Windows) ]
[ Código C ] ──(Compilador Linux)────► [ binario_elf   (Solo funciona en Linux)   ]

MODELO DE VIRTUALIZACIÓN (Java):
[ Código Java (.java) ] ──(javac)──► [ Bytecode (.class) ] ──(Universal)
                                              │
                    ┌─────────────────────────┼─────────────────────────┐
                    ▼                         ▼                         ▼
          [ JVM para Windows ]       [ JVM para Linux ]        [ JVM para macOS ]
```

* **El problema del modelo tradicional.** Para que el IES El Caminàs pudiera usar el software en distintos equipos, habría que mantener compiladores distintos y adaptar llamadas al sistema operativo en cada plataforma.
* **La solución de la virtualización (JVM).** Java introduce la **Máquina Virtual de Java (Java Virtual Machine - JVM)** como capa intermedia de software:
    1. El desarrollador compila una sola vez y genera el archivo **`.class`**.
    2. Ese mismo archivo `.class` viaja a cualquier ordenador del mundo.
    3. La JVM instalada en el ordenador del cliente lee el Bytecode y lo traduce en tiempo real a las instrucciones de su CPU local.
    4. Así se cumple el principio fundamental: **«Write Once, Run Anywhere»** (Escribe una vez, ejecuta en cualquier parte).

---

#### 4. Laboratorio práctico guiado. Inspección de las tripas de un archivo `.class`
Vas a comprobar con tus propios ojos la diferencia física entre código fuente y Bytecode:  

1. En el explorador de archivos o en IntelliJ IDEA, localizad la carpeta donde el entorno guarda los archivos compilados:
   `out/production/Proyecto/`
2. Abrid con un visor de texto plano el archivo **`ControlAccesoQR.class`**:
    * ¿Qué se visualiza? Veréis caracteres extraños, símbolos binarios y, en las cuatro primeras posiciones hexadecimales, la firma mágica histórica con la que comienzan todos los archivos de Java en el mundo: **`0xCAFEBABE`**.
3. **Uso del desensamblador del JDK (`javap`):**
    * El JDK incluye una herramienta oficial para traducir el Bytecode a mnemónicos legibles por los informaticos.
    * Abrid la terminal integrada de IntelliJ (`Alt + F12`) y ejecutad:
      ```bash
      javap -c out/production/*/ControlAccesoQR.class
      ```
    * Observad la salida en pantalla: veréis las instrucciones internas reales de la máquina virtual de Java que generó vuestro código (mnemónicos como `iload`, `bipush`, `imul`, `invokevirtual`).
    * *Pregunta para el grupo:* ¿Identificáis las operaciones de multiplicación (`imul`) y el número 60 (`bipush 60`) que programamos la semana pasada para el cálculo de estancia? El código que escribimos en Java se ha convertido en esas órdenes exactas.

---

#### Sesion 2. Dojo de entrenamiento y katas de herramientas

Pasamos al dojo de entrenamiento de herramientas, con tres **katas de compilación, ejecución y análisis de máquina virtual** para entrenar la independencia del software respecto al entorno de desarrollo, el desacoplamiento de binarios y la inspección de bajo nivel de la JVM:

---

##### Kata 1 (Cinturón blanco / Nivel base). Compilación y ejecución multi-entorno de tu proyecto propio
* **Objetivo.** Demostrar empíricamente que el código fuente de tu proyecto no depende de IntelliJ IDEA y es capaz de compilarse y ejecutarse limpiamente desde la consola del sistema operativo utilizando únicamente las herramientas del JDK (`javac` y `java`).
* **Aplicación según tu proyecto elegido:**
    * En **Aventura conversacional**. Compilar manualmente `MiProyecto.java` desde la terminal en `pr/src/`, verificar la generación del archivo `MiProyecto.class`, ejecutarlo comprobando la captura de datos del personaje y limpiar después el archivo binario residual generado en `src/`.
    * En **Motor de recomendación**. Compilar manualmente `MiProyecto.java` desde la terminal en `pr/src/`, verificar que la JVM ejecuta el cálculo de afinidad y comprobar que la salida por consola es idéntica a la obtenida dentro del IDE.
    * En **Simulador de físicas 2D**. Compilar manualmente `MiProyecto.java` desde la terminal en `pr/src/`, ejecutar la simulación cinemática introduciendo valores de prueba y certificar la independencia del entorno de ejecución.
    * En **Bóveda de contraseñas**. Compilar manualmente `MiProyecto.java` desde la terminal en `pr/src/`, verificar que la máquina virtual ejecuta la captura de credenciales y purgar el archivo `.class` del directorio de código fuente para mantener la higiene de Git.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Compilación desacoplada con directorio de destino (`-d`) y gestión de Classpath (`-cp`)
* **Contexto técnico.** En proyectos profesionales nunca se compilan los archivos `.class` mezclados dentro de la misma carpeta donde reside el código fuente (`src/`), ya que contamina el repositorio y dificulta el empaquetado.
* **Misión de la kata:**
    1. En la raíz de tu proyecto, crea una carpeta llamada `bin/` destinada exclusivamente a almacenar binarios:
       ```bash
       mkdir -p bin
       ```
    2. Compila tu archivo fuente utilizando la bandera **`-d`** (*destination*) de `javac` para indicarle al compilador que deposite el archivo `.class` dentro de `bin/` sin tocar `pr/src/`:
       ```bash
       javac -d bin pr/src/MiProyecto.java
       ```
    3. Comprueba mediante `ls -la bin/` que el archivo `MiProyecto.class` se ha creado dentro de `bin/` y que `pr/src/` permanece completamente limpio.
    4. Intenta ejecutar `java MiProyecto` desde la raíz. Comprobarás que la JVM emite un error de tipo `ClassNotFoundException`.
    5. Soluciona el error utilizando el parámetro **`-cp`** (*Classpath*), indicándole a la máquina virtual dónde debe buscar los archivos compilados:
       ```bash
       java -cp bin MiProyecto
       ```
    6. Redacta dos líneas de comentario en tu libreta técnica explicando por qué ocurre esto: **la JVM necesita que el parámetro Classpath le indique la ruta exacta del sistema de archivos donde residen los paquetes y clases compiladas**.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Telemetría de la JVM con `-verbose:class` e ingeniería inversa de memoria
* **Contexto de arquitectura.** Comprender cómo la Máquina Virtual de Java gestiona el ciclo de vida de carga de clases en memoria RAM y analizar la tabla de constantes del Bytecode mediante herramientas del JDK.
* **Misión de la kata:**
    1. Ejecuta tu programa desde la terminal activando la bandera de telemetría detallada del cargador de clases (*ClassLoader*):
       ```bash
       java -cp bin -verbose:class MiProyecto
       ```
    2. Observa la avalancha de información en la consola antes de que aparezca tu primera línea de salida. Cuenta aproximadamente cuántas clases del sistema operativo y del núcleo de Java carga la JVM en la memoria RAM antes de llegar a ejecutar tu método `main` (descubrirás que carga más de 400 clases base).
    3. Ejecuta el desensamblador oficial del JDK en modo exhaustivo (*verbose*):
       ```bash
       javap -v bin/MiProyecto.class
       ```
    4. Localiza en la salida el bloque **`Constant Pool` (Piscina de constantes)**. Identifica en qué líneas ha guardado Java los textos literales y constantes `final` que definiste en tu proyecto.
    5. Anota en tu cuaderno qué valores tienen los campos `stack` (tamaño máximo de la pila de operandos de tu método `main`) y `locals` (número de ranuras de memoria reservadas para las variables locales).
    6. Realiza una prueba de estrés de memoria simulando un entorno embebido ultrarrestringido, limitando el montículo (*heap*) de la JVM a solo 8 megabytes mediante la bandera de configuración de memoria:
       ```bash
       java -cp bin -Xmx8m MiProyecto
       ```

---

### Día 8 - 1 sesión

---

#### Teoría. Comparación de entornos de desarrollo
#### 1. Caso guía en AzaharTech
Es viernes por la tarde en la sede de **AzaharTech**. En la pantalla de la sala de reuniones, **Laia Claramunt** proyecta una consulta técnica remitida por el equipo de soporte del **IES El Caminàs**:

> *«Equipo, el coordinador de informática del centro nos traslada una situación real: en las aulas de informática disponen de equipos con IntelliJ IDEA, pero en los ordenadores portátiles prefieren utilizar la herramienta  **Visual Studio Code**.*
>
> *Nos preguntan si abrir el proyecto con otro entorno de desarrollo diferente puede corromper el software o provocar incompatibilidades en el código.*
>
> *Hoy comprobaremos de forma práctica que un código bien estructurado no pertenece a un fabricante de software concreto. En vuestros proyectos de la bolsa (**Aventura conversacional**, **Motor de recomendación**, **Simulador de físicas 2D** o **Bóveda de contraseñas**) el núcleo de Java debe comportarse con absoluta fidelidad tanto si lo editamos en un entorno libre como en un entorno propietario»*.

---

#### 2. El ecosistema de herramientas de desarrollo
* **El IDE completo frente al editor modular extensible:**
  * *IntelliJ IDEA Community.* Entorno de desarrollo integrado completo y libre diseñado específicamente para la máquina virtual de Java. Incluye de serie compilador, depurador, analizador de código y soporte nativo sin necesidad de plugins externos.
  * *Visual Studio Code.* Editor de texto plano ligero y modular. Para trabajar con Java requiere instalar extensiones que se comuniquen con el JDK mediante el protocolo estándar de servidores de lenguaje (*Language Server Protocol - LSP*).
* **Software libre frente a software con telemetría/propietario:**
  * IntelliJ IDEA Community cuenta con licencia de código abierto (*Apache 2.0*).
  * VS Code utiliza una base libre (*VSCodium*), pero el instalador oficial de Microsoft incluye componentes y telemetría bajo licencia privativa.
* **Independencia del código fuente:**
  * Ambos entornos utilizan el mismo compilador subyacente (`javac`) y la misma máquina virtual (`java`) instalados en el sistema operativo. El código no cambia; lo que cambia es la ergonomía y el consumo de recursos de la herramienta.

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   COMPARATIVA DE ARQUITECTURA DE ENTORNOS              │
├───────────────────────────────┬────────────────────────────────────────┤
│ IntelliJ IDEA Community       │ Visual Studio Code                     │
├───────────────────────────────┼────────────────────────────────────────┤
│ • Todas las funciones nativas │ • Requiere Extension Pack for Java     │
│ • Licencia libre Apache 2.0   │ • Binario oficial con telemetría       │
└───────────────────────────────┴────────────────────────────────────────┘
```

---

#### Dojo de entrenamiento y katas de herramientas

Pasamos al dojo de entrenamiento de herramientas, con tres **katas de ejecución multi-entorno, análisis de rendimiento y compatibilidad de versiones** para entrenar la independencia técnica del software y la evaluación comparativa de herramientas:

---

##### Kata 1 (Cinturón blanco / Nivel base). Ejecución de tu proyecto propio en un segundo entorno (VS Code)
* **Objetivo.** Abrir, compilar y ejecutar el archivo maestro de tu proyecto propio en **Visual Studio Code** (o en un editor modular secundario con el paquete de extensiones de Java), demostrando que la aplicación produce exactamente los mismos resultados en consola que en IntelliJ IDEA.
* **Aplicación según tu proyecto elegido:**
    * En **Aventura conversacional**. Abrir la carpeta del proyecto en VS Code, comprobar que el editor reconoce el método `main`, ejecutar la captura de datos del personaje y verificar que los textos con caracteres especiales no se desconfiguran.
    * En **Motor de recomendación**. Ejecutar el cálculo secuencial de afinidad dentro del panel de terminal de VS Code y certificar que la salida en pantalla coincide al céntimo con la obtenida en IntelliJ.
    * En **Simulador de físicas 2D**. Comprobar la compilación de las variables cinemáticas en el entorno secundario y validar que el compilador no genera advertencias sintácticas.
    * En **Bóveda de contraseñas**. Verificar la captura interactiva de datos de credenciales desde la consola integrada del nuevo entorno y confirmar que el código fuente permanece 100 % inalterado.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Auditoría de consumo de recursos y huella en memoria RAM
* **Contexto técnico.** Un desarrollador debe saber qué impacto tienen sus herramientas sobre el hardware del cliente para elegir el entorno idóneo según los recursos disponibles.
* **Misión de la kata:**
    1. Con ambos entornos abiertos en tu equipo (IntelliJ IDEA y VS Code con el mismo proyecto cargado), abre el monitor del sistema operativo:
        * En Windows: *Administrador de tareas* (`Ctrl + Shift + Esc`).
        * En Linux: *Monitor del sistema* o comando `top` / `htop` en la terminal.
    2. Localiza los procesos correspondientes a cada herramienta y anota los siguientes datos:
        * Memoria RAM consumida por IntelliJ IDEA en reposo (habitualmente entre 800 MB y 1,5 GB).
        * Memoria RAM consumida por VS Code con sus extensiones activas (habitualmente entre 300 MB y 600 MB).
    3. Redacta dos líneas de conclusión técnica en tu libreta: **¿en qué escenarios reales del IES El Caminàs recomendarías instalar IntelliJ IDEA y en cuáles sería técnicamente más viable desplegar VS Code?**

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Diagnóstico del error `UnsupportedClassVersionError`
* **Contexto de arquitectura.** Comprender qué ocurre cuando un archivo `.class` compilado con una versión moderna de Java intenta ejecutarse en una máquina virtual antigua.
* **Misión de la kata:**
    1. Cada versión del JDK asigna un número de versión mayor al Bytecode que genera (Java 8 = versión 52, Java 17 = versión 61, Java 21 = versión 65).
    2. Ejecuta el desensamblador oficial del JDK para averiguar el número de versión interna de tu archivo compilado:
       ```bash
       javap -v out/production/*/ControlAccesoQR.class | grep "major version"
       ```
       *(En Windows PowerShell: `javap -v out/production/Proyecto/ControlAccesoQR.class | Select-String "major version"`).*
    3. Comprueba que la salida muestra: `major version: 65` (correspondiente a Java 21).
    4. Deduce y anota en tu cuaderno técnico: **¿qué error emitirá la JVM si intentamos ejecutar este archivo en un servidor del instituto que tenga instalado únicamente Java 17?** (Anota el nombre de la excepción: `java.lang.UnsupportedClassVersionError`).

---

## Semana 5. Construcción y proyectos con Maven

---

### Día 9 - 2 sesiones

---

#### Sesion 1. Teoría. El problema de la escala y el principio de convención sobre configuración

#### 1. Caso guía en AzaharTech
Son las tres de la tarde del lunes 12 de octubre de 2026. En la sede de **AzaharTech**, el equipo de desarrollo inicia la segunda semana del Sprint 2.

**Laia Claramunt** proyecta en la pantalla el estado actual del repositorio del **IES El Caminàs**:
> *«La semana pasada aprendimos a compilar manualmente con `javac` y entendimos el papel de la máquina virtual. Sin embargo, en el mundo real una aplicación no se queda en un archivo único de 50 líneas. El sistema de acceso por QR va a incorporar paquetes con controladores de red, clases de validación de tokens y módulos de almacenamiento.*
>
> *En los proyectos de vuestros equipos ocurre lo mismo: **Aventura conversacional** necesitará estructurar escenas y personajes, **Motor de recomendación** dividirá su lógica de cálculo de afinidad de la gestión de ítems, **Simulador de físicas 2D** requerirá separar las fórmulas cinemáticas del motor gráfico, y **Bóveda de contraseñas** separará el evaluador de entropía del cifrado.*
>
> *Si tuviéramos que compilar veinte clases a mano con la terminal indicando rutas una por una, pasaríamos más tiempo gestionando comandos que programando. Para solucionar esto, la industria utiliza **herramientas de automatización de la construcción (*build tools*)**. Hoy conoceremos el estándar mundial del ecosistema Java: **Apache Maven**»*.

---

#### 2. El porqué de Maven y la arquitectura estándar

```
┌────────────────────────────────────────────────────────────────────────┐
│                        ¿QUÉ RESUELVE APACHE MAVEN?                     │
├──────────────────────────┬─────────────────────────────────────────────┤
│ Problema tradicional     │ Solución estandarizada con Maven            │
├──────────────────────────┼─────────────────────────────────────────────┤
│ Cada desarrollador crea  │ Convención sobre configuración:             │
│ carpetas con nombres     │ Estructura universal idéntica en cualquier  │
│ distintos (bin, code...) │ empresa del mundo (src/main/java).          │
├──────────────────────────┼─────────────────────────────────────────────┤
│ Compilación manual       │ Ciclo de vida automatizado:                 │
│ propensa a errores con   │ Fases estándar para limpiar y compilar con  │
│ comandos interminables.  │ un solo clic (clean, compile).              │
└──────────────────────────┴─────────────────────────────────────────────┘
```

##### A. El principio de «Convención sobre configuración» (*Convention over Configuration*)
Maven asume que todos los proyectos Java profesionales deben compartir la misma estructura física. Si el desarrollador respeta esa convención, no necesita escribir ningún script para explicarle al compilador dónde buscar el código:

```text
proyecto-maven/
├── pom.xml                     <-- Archivo descriptor del proyecto
└── src/
    └── main/
        ├── java/               <-- Código fuente Java oficial (.java)
        └── resources/          <-- Archivos de configuración, textos e imágenes
```

##### B. El archivo descriptor `pom.xml` y las coordenadas GAV
El corazón de un proyecto Maven es el archivo **`pom.xml`** (*Project Object Model*). Es un archivo en formato XML que define la identidad del software mediante tres coordenadas universales (**GAV**):

* **`groupId`.** Identificador de la organización o empresa, habitualmente en formato de dominio web invertido (*por ejemplo, `com.azahartech`*).  
* **`artifactId`.** Nombre del proyecto o módulo concreto en minúsculas y separado por guiones (*por ejemplo, `control-acceso-caminas`*).  
* **`version`.** Versión del desarrollo (*por ejemplo, `0.2.0-SNAPSHOT` para versiones en desarrollo*).  

---

#### 3. Actividad activa interactiva: Análisis y diseño del `pom.xml`
*Los estudiantes analizan la sintaxis XML y definen en su libreta las coordenadas oficiales de su proyecto propio:*

1. **Lectura de la plantilla mínima de Maven:**
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
            http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>

       <groupId>com.azahartech.proyectos</groupId>
       <artifactId>nombre-de-tu-proyecto</artifactId>
       <version>0.2.0-SNAPSHOT</version>

       <properties>
           <maven.compiler.source>21</maven.compiler.source>
           <maven.compiler.target>21</maven.compiler.target>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       </properties>
   </project>
   ```
2. **Diseño de coordenadas para los cuatro proyectos:**
    * Cada estudiante anota en su libreta el bloque `<groupId>` y `<artifactId>` correspondiente a su reto de la bolsa.
    * *Pregunta de análisis:* ¿Por qué especificamos `<maven.compiler.source>21</maven.compiler.source>`? Para garantizar que Maven rechace compilar si alguien utiliza una versión del JDK obsoleta en su máquina.

---

#### 4. Puesta a punto de proyectos con estructura de directorios de Maven modificada
La estructura de directorios de los proyectos realizados por los equipos es diferente de la estructura básica de Maven. 

Para que Maven pueda reconocer cada proyecto como propio, se debe añadir al pom.xml las siguientes líneas (dentro de la etiqueta project):

```xml
    <build>
        <sourceDirectory>pr/src/main/java</sourceDirectory>
        <resources>
            <resource>
                <directory>pr/src/main/resources</directory>
            </resource>
        </resources>
    </build>
```

---

#### Sesion 2. Dojo de entrenamiento y katas de herramientas
Pasamos al dojo de entrenamiento de herramientas, con tres **katas de construcción, estandarización de directorios y ciclo de compilación con Maven** para entrenar la transición del proyecto básico a la arquitectura profesional:

---

##### Kata 1 (Cinturón blanco / Nivel base). Inicialización del proyecto Maven y migración del código fuente
* **Objetivo.** Crear el proyecto gestionado por Maven en IntelliJ IDEA dentro de tu espacio de trabajo oficial, configurar el archivo `pom.xml` mínimo para OpenJDK 21 y migrar la clase única de tu proyecto propio a la ruta estándar `src/main/java/`.
* **Aplicación según tu proyecto elegido:**
    * En **Aventura conversacional**. Crear el proyecto Maven con el artifactId `aventura-conversacional`, ubicar `MiProyecto.java` en `src/main/java/` y verificar que el editor reconoce la nueva estructura estándar de carpetas.
    * En **Motor de recomendación**. Configurar las propiedades de compilación para Java 21 en el `pom.xml`, mover el código a `src/main/java/` y sincronizar los cambios de Maven mediante el atajo `Ctrl + Shift + O`.
    * En **Simulador de físicas 2D**. Inicializar el proyecto con Maven, estructurar la carpeta de recursos vacía `src/main/resources/` para futuras tablas de datos cinemáticos y comprobar que el código fuente compila sin errores.
    * En **Bóveda de contraseñas**. Migrar el código del proyecto propio a la carpeta `src/main/java/` y verificar en el árbol de proyectos que IntelliJ asigna el icono de paquete azul oficial a la carpeta `java`.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). El ciclo de vida de Maven: ejecución visual de las fases `clean` y `compile`
* **Contexto técnico.** En proyectos profesionales no se compila invocando a `javac` manualmente; se utiliza el ciclo de vida de construcción estándar gestionado por el entorno.
* **Misión de la kata:**
    1. En el margen derecho de IntelliJ IDEA, despliega la ventana lateral **Maven** (icono vertical de la 'm').
    2. Localiza la sección **Lifecycle** del proyecto:
        * Haz doble clic sobre la fase **`compile`**.
        * Observa en la consola inferior cómo Maven ejecuta automáticamente el compilador y comprueba en el árbol de proyectos que se ha generado la carpeta **`target/classes/`** conteniendo el archivo compilado `.class`.
    3. Haz doble clic sobre la fase **`clean`**:
        * Comprueba en el árbol de proyectos cómo Maven elimina físicamente toda la carpeta `target/` de forma instantánea.
    4. Ejecuta de forma combinada **`clean`** y a continuación **`compile`**.
    5. Redacta dos líneas de conclusión en tu libreta técnica: **¿por qué es una buena práctica ejecutar la fase `clean` antes de una compilación importante?** (Para asegurar que no quedan archivos binarios residuales de clases eliminadas o renombradas).

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Auditoría de dependencias internas y directorio local `.m2`
* **Contexto de arquitectura.** Comprender cómo Maven descarga y almacena en el disco duro del sistema operativo los plugins necesarios para compilar el código.
* **Misión de la kata:**
    1. Abre la terminal integrada de IntelliJ (`Alt + F12`) en la raíz donde reside el archivo `pom.xml`.
    2. Ejecuta una compilación forzando el modo de diagnóstico detallado (*debug*):
       ```bash
       mvn compile -X
       ```
       *(En caso de no tener el comando global configurado, utiliza el menú contextual de Maven en IntelliJ: botón derecho sobre `compile` -> Run Maven Build with Debugging).*
    3. Observa la salida técnica de la consola: identifica las líneas donde Maven invoca al plugin oficial de compilación: `maven-compiler-plugin`.
    4. Localiza en tu sistema operativo la carpeta oculta donde Maven guarda en caché todos los componentes descargados:
        * En Windows: `C:\Users\tu-usuario\.m2\repository\`
        * En Linux: `/home/tu-usuario/.m2/repository/`
    5. Abre esa carpeta y comprueba cómo Maven almacena los plugins ordenados por carpetas según su `groupId`. Anota en tu cuaderno técnico: **¿por qué esta caché local evita tener que descargar las herramientas cada vez que creas un proyecto nuevo?**

---

### Día 10 - 1 sesión

---

#### Teoria. Recursos del proyecto y búsqueda en Maven Central

#### 1. Caso guía en AzaharTech
Es viernes por la tarde en **AzaharTech**. En la pantalla de pruebas, **Pau Ferrer** muestra la estructura del proyecto Maven que el equipo configuró el lunes pasado. La compilación de clases en `src/main/java/` funciona sin fallos, pero **Alba Torres** plantea un nuevo requerimiento del cliente:

> *«El terminal del **IES El Caminàs** no solo va a ejecutar código Java puro. Necesitamos incluir archivos no ejecutables: un archivo de texto con el mensaje legal de bienvenida para el vestíbulo, los datos de configuración del centro y el logotipo institucional que se mostrará en pantalla.*
>
> *¿Dónde se guardan estos archivos dentro de la estructura estándar de Maven para que no se pierdan al compilar? Y lo que es más importante: la semana que viene añadiremos librerías externas para generar códigos QR reales y procesar datos en vuestros proyectos (**Aventura conversacional**, **Motor de recomendación**, **Simulador de físicas 2D** y **Bóveda de contraseñas**). ¿De dónde saca Maven esas librerías?*
>
> *Hoy aprenderemos a gestionar la carpeta **`src/main/resources/`** y descubriremos el gran almacén mundial de software del que se nutre nuestra herramienta: el repositorio **Maven Central**»*.

---

#### 2. Recursos del proyecto y el ecosistema Maven Central

```
┌────────────────────────────────────────────────────────────────────────┐
│                   EL VIAJE DE LOS RECURSOS EN MAVEN                    │
├───────────────────────────────────┬────────────────────────────────────┤
│ 1. Carpeta de origen (desarrollo) │ 2. Carpeta de destino (compilado)  │
├───────────────────────────────────┼────────────────────────────────────┤
│ src/main/resources/               │ target/classes/                    │
│   ├── config.properties           │   ├── config.properties            │
│   └── banner.txt                  │   └── banner.txt                   │
├───────────────────────────────────┴────────────────────────────────────┤
│ Maven copia automáticamente los recursos junto al Bytecode (.class)   │
│ para que la JVM pueda encontrarlos en tiempo de ejecución.             │
└────────────────────────────────────────────────────────────────────────┘
```

##### A. La carpeta estándar `src/main/resources/`
En el desarrollo profesional, los archivos que no son código Java (imágenes, audios, archivos de texto, plantillas o configuraciones) jamás se mezclan con las clases dentro de `src/main/java/`:

* Se almacenan dentro del directorio reservado **`src/main/resources/`**.  
* Cuando ejecutas la fase **`compile`** en Maven, la herramienta invoca automáticamente un plugin interno (`maven-resources-plugin`) que toma todo el contenido de `resources/` y lo copia de forma transparente dentro de **`target/classes/`**.  
* De este modo, los recursos quedan empaquetados en la misma raíz que las clases y la JVM puede leerlos desde el *Classpath* sin importar en qué sistema operativo se ejecute la aplicación.  

##### B. El almacén mundial de software: Maven Central
¿Cómo sabe Maven de dónde descargar una librería cuando la necesitamos?

* **Maven Central (`repo.maven.apache.org`).** Es el repositorio público oficial en la nube mantenido por la comunidad de software donde empresas y desarrolladores de todo el mundo publican sus librerías de Java certificadas.  
* Para localizar cualquier librería y conocer sus coordenadas exactas (**GAV**: `groupId`, `artifactId`, `version`), los desarrolladores consultamos el portal indexador oficial: **[mvnrepository.com](https://mvnrepository.com)**.  

---

#### Dojo de entrenamiento y katas de herramientas

Pasamos al dojo de entrenamiento de herramientas, con tres **katas de gestión de recursos, exploración en Maven Central y filtrado de propiedades** para entrenar la arquitectura de proyectos industriales:

##### Kata 1 (Cinturón blanco / Nivel base). Integración de recursos del proyecto propio en `src/main/resources/`
* **Objetivo.** Crear un archivo de recursos plano dentro de la ruta estándar `src/main/resources/`, compilar el proyecto con Maven y comprobar que el archivo se transfiere automáticamente a `target/classes/` junto al Bytecode compilado.
* **Aplicación según tu proyecto elegido:**
  * En **Aventura conversacional**. Crear el archivo `src/main/resources/bienvenida.txt` con la ambientación introductoria del juego, ejecutar la fase `compile` desde el panel de Maven y verificar en el árbol de proyectos que el archivo de texto aparece duplicado en `target/classes/bienvenida.txt`.
  * En **Motor de recomendación**. Crear el archivo `src/main/resources/categorias.txt` con el catálogo base de géneros de ocio y validar que Maven gestiona el recurso sin errores de compilación.
  * En **Simulador de físicas 2D**. Crear el archivo `src/main/resources/constantes-universo.properties` definiendo valores de gravedad y fricción simulada, comprobando su despliegue en la carpeta de clases de salida.
  * En **Bóveda de contraseñas**. Crear el archivo `src/main/resources/politicas-seguridad.txt` con los requisitos de longitud y caracteres para las contraseñas, verificando la sincronización de recursos con Maven.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Exploración técnica de dependencias en Maven Central
* **Contexto técnico.** Un desarrollador de AzaharTech no descarga archivos `.jar` sueltos desde páginas web dudosas; busca las coordenadas oficiales en el repositorio central de Maven para garantizar la seguridad y autenticidad del software.
* **Misión de la kata:**
  1. Abre tu navegador web y accede al portal oficial de búsqueda de dependencias: **[https://mvnrepository.com](https://mvnrepository.com)**.
  2. Localiza una librería estándar del ecosistema Java representativa según la temática de tu proyecto propio:
     * Para **Aventura conversacional** o **Bóveda de contraseñas**: busca la librería **`JSON-java`** (coordenadas de la organización `org.json`).
     * Para **Motor de recomendación**: busca la librería de utilidades de colecciones y cadenas **`Apache Commons Lang`** (organización `org.apache.commons`).
     * Para **Simulador de físicas 2D**: busca la librería matemática y de matrices **`Apache Commons Math`** (organización `org.apache.commons`).
  3. Selecciona la versión estable más reciente y haz clic sobre la pestaña de configuración para **Maven**.
  4. Copia en tu libreta técnica el bloque XML `<dependency>` resultante, identificando claramente cuáles son sus tres coordenadas (**`groupId`**, **`artifactId`** y **`version`**).
  5. *Pregunta de investigación:* ¿Qué diferencia hay entre una versión marcada como *Release* y una marcada como *SNAPSHOT* o *Alpha*? Anota en tu cuaderno por qué en producción solo debemos usar versiones estables *Release*.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Filtrado automático de variables (*Resource Filtering*) en el `pom.xml`
* **Contexto de arquitectura.** Automatizar que Maven inyecte información de la versión del proyecto directamente dentro de un archivo de texto en tiempo de compilación sin escribirla a mano.
* **Misión de la kata:**
  1. En tu archivo `src/main/resources/info.txt`, escribe el siguiente contenido utilizando la sintaxis de variables de Maven:
     ```text
     Aplicación: ${project.artifactId}
     Versión oficial: ${project.version}
     Entorno de compilación: Java ${maven.compiler.target}
     ```
  2. Si compilas ahora, Maven copiará el archivo tal cual, mostrando literalmente el texto `${project.version}`.
  3. Para activar la sustitución dinámica de variables, abre tu archivo **`pom.xml`** y añade dentro de la etiqueta `<build>` la instrucción de filtrado de recursos:
     ```xml
     <build>
         <resources>
             <resource>
                 <directory>src/main/resources</directory>
                 <filtering>true</filtering>
             </resource>
         </resources>
     </build>
     ```
  4. Sincroniza los cambios de Maven en IntelliJ (`Ctrl + Shift + O`) y ejecuta la fase **`compile`**.
  5. Abre el archivo resultante en `target/classes/info.txt` y comprueba la magia de la automatización: Maven habrá reemplazado las variables por los valores reales de tu proyecto (*por ejemplo, `Versión oficial: 0.2.0-SNAPSHOT`*).

---

## Semana 6. Gestión de dependencias y cierre

---

### Día 11 - 2 sesiones

---

#### Sesión 1. Teoría. De la nada al código QR: Maven Central, dependencias declarativas y renderizado en memoria

---

#### 1. Caso guía en AzaharTech

Son las tres de la tarde del lunes 19 de octubre de 2026. Entramos en la última semana del **Sprint 2** en la sede de **AzaharTech** en Castellón de la Plana.

En la pantalla de proyección, **Laia Claramunt** y **Alba Torres** muestran el nuevo requerimiento técnico enviado por la dirección del **IES El Caminàs**:

> *«Equipo, el prototipo del instituto ya compila con la estructura estándar de Maven y gestiona limpiamente su ciclo de construcción. Pero hoy el cliente nos plantea un salto funcional determinante:*
>
> *El terminal de conserjería debe generar en pantalla el código QR del carné del estudiante para que el lector óptico del torno o el conserje puedan verificar su identidad en el acceso al centro.*
>
> *Programar un algoritmo de código QR desde cero requeriría meses implementando codificación binaria, máscaras de píxeles y corrección de errores Reed-Solomon. En la ingeniería de software profesional nadie reinventa la rueda.*
>
> *Para eso existe **Maven Central**: un repositorio público mundial con millones de librerías abiertas. Con solo declarar cuatro líneas en el archivo `pom.xml`, Maven descargará la librería industrial **ZXing Core** de Google, resolverá sus dependencias y nos permitirá generar una matriz QR real.*
>
> *Como todavía no hemos estudiado la gestión de ficheros en disco (que abordaremos más adelante en el curso), no guardaremos imágenes en carpetas: utilizaremos los bucles y condicionales que estáis aprendiendo en Programación para renderizar el código QR directamente en la consola con caracteres de texto. ¡Y será tan nítido que podréis escanearlo con la cámara de vuestro propio teléfono móvil apuntando a la pantalla del monitor!»*.

---

#### 2. El infierno de las librerías frente a la gestión declarativa de Maven

Antes de la llegada de herramientas de construcción como Apache Maven, incorporar código de terceros era un proceso manual, frágil y propenso a errores conocido como el **Jar Hell** (*el infierno de los JARs*):

```
┌────────────────────────────────────────────────────────────────────────┐
│                   EVOLUCIÓN EN LA GESTIÓN DE LIBRERÍAS                 │
├───────────────────────────────────┬────────────────────────────────────┤
│ Método artesanal antiguo          │ Gestión declarativa con Maven      │
├───────────────────────────────────┼────────────────────────────────────┤
│ 1. Buscar y descargar archivos    │ 1. Declarar coordenadas GAV en el  │
│    .jar en páginas web diversas.  │    archivo pom.xml.                │
│ 2. Copiarlos a mano en carpetas   │ 2. Maven descarga y valida         │
│    locales del proyecto.          │    automáticamente los binarios    │
│ 3. Si la librería necesita otra   │    desde Maven Central.            │
│    librería secundaria, buscarla  │ 3. Resolución automática del árbol │
│    manualmente a ciegas.          │    completo de dependencias        │
│ 4. Errores de ClassNotFoundException│    transitivas en ~/.m2/repository.│
│    en tiempo de ejecución.        │ 4. Integración transparente en el  │
│                                   │    Classpath del IDE.              │
└───────────────────────────────────┴────────────────────────────────────┘
```

##### Coordenadas GAV y dependencias transitivas
Para solicitar una librería a Maven Central, únicamente debemos indicar sus tres coordenadas universales: **GroupId**, **ArtifactId** y **Version**:

* **Dependencia directa:** Aquella que declaras explícitamente en el bloque `<dependencies>` de tu `pom.xml`.
* **Dependencia transitiva:** Si la librería que has solicitado necesita a su vez otras librerías auxiliares para operar, Maven analiza el descriptor remoto de esa librería y descarga toda la cadena de dependencias de forma automática.

```
RESOLUCIÓN DECLARATIVA EN MAVEN:

   Tu pom.xml ────► Solicita: [com.google.zxing:core:3.5.3]
                           │
                           ▼
                    Maven Central
                           │ (Descarga automática a caché local)
                           ▼
                 ~/.m2/repository/com/google/zxing/core/3.5.3/core-3.5.3.jar
                           │
                           ▼
                Classpath de tu aplicación (Listo para usar en Java)
```

---

#### 3. El caso guía en acción: Renderizado de QR en memoria y consola

Pau Ferrer abre IntelliJ IDEA y muestra el procedimiento completo para dotar al proyecto del IES El Caminàs de su generador de códigos QR sin tocar el sistema de archivos:

##### Paso 1: Declarar la dependencia en el `pom.xml`
Abrimos el archivo de configuración de Maven y añadimos el bloque de dependencias:

```xml
<dependencies>
    <!-- Motor de cálculo y generación de matrices QR de Google -->
    <dependency>
        <groupId>com.google.zxing</groupId>
        <artifactId>core</artifactId>
        <version>3.5.3</version>
    </dependency>
</dependencies>
```

##### Paso 2: Sincronizar Maven en IntelliJ IDEA
Tras editar el archivo, pulsamos el botón flotante con el icono de Maven (o el atajo **`Ctrl + Shift + O`**). IntelliJ contacta con Maven Central, descarga el archivo JAR en milisegundos y lo añade a las librerías del proyecto.

##### Paso 3: Dibujar la matriz en la consola
Creamos la clase `GeneradorQRCaminas`. La librería externa realiza los cálculos matemáticos avanzados y nos entrega una matriz bidimensional de bits (`BitMatrix`). Nosotros nos limitamos a recorrerla con dos bucles anidados e imprimir caracteres de bloque en pantalla:

```java
package es.azahartech.caminas;

import com.google.zxing.BarcodeFormat;
import com.google.zxing.common.BitMatrix;
import com.google.zxing.qrcode.QRCodeWriter;

public class GeneradorQRCaminas {

    public static void main(String[] args) throws Exception {
        String datosAlumno = "ALUMNO:12004217;CENTRO:IES_EL_CAMINAS;CURSO:1DAM";

        // 1. Invocamos a la librería externa para calcular la matriz QR
        QRCodeWriter escritorQR = new QRCodeWriter();
        // Generamos una matriz de 25x25 módulos en memoria
        BitMatrix matriz = escritorQR.encode(datosAlumno, BarcodeFormat.QR_CODE, 25, 25);

        System.out.println("=================================================");
        System.out.println("  CÓDIGO QR EN CONSOLA - ACCESO IES EL CAMINÀS   ");
        System.out.println("  Apunta con la cámara de tu móvil a la pantalla ");
        System.out.println("=================================================\n");

        // 2. Recorrido bidimensional de la matriz en memoria RAM (sin ficheros)
        for (int y = 0; y < matriz.getHeight(); y++) {
            for (int x = 0; x < matriz.getWidth(); x++) {
                // Si la posición tiene bit activo pintamos doble bloque negro "██";
                // si no, pintamos dos espacios en blanco "  "
                if (matriz.get(x, y)) {
                    System.out.print("██");
                } else {
                    System.out.print("  ");
                }
            }
            System.out.println(); // Salto a la siguiente fila
        }

        System.out.println("\n[OK] Código QR generado en memoria RAM y renderizado con éxito.");
    }
}
```

Al pulsar **Run (`Shift + F10`)**, la terminal de IntelliJ se transforma en un panel gráfico de texto: los estudiantes enfocan la consola con sus teléfonos móviles y la cámara reconoce al instante el texto codificado del instituto.

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

Pasamos a los puestos individuales. Cada alumno integrará una librería externa especializada de Maven Central en su proyecto de la bolsa para incorporar funciones profesionales sin necesidad de manipular ficheros:

---

##### Kata 1 (Cinturón blanco / Nivel base). El generador QR en tu entorno local

* **Objetivo.** Integrar la dependencia `zxing:core` en el proyecto del caso guía, sincronizar el proyecto con Maven Central y renderizar un código QR funcional en la consola de tu equipo.
* **Misión:**
    1. Abre el archivo `pom.xml` e introduce el bloque `<dependencies>` con `com.google.zxing:core:3.5.3`.
    2. Sincroniza el gestor con **`Ctrl + Shift + O`**.
    3. Implementa la clase de renderizado en `src/main/java/` y ejecuta el programa.
    4. Comprueba que el código QR aparece perfectamente cuadrado en la consola inferior de IntelliJ. Si se ve deformado, ajusta el ancho de los caracteres utilizando el doble bloque `"██"`.
    5. Escanéalo con tu propio móvil y verifica que lee los datos del alumno correctamente.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Inyección de superpoderes Maven en tu proyecto propio

* **Contexto técnico.** En la industria del software seleccionamos librerías consolidadas para dotar a nuestros programas de capacidades avanzadas en memoria sin escribir código redundante.
* **Misión según tu proyecto de la bolsa:**

    * **En Aventura conversacional:**
        * *Librería:* **`com.github.freva:ascii-table:1.8.0`** (Generador de tablas formateadas en consola).
        * *Aplicación:* Declara la dependencia en tu `pom.xml` y utiliza su API para sustituir los textos planos del menú por una tabla alineada en consola que muestre las estadísticas del personaje (Nivel, Puntos de Vida `HP`, Puntos de Magia `MP` y Oro) con bordes dobles profesionales.

    * **En Motor de recomendación:**
        * *Librería:* **`org.apache.commons:commons-math3:3.6.1`** (Matemáticas avanzadas de Apache).
        * *Aplicación:* Declara la dependencia en tu `pom.xml` e instancia dos objetos `ArrayRealVector` para representar las valoraciones numéricas de dos usuarios. Utiliza el método `.cosine(otroVector)` para calcular la similitud coseno entre ambos en una sola línea de código e imprime el porcentaje de afinidad por consola.

    * **En Simulador de físicas 2D:**
        * *Librería:* **`org.joml:joml:1.10.5`** (Álgebra vectorial en memoria).
        * *Aplicación:* Declara la dependencia y sustituye las variables primitivas de posición y aceleración por un objeto `Vector2f`. Aplica la aceleración sobre la velocidad mediante el método vectorial `velocidad.add(aceleracion)` e imprime las nuevas coordenadas resultantes en la consola.

    * **En Bóveda de contraseñas:**
        * *Librería:* **`org.mindrot:jbcrypt:0.4`** (Algoritmo de cifrado y derivación de claves BCrypt).
        * *Aplicación:* Declara la dependencia y utiliza `BCrypt.hashpw(passwordPlana, BCrypt.gensalt(12))` para generar en memoria un hash criptográfico real de 60 caracteres. Imprime el resultado en consola y comprueba mediante `BCrypt.checkpw()` cómo la librería valida si una contraseña introducida coincide o no sin necesidad de descifrarla.

* **Misión alternativa según tu proyecto de la bolsa:**

    * **En Aventura conversacional:**
        * *Librería:* **`com.diogonunes.JColor:5.5.1`** (Colores en el texto).
        * *Aplicación:* Colorear las estadísticas fijas del héroe: la salud en verde brillante, el nombre del enemigo en rojo y las monedas en amarillo.

    * **En Motor de recomendación:**
        * *Librería:* **`net.datafaker.datafaker:2.4.2`** (Generar datos del mundo real).
        * *Aplicación:* Generar automáticamente un título de película ficticia con faker.book().title() y una valoración decimal aleatoria para calcular su afinidad.

    * **En Simulador de físicas 2D:**
        * *Librería:* **`com.diogonunes.JColor:5.5.1`** (Colores en el texto).
        * *Aplicación:* Destacar los vectores cinemáticos en la salida formateada: velocidad inicial en azul y aceleración gravitatoria en magenta.

    * **En Bóveda de contraseñas:**
        * *Librería:* **`net.datafaker.datafaker:2.4.2`** (Generar datos del mundo real).
        * *Aplicación:* Instanciar faker.internet().password(12, 16, true, true) para generar una contraseña candidata de alta entropía y medir su longitud con métodos de String.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Radiografía de Maven Central y confirmación visual en Git

* **Contexto de arquitectura.** Un desarrollador senior debe auditar físicamente dónde residen los binarios descargados y confirmar las modificaciones en el repositorio respetando las buenas prácticas del equipo.
* **Misión:**
    1. **Inspección de la caché local (.m2):**
        * Localiza en el explorador de archivos de tu sistema operativo la carpeta oculta de Maven en tu directorio de usuario: `~/.m2/repository/` (o `%USERPROFILE%\.m2\repository\` en Windows).
        * Navega hasta la carpeta correspondiente a tu librería (por ejemplo, `com/google/zxing/core/3.5.3/`) y comprueba que Maven ha descargado el archivo `.jar` compilado y su archivo descriptor `.pom`.
    2. **Auditoría visual de dependencias en IntelliJ:**
        * Abre la pestaña lateral derecha **Maven**.
        * Despliega la rama **Dependencies** de tu proyecto y confirma que la librería aparece con su icono azul vinculada al proyecto.
    3. **Commit visual en IntelliJ IDEA (sin consola):**
        * Pulsa **`Ctrl + K`** (o abre la pestaña lateral izquierda *Commit*).
        * En el visor de diferencias (*Diff Viewer*), haz doble clic sobre `pom.xml` para comprobar visualmente que solo se han añadido las etiquetas de dependencias.
        * Marca con visto bueno (`[x]`) los archivos modificados (`pom.xml` y la nueva clase Java).
        * Escribe en el cuadro de texto el mensaje estructurado:
          `feat(core): incorporada libreria externa maven y renderizado en memoria`
        * Haz clic en el botón azul **Commit**.

---

### Día 12 - 1 sesión

---

#### Taller de consolidación: ciclo de vida de Maven, higiene de repositorio y cierre formal del Sprint 2

---

#### 1. Caso guía en AzaharTech

Viernes 23 de octubre de 2026. Son las cuatro de la tarde en la oficina de **AzaharTech** en Castellón de la Plana. La jornada marca el cierre definitivo del **Sprint 2: «El viaje del código: compilación, máquinas virtuales y dependencias»**.

En las pantallas de los puestos de desarrollo, el generador de códigos QR del **IES El Caminàs** y los cuatro proyectos singulares de la bolsa (**Aventura conversacional**, **Motor de recomendación**, **Simulador de físicas 2D** y **Bóveda de contraseñas**) se ejecutan con fluidez: cada uno incorpora ya librerías externas de primer nivel gestionadas por Maven Central y renderizadas limpiamente en memoria RAM sin necesidad de ensuciar el disco con ficheros temporales.

**Laia Claramunt** y **Pau Ferrer** reúnen al equipo frente al proyector para la sesión de cierre:

> *«Equipo, en este segundo sprint habéis conquistado un terreno técnico crucial: sabéis cómo el compilador genera bytecode, cómo la JVM lo interpreta en cualquier plataforma y cómo Maven descarga y enlaza dependencias industriales en cuestión de segundos.*
>
> *Sin embargo, antes de dar un sprint por completado en AzaharTech, todo software debe superar dos controles de calidad indispensables:*
> 1. *Validar que el ciclo de vida de Maven ejecuta las fases de limpieza y compilación sin depender de configuraciones manuales del IDE.*
> 2. *Auditar la higiene estricta de Git: verificar que la carpeta `target/` y los metadatos internos del sistema jamás contaminen el repositorio remoto en GitHub.*
>
> *Hoy cerraremos formalmente el Sprint 2 ejecutando el ciclo de vida de Maven desde la interfaz de IntelliJ IDEA y realizando el commit y push visual definitivo antes de encarar el lunes el Sprint 3: el laberinto de la lógica, las pruebas formales y la depuración interactiva»*.

---

#### 2. La prueba de fuego: ciclo de vida visual de Maven en IntelliJ

Cuando un proyecto integra librerías externas, la compilación no se limita a invocar al compilador `javac`: Maven debe verificar la integridad de las dependencias alojadas en `~/.m2/repository` y enlazarlas en el *Classpath* de compilación.

Para comprobar que el proyecto compila de forma reproducible y autónoma, los desarrolladores ejecutan las fases del ciclo de vida utilizando la **ventana lateral de Maven** de IntelliJ IDEA:

```
                  VENTANA LATERAL DE MAVEN EN INTELLIJ IDEA
                  
       ┌────────────────────────────────────────────────────────┐
       │ Maven                                              [-] │
       ├────────────────────────────────────────────────────────┤
       │ ▼ mi-proyecto                                          │
       │   ▼ Lifecycle                                          │
       │       clean     ◄── (1. Doble clic: borra target/)     │
       │       validate                                         │
       │       compile   ◄── (2. Doble clic: compila + .m2)     │
       │       test                                             │
       │       package                                          │
       │   ► Plugins                                            │
       │   ► Dependencies (Librerías externas enlazadas)        │
       └────────────────────────────────────────────────────────┘
```

##### Verificación en dos pasos:
1. **Fase `clean`:**
    * Haz doble clic sobre **clean** dentro del menú *Lifecycle*.
    * Observa cómo la carpeta de salida `target/` desaparece instantáneamente del árbol del proyecto, garantizando que no queden restos de compilaciones previas.
2. **Fase `compile`:**
    * Haz doble clic sobre **compile**.
    * Maven analiza el archivo `pom.xml`, valida las dependencias externas (como `zxing-core`), compila todo el código fuente de `src/main/java/` y genera de nuevo la estructura binaria en `target/classes/`.
    * La consola inferior de Maven debe culminar con el mensaje en verde:
      `[INFO] BUILD SUCCESS`.

---

#### 3. Auditoría de higiene del repositorio: el archivo `.gitignore`

Uno de los errores más graves de un desarrollador júnior es versionar en Git los archivos binarios compilados o los archivos de configuración local de su propio ordenador.

En un proyecto gestionado con Maven, el código fuente (`src/`) y la receta de construcción (`pom.xml`) son los **únicos** elementos necesarios para reconstruir el software en cualquier máquina del mundo.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   MAPA DE HIGIENE TÉCNICA DEL REPOSITORIO              │
├────────────────────────────┬─────────────┬─────────────────────────────┤
│ Elemento / Directorio      │ ¿Va a Git?  │ Justificación técnica       │
├────────────────────────────┼─────────────┼─────────────────────────────┤
│ src/                       │ SÍ          │ Código fuente y recursos.   │
│ pom.xml                    │ SÍ          │ Declaración del proyecto.   │
│ README.md                  │ SÍ          │ Documentación del software. │
├────────────────────────────┼─────────────┼─────────────────────────────┤
│ target/                    │ NUNCA       │ Bytecode autogenerado.      │
│ .idea/ y *.iml             │ NUNCA       │ Configuración de tu IDE.    │
│ .DS_Store / Thumbs.db      │ NUNCA       │ Basura del sistema operativo│
└────────────────────────────┴─────────────┴─────────────────────────────┘
```

##### Inspección visual en el árbol de IntelliJ IDEA
Cada estudiante abre el archivo `.gitignore` situado en la raíz del proyecto y confirma que contiene las exclusiones estándar de la empresa:

```gitignore
# Directorio de construcción y binarios de Maven
target/

# Metadatos del entorno de desarrollo (IntelliJ IDEA)
.idea/
*.iml
out/

# Archivos de índices del sistema operativo
.DS_Store
Thumbs.db
```

> **Comprobación visual de Pau Ferrer:** En el panel del explorador de proyectos de IntelliJ, la carpeta `target/` debe mostrarse con una tipografía de **color naranja oscuro o gris atenuado** (*ignored*). Si aparece en color blanco, azul o rojo, Git la está vigilando por error y debe regularizarse de inmediato antes del commit.

---

#### 4. Cierre formal del Sprint 2 mediante Commit y Push visual

Siguiendo el estándar pedagógico de AzaharTech, el cierre del sprint se formaliza **exclusivamente a través de la interfaz gráfica de IntelliJ IDEA**, sin recurrir a la terminal de comandos:

```
                VENTANA VISUAL DE COMMIT EN INTELLIJ IDEA (Ctrl + K)
                
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Commit: Local Changes                                                  │
 ├────────────────────────────────────────────────────────────────────────┤
 │ Changes (2 files)                                                      │
 │  [x] pom.xml                        (dependencia zxing/externa)        │
 │  [x] src/main/java/.../App.java     (renderizado de QR / lógica)      │
 ├────────────────────────────────────────────────────────────────────────┤
 │ Mensaje de confirmación:                                               │
 │ ┌────────────────────────────────────────────────────────────────────┐ │
 │ │ feat(core): integradas dependencias maven y renderizado en memoria │ │
 │ └────────────────────────────────────────────────────────────────────┘ │
 │                                                                        │
 │ [ Commit v ]  ──► Desplegar flecha: [ Commit and Push... ]            │
 └────────────────────────────────────────────────────────────────────────┘
```

##### Pasos visuales paso a paso:
1. **Abrir la herramienta visual de cambios:**
    * Pulsa el atajo universal **`Ctrl + K`** (en Windows/Linux) o haz clic en la pestaña vertical izquierda **Commit** (`Alt + 0`).
2. **Revisión de diferencias (*Diff Viewer*):**
    * Haz doble clic sobre `pom.xml` en la lista de cambios. Comprueba en el panel comparativo que las etiquetas `<dependency>` se han introducido de forma limpia.
    * Haz doble clic sobre la clase Java para certificar que el código recorre la matriz en memoria sin dependencias de ficheros.
3. **Selección de archivos:**
    * Marca con visto bueno (`[x]`) únicamente los archivos modificados que deben formar parte del repositorio (`pom.xml`, `.gitignore` y clases Java bajo `src/main/java/`).
    * Cerciórate de que ningún fichero dentro de `target/` aparece seleccionado.
4. **Redacción del mensaje de commit:**
    * Escribe en el cuadro de texto el mensaje formal de cierre:
      `feat(core): integradas dependencias maven y renderizado en memoria`
5. **Confirmación y sincronización con GitHub (*Commit and Push*):**
    * Haz clic sobre la flecha desplegable del botón azul y selecciona **Commit and Push...** (o pulsa **`Ctrl + Alt + K`**).
    * En la ventana emergente de confirmación de subida (*Push Commits*), revisa que la rama de destino sea tu repositorio personal en GitHub y haz clic en el botón azul **Push**.

Al mostrarse la notificación emergente verde en la esquina inferior derecha (*«Pushed 1 commit to origin/main»*), el **Sprint 2: «El viaje del código: compilación, máquinas virtuales y dependencias»** queda oficialmente completado y sellado.

El equipo domina la construcción automatizada, el enlace de librerías de Maven Central y la higiene del repositorio digital. El próximo lunes arrancará el **Sprint 3**, donde utilizaremos el depurador interactivo de IntelliJ para aprender a congelar la ejecución y cazar los errores de nuestras estructuras de control algorítmicas.

