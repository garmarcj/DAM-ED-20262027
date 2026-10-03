# Sprint 2. El viaje del código: compilación, máquinas virtuales y dependencias

---

## Semana 1. Código fuente, código objeto y tecnologías de virtualización

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

## Semana 2. Herramientas de construcción y estandarización con Maven

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

#### 4. Puesta en común de coordenadas en la pizarra
* Validación cruzada: varios alumnos indican en voz alta las coordenadas GAV diseñadas para verificar que cumplen las normas de nomenclatura técnica (sin mayúsculas, sin espacios y con identificadores claros).

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

### Día 11 - 2 sesiones

---

#### Sesion 1. Teoría. El infierno de las librerías frente a la gestión declarativa de dependencias

#### 1. Caso guía en AzaharTech
Son las tres de la tarde del lunes 19 de octubre de 2026. Entramos en la última semana del Sprint 2. En la sala de reuniones de **AzaharTech**, **Alba Torres** proyecta en la pantalla una nueva petición del equipo directivo del **IES El Caminàs**:

> *«Equipo, el terminal de vestíbulo ya compila limpiamente bajo la estructura de Maven, pero ahora nos piden generar la imagen física del código QR para proyectarla en la pantalla. Ningún programador en el mundo escribe desde cero los miles de cálculos matemáticos necesarios para codificar una matriz de píxeles QR: para eso utilizamos **librerías de terceros** creadas y mantenidas por la comunidad.*
>
> *En los proyectos de vuestros equipos ocurre exactamente lo mismo: **Aventura conversacional** y **Bóveda de contraseñas** necesitarán librerías para estructurar datos en formato JSON, **Motor de recomendación** requerirá herramientas para procesar texto avanzado y **Simulador de físicas 2D** necesitará funciones de cálculo vectorial.*
>
> *Antiguamente, añadir una librería externa a un proyecto era una pesadilla técnica conocida como el 'infierno de las dependencias' (*Jar Hell*). Hoy aprenderemos cómo **Apache Maven automatiza la resolución declarativa de dependencias** y resuelve por nosotros las dependencias transitivas en cuestión de segundos»*.

---

#### 2. El mecanismo de dependencias de Maven

```
┌────────────────────────────────────────────────────────────────────────┐
│                        EL SISTEMA DE DEPENDENCIAS EN MAVEN             │
├───────────────────────────────────┬────────────────────────────────────┤
│ Método artesanal antiguo          │ Gestión declarativa con Maven      │
├───────────────────────────────────┼────────────────────────────────────┤
│ 1. Buscar el archivo .jar en webs │ 1. Añadir el bloque <dependency>   │
│    inseguras en internet.         │    con las coordenadas GAV en el   │
│ 2. Copiarlo a mano a una carpeta. │    archivo pom.xml.                │
│ 3. Si requiere otra librería,     │ 2. Maven calcula el árbol de       │
│    buscarla también a mano.       │    dependencias y descarga todo de │
│ 4. Si falta una: fallo en runtime.│    forma automática y segura.      │
└───────────────────────────────────┴────────────────────────────────────┘
```

##### A. El bloque `<dependencies>` en el archivo `pom.xml`
Para incorporar una librería externa certificada, el desarrollador no descarga ningún archivo manualmente; simplemente declara en su archivo `pom.xml` qué librería necesita utilizando sus coordenadas universales (**GAV**):

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.14.0</version>
    </dependency>
</dependencies>
```

##### B. El concepto clave: dependencias transitivas (*Transitive dependencies*)
¿Qué sucede si la librería que has añadido necesita a su vez otras dos librerías secundarias para funcionar?
* En un proyecto sin herramientas de construcción, el programa fallaría en tiempo de ejecución con un error de tipo `NoClassDefFoundError`.
* **La magia de Maven:** Maven lee los archivos `pom.xml` de las librerías que descargas, construye un **grafo de dependencias** en memoria y descarga automáticamente todas las librerías secundarias e indirectas que el software requiera.

##### C. El ámbito de las dependencias (*Scope*)
No todas las librerías deben estar presentes en todos los momentos del ciclo de vida del software:
* **`compile` (por defecto).** La librería es necesaria para compilar, probar y ejecutar el programa en producción.
* **`test`.** La librería solo se necesita durante la ejecución de pruebas automatizadas y no se incluirá en el software final entregado al cliente (se profundizará en el Sprint 6 con JUnit).
* **`provided`.** La librería se requiere para compilar, pero en producción la proporcionará el propio entorno o servidor.

---

#### 3. Actividad activa interactiva: Análisis del árbol de dependencias
*Los estudiantes debaten y resuelven un problema de arquitectura de dependencias en papel antes de codificar:*

1. **El reto del conflicto de versiones:**
    * Supongamos que en nuestro proyecto declaramos la *Librería A* (que internamente requiere la *Librería C en su versión 1.0*).
    * Al mismo tiempo, añadimos la *Librería B* (que internamente requiere la misma *Librería C pero en su versión 2.0*).
2. **Dinámica de análisis por parejas:**
    * ¿Qué versión de la *Librería C* creéis que elegirá Maven para evitar duplicidades en el Classpath?
    * *Regla de resolución de Maven:* Maven aplica la **regla del camino más corto (*Nearest Definition*)**: utiliza la versión que esté a menos niveles de distancia del `pom.xml` raíz. Si están al mismo nivel, prevalece la que se declaró primero en el archivo.

---

#### 4. Puesta en común y sincronización en IntelliJ IDEA
* El docente muestra en pantalla cómo el IDE detecta inmediatamente cualquier modificación en el archivo `pom.xml`.
* Se localiza el icono flotante de recarga de Maven (**Reload Changes** o atajo **`Ctrl + Shift + O`**) y se observa la barra de progreso inferior de IntelliJ mientras descarga los paquetes del repositorio central.

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas
Pasamos al dojo de entrenamiento de herramientas, con tres **katas de integración declarativa de dependencias, inspección de dependencias transitivas y gestión de ámbitos** para entrenar la integración de librerías profesionales:

---

##### Kata 1 (Cinturón blanco / Nivel base). Declaración y uso de una librería oficial en el proyecto propio
* **Objetivo.** Añadir una primera dependencia externa certificada al archivo `pom.xml` de tu proyecto propio, sincronizar Maven en IntelliJ IDEA y comprobar que sus clases quedan disponibles en el editor con autocompletado inteligente.
* **Aplicación según tu proyecto elegido:**
    * En **Aventura conversacional**. Abrir el `pom.xml` y añadir la librería **`org.apache.commons:commons-lang3:3.14.0`** para manipulación avanzada de textos y diálogos. Comprobar que en `MiProyecto.java` puedes invocar utilidades como `StringUtils`.
    * En **Motor de recomendación**. Añadir la dependencia **`org.apache.commons:commons-lang3:3.14.0`** para formateo de matrices de afinidad y comprobar que el IDE resuelve las clases externas sin errores de compilación.
    * En **Simulador de físicas 2D**. Añadir la librería matemática **`org.apache.commons:commons-math3:3.6.1`** (organización `org.apache.commons`) para futuros cálculos vectoriales de precisión y sincronizar el proyecto con `Ctrl + Shift + O`.
    * En **Bóveda de contraseñas**. Añadir la dependencia **`org.apache.commons:commons-lang3:3.14.0`** para el tratamiento avanzado de caracteres y cadenas seguras, verificando que la librería se descarga en el repositorio local.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Inspección visual del árbol de dependencias transitivas
* **Contexto técnico.** Un desarrollador de software debe saber qué librerías secundarias ha introducido indirectamente en su proyecto para evitar problemas de licencias o vulnerabilidades de seguridad.
* **Misión de la kata:**
    1. Abre el panel lateral **Maven** en el margen derecho de IntelliJ IDEA.
    2. Localiza y despliega la carpeta denominada **Dependencies**.
    3. Comprueba que aparece la librería que declaraste en la Kata 1 y observa cómo debajo de ella se listan las librerías secundarias (*transitivas*) que Maven ha descargado automáticamente.
    4. Abre la terminal integrada de IntelliJ (`Alt + F12`) en la raíz del proyecto y ejecuta el comando oficial de diagnóstico de dependencias de Maven:
       ```bash
       mvn dependency:tree
       ```
    5. Inspecciona el árbol resultante en texto plano y localiza en qué rama del árbol reside cada componente descargado.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Aislamiento de dependencias mediante ámbitos (`<scope>`)
* **Contexto de arquitectura.** Comprender cómo la etiqueta `<scope>` permite aislar librerías de prueba para que no se filtren por descuido en el código de producción de la aplicación.
* **Misión de la kata:**
    1. En tu archivo `pom.xml`, edita la dependencia añadida en la Kata 1 e introduce la etiqueta de ámbito de pruebas:
       ```xml
       <scope>test</scope>
       ```
    2. Sincroniza Maven con el atajo `Ctrl + Shift + O`.
    3. Abre tu clase de código fuente en `src/main/java/MiProyecto.java` e intenta importar alguna clase de esa librería (por ejemplo, `import org.apache.commons.lang3.StringUtils;`).
    4. Observa cómo IntelliJ resalta la línea en rojo con un error de compilación: **la librería existe en el disco duro, pero el ámbito `test` le prohíbe al código principal acceder a ella**.
    5. Elimina la etiqueta `<scope>test</scope>` (o devuélvela a su valor por defecto `compile`) y vuelve a sincronizar para restaurar la visibilidad en todo el proyecto.
    6. Redacta dos líneas en tu cuaderno técnico: **¿por qué aislar dependencias según su ámbito mejora la seguridad y reduce el peso del software en producción?**

---

### Día 12 - 1 sesión

---

#### Teoria. Auditoría de proyecto Maven y registro de release v0.2.0

#### 1. Caso guía en AzaharTech
Es viernes 23 de octubre por la tarde en la sede de **AzaharTech**. Las tres semanas de trabajo del **Sprint 2** concluyen hoy formalmente. 

En la sala de reuniones, **Laia Claramunt** proyecta el estado del repositorio corporativo del caso guía del **IES El Caminàs**:
> *«Equipo, contemplad el salto de ingeniería que hemos dado en este segundo ciclo de desarrollo. En el Sprint 1 teníamos archivos de código sueltos compilados a mano; hoy disponemos de un proyecto industrial estandarizado bajo **Apache Maven**, con su jerarquía oficial `src/main/java` y `src/main/resources`, gobernado por un archivo descriptor `pom.xml`, con resolución automática de dependencias y plenamente verificado en la máquina virtual.*
>
> *En los proyectos de vuestros equipos (**Aventura conversacional**, **Motor de recomendación**, **Simulador de físicas 2D** y **Bóveda de contraseñas**) habéis logrado la misma madurez técnica.*
>
> *Hoy cerraremos este hito aplicando las buenas prácticas de la empresa: auditaremos que el archivo `.gitignore` mantiene el repositorio limpio de compilados en la carpeta `target/`, actualizaremos el panel de tareas del `README.md` y congelaremos el segundo incremento de software mediante la etiqueta formal de versión **`v0.2.0-sprint2`**»*.

---

#### 2. Micro-exposición docente: Versionado semántico y herencia del Super POM

```
┌────────────────────────────────────────────────────────────────────────┐
│                        EVOLUCIÓN DEL VERSIONADO SEMÁNTICO              │
├───────────────────────────────┬────────────────────────────────────────┤
│ Tag Sprint 1: v0.1.0-sprint1  │ Tag Sprint 2: v0.2.0-sprint2           │
├───────────────────────────────┼────────────────────────────────────────┤
│ • Esqueleto de código secuencial│ • Migración a estructura Maven       │
│ • Compilación manual en IDE   │ • Compilación automatizada en lifecycle│
│ • Taller digital básico       │ • Gestión declarativa de dependencias  │
└───────────────────────────────┴────────────────────────────────────────┘
```

##### A. El incremento del dígito menor (*Minor version*)
Siguiendo el estándar internacional **SemVer 2.0.0**:
* Avanzamos de la versión `0.1.0` a la **`0.2.0`**.
* Se incrementa el dígito central (*Minor*) porque hemos incorporado una nueva infraestructura completa (Apache Maven, dependencias externas y recursos empaquetados) manteniendo la compatibilidad con el código previo del Sprint 1.

##### B. El concepto del Super POM en Maven
¿Por qué nuestro archivo `pom.xml` solo tiene 20 líneas pero es capaz de ejecutar tareas tan complejas?
* Maven utiliza el principio de **herencia de configuración**: todo archivo `pom.xml` hereda de forma invisible de un archivo maestro del sistema llamado **Super POM**.
* El Super POM define las versiones por defecto de los plugins del compilador (`maven-compiler-plugin`), las rutas universales de carpetas y los repositorios remotos oficiales como Maven Central.

---

#### Dojo de entrenamiento y katas de herramientas

Pasamos al dojo de entrenamiento de herramientas, con tres **katas de auditoría técnica de Maven, etiquetado de versión en Git y análisis del Super POM** para registrar con rigor el segundo sprint del curso:

---

##### Kata 1 (Cinturón blanco / Nivel base). Auditoría de proyecto Maven y actualización del panel README
* **Objetivo.** Comprobar que el proyecto Maven compila limpiamente desde el ciclo de vida de IntelliJ IDEA, verificar que la carpeta `target/` no se sube a GitHub gracias al `.gitignore` y actualizar el checklist del Sprint 2 en el archivo `README.md`.
* **Aplicación según tu proyecto elegido:**
  * En **Aventura conversacional**. Abrir el panel de Maven en IntelliJ, ejecutar la fase `clean` seguida de `compile`, verificar que el `README.md` describe la integración de Maven y comprobar que el árbol de trabajo de Git permanece limpio.
  * En **Motor de recomendación**. Verificar que la carpeta `target/` aparece en color gris atenuado en el árbol de proyectos, asegurando que los archivos `.class` compilados no se subirán a la nube.
  * En **Simulador de físicas 2D**. Comprobar que los recursos en `src/main/resources/` se copian correctamente a `target/classes/` tras compilar y actualizar las tareas del Sprint 2 como completadas (`- [x]`) en el `README.md`.
  * En **Bóveda de contraseñas**. Realizar una compilación limpia del proyecto con su dependencia declarada y confirmar que el archivo `pom.xml` está guardado sin advertencias de sintaxis XML.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Congelación y etiquetado oficial de la release (`v0.2.0-sprint2`)
* **Contexto técnico.** En la ingeniería del software profesional, cada entrega validada de sprint se sella en el historial de Git mediante una etiqueta de versión inmutable para que el cliente y el docente puedan auditar el incremento exacto.
* **Misión de la kata:**
  1. En IntelliJ IDEA, abre el panel de confirmación visual de Git (**`Ctrl + K`**).
  2. Comprueba que están seleccionados únicamente los cambios de documentación (`README.md` y `pom.xml`) y escribe el mensaje convencional de cierre:
     ```text
     docs(ed): cerrar sprint 2 con estructura maven y dependencias integradas
     ```
  3. Pulsa **Commit and Push**.
  4. Crea la etiqueta de versión oficial desde la interfaz visual de IntelliJ:
     * Menú superior: **Git -> New Tag...**
     * **Tag Name:** `v0.2.0-sprint2`
     * **Message:** `Release oficial del sprint 2 - Proyecto Maven con dependencias AzaharTech`
     * Pulsa **Create Tag**.
  5. Sincroniza la etiqueta con GitHub: menú **Git -> Push...**, marca la casilla **Push Tags: All** y pulsa **Push**.
  6. Abre tu navegador web, accede a tu repositorio de GitHub y verifica que en la sección lateral **Tags** aparece listada la versión `v0.2.0-sprint2`.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Radiografía del Super POM mediante `mvn help:effective-pom`
* **Contexto de arquitectura.** Inspeccionar la configuración completa real que la JVM y Maven aplican sobre el proyecto, combinando tu `pom.xml` con el Super POM heredado del sistema (CE 1.f).
* **Misión de la kata:**
  1. Abre la terminal integrada de IntelliJ (`Alt + F12`) en el directorio donde reside tu archivo `pom.xml`.
  2. Ejecuta el comando de diagnóstico avanzado de Maven:
     ```bash
     mvn help:effective-pom
     ```
  3. Observa cómo la terminal genera un documento XML de cientos de líneas: es el **POM Efectivo**.
  4. Localiza dentro de la salida los siguientes tres elementos heredados que tú no tuviste que escribir:
     * La URL oficial del repositorio central: `https://repo.maven.apache.org/maven2`.
     * El directorio por defecto de salida de clases: `<outputDirectory>.../target/classes</outputDirectory>`.
     * La versión interna del plugin de recursos: `maven-resources-plugin`.
  5. Redacta dos líneas de conclusión en tu libreta técnica: **¿por qué la herencia del Super POM ahorra cientos de líneas de configuración a los equipos de desarrollo de AzaharTech?**

---