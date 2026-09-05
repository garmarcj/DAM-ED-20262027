# MÓDULO PROFESIONAL: ENTORNOS DE DESARROLLO (ED)

## SPRINT 1. Ecosistema de desarrollo, marco ágil y repositorio digital (3 semanas | 9 horas)

---

# SEMANA 1 — SESIÓN 1 (Lunes, 14 de septiembre de 2026 — 2 horas lectivas)
### Bloque: Fundamentos del software, ciclo de vida (SDLC) y puesta a punto del taller digital (IDE + JDK)
* **Distribución horaria:** 1 hora de teoría conceptual y metodológica + 1 hora de laboratorio práctico guiado.
* **Criterios de Evaluación vinculados:** RA1.a, RA1.b, RA1.e, RA1.f, RA2.a, RA2.b, RA2.c, RA2.d, RA2.g.

---

## PARTE I. SESIÓN TEÓRICA (1 HORA): INGENIERÍA DEL SOFTWARE Y HERRAMIENTAS

### 1. Caso práctico narrativo: El encargo en las oficinas de AzaharTech

Son las nueve de la mañana del lunes 14 de septiembre de 2026. Las oficinas de **AzaharTech**, consultora de desarrollo de software situada en el distrito tecnológico de Castellón de la Plana, bullen de actividad. Los miembros de la célula de desarrollo asignada a proyectos educativos y de gestión local ocupan sus puestos: **Alba Torres**, desarrolladora especializada en arquitectura de software; **Pau Ferrer**, técnico de desarrollo e integración de sistemas; y la supervisora del equipo, **Laia Claramunt**. Junto a ellos se sienta el nuevo desarrollador junior que acaba de incorporarse a la empresa (el estudiante).

En la pantalla táctil de la sala de reuniones, Laia proyecta una fotografía de la fachada del **IES El Caminàs**, emblemático centro educativo de la ciudad:

> *«Equipo, bienvenidos al arranque del curso académico. Este trimestre tenemos un reto de primer nivel. El equipo directivo del IES El Caminàs nos ha contratado formalmente para digitalizar y automatizar el acceso al recinto escolar. Cada mañana, cientos de alumnos se agolpan en la entrada, provocando retrasos en el inicio de las clases y obligando a conserjería y a los profesores de guardia a anotar incidencias en listas de papel que tardan horas en llegar a jefatura de estudios.*
>
> *Nos han encargado desarrollar un **sistema de control de asistencia mediante códigos QR dinámicos**. La solución mostrará un código QR variable en una gran pantalla situada en el vestíbulo principal, que los estudiantes escanearán desde su teléfono móvil al cruzar la puerta de entrada.*
>
> *Este proyecto del IES El Caminàs será nuestro **caso guía de referencia**: sobre él modelaremos en clase cada decisión técnica y cada arquitectura. Pero, paralelamente, cada uno de vuestros equipos trabajará sobre el proyecto singular que habéis seleccionado de nuestra bolsa de proyectos.*
>
> *Ahora bien: antes de teclear una sola línea de código en un editor, necesitamos aprender a pensar como ingenieros. No vamos a construir un simple script casero; vamos a levantar un **Sistema de Información profesional**. Para que el proyecto no naufrague, hoy debemos entender en profundidad qué etapas componen el ciclo de vida del software, cómo se transforman los lenguajes de programación y cómo preparar nuestro taller de trabajo con las herramientas adecuadas»*.

---

### 2. De la instrucción aislada al Sistema de Información

En el argot popular es muy común escuchar términos como *programa*, *código*, *aplicación* o *sistema* como si fuesen sinónimos intercambiables. Sin embargo, en el ámbito de la ingeniería del software y el desarrollo de aplicaciones multiplataforma (DAM), establecer con rigor sus fronteras conceptuales es el primer paso obligatorio.

#### A. Programa informático
Un **programa informático** es una secuencia estructurada, lógica y finita de instrucciones escritas en un lenguaje de programación formal, diseñada para que un procesador ejecute una serie de tareas concretas de cálculo o manipulación de datos. Por ejemplo: un algoritmo que recibe la hora actual y la hora oficial de entrada y calcula la diferencia en minutos es un programa informático.

#### B. Sistema de Información (SI)
Un **Sistema de Información (SI)** es un ecosistema mucho más complejo, amplio y multidimensional. Se define como un conjunto organizado de elementos interactivos que recopilan, procesan, almacenan, aseguran y distribuyen datos para apoyar la toma de decisiones, la coordinación, el control y la operativa diaria de una organización o negocio.

Un Sistema de Información no se limita al código fuente; está formado por **cinco componentes indispensables** que deben encajar con precisión:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        LOS CINCO COMPONENTES DE UN SISTEMA DE INFORMACIÓN              │
├─────────────────┬──────────────────────────────────┬───────────────────────────────────┤
│ Componente      │ Definición técnica               │ Aplicación al IES El Caminàs      │
├─────────────────┼──────────────────────────────────┼───────────────────────────────────┤
│ 1. Hardware     │ Dispositivos físicos, máquinas,  │ Pantalla del vestíbulo, servidores│
│                 │ terminales, procesadores y redes.│ del centro, teléfonos móviles.    │
├─────────────────┼──────────────────────────────────┼───────────────────────────────────┤
│ 2. Software     │ Programas, aplicaciones y lógica │ Backend en Java, lectores QR,     │
│                 │ ejecutada sobre el hardware.     │ interfaces gráficas Swing, bases. │
├─────────────────┼──────────────────────────────────┼───────────────────────────────────┤
│ 3. Datos        │ Materia prima de la información; │ Censos de alumnos, matrículas,    │
│                 │ registros brutos sin procesar.   │ horas exactas de fichaje, grupos. │
├─────────────────┼──────────────────────────────────┼───────────────────────────────────┤
│ 4. Personas     │ Usuarios finales, administradores│ Alumnado, docentes de guardia,    │
│                 │ y personal técnico del sistema.  │ conserjería, equipo directivo.    │
├─────────────────┼──────────────────────────────────┼───────────────────────────────────┤
│ 5. Procesos     │ Reglas de negocio, protocolos y  │ Protocolo de entrada: retrasos a  │
│                 │ flujos operativos de la entidad. │ partir de las 08:05 h, avisos web.│
└─────────────────┴──────────────────────────────────┴───────────────────────────────────┘
```

> **Reflexión para el desarrollador:** Como programadores, nosotros producimos directamente el **Software**. Sin embargo, si nuestro código no contempla las limitaciones del **Hardware** (pantallas lentas o móviles sin cámara de calidad), el perfil de las **Personas** que lo utilizarán (estudiantes con prisa o docentes sin formación técnica) o los **Procesos** de la organización, el proyecto fracasará aunque el compilador no devuelva ningún error.

---

### 3. El Ciclo de Vida del Desarrollo de Software (SDLC)

El software no se fabrica como una pieza física en una cadena de montaje; el software se diseña, se modela, se implementa y evoluciona. La experiencia acumulada durante décadas en la industria informática demostró que lanzarse a programar sin una metodología previa desembocaba invariablemente en proyectos que duplicaban su presupuesto, se entregaban con meses de retraso o no cumplían lo que el cliente necesitaba.

Para garantizar la calidad técnica y la viabilidad económica de cualquier desarrollo, se aplica el **Ciclo de Vida del Desarrollo de Software** (*Software Development Life Cycle* o **SDLC**): un marco de trabajo formal que define las fases secuenciales e iterativas que transcurren desde que surge una necesidad operativa hasta que el software se retira definitivamente del servicio.

```
  ┌──────────────┐     ┌──────────────┐     ┌──────────────────────┐
  │  1. ANÁLISIS ├────►│  2. DISEÑO   ├────►│  3. IMPLEMENTACIÓN   │
  │ ¿Qué hacer?  │     │ ¿Cómo hacer? │     │     (Codificación)   │
  └──────────────┘     └──────────────┘     └──────────┬───────────┘
                                                       │
  ┌──────────────┐     ┌──────────────┐     ┌──────────▼───────────┐
  │6.MANTENIMIEN-│◄────┤5. DESPLIEGUE │◄────┤     4. PRUEBAS       │
  │      TO      │     │ (Producción) │     │ (Testing y Calidad)  │
  └──────────────┘     └──────────────┘     └──────────────────────┘
```

#### Fase 1. Análisis de requisitos
Es la fase inicial y más crítica. Su objetivo es responder con absoluta claridad a la pregunta: **¿Qué debe hacer el software?**
* Se mantienen entrevistas con el cliente (jefatura de estudios del IES El Caminàs).
* Se redactan los **requisitos funcionales** (lo que el sistema debe hacer: *«generar un QR dinámico cada 10 segundos»*) y los **requisitos no funcionales** (restricciones de rendimiento, seguridad o tecnología: *«el sistema debe responder en menos de 1 segundo con 50 accesos simultáneos»*).
* *Regla de oro de la ingeniería:* Un error detectado en la fase de análisis cuesta hasta 100 veces menos de solucionar que si se descubre cuando el programa ya está instalado en el cliente.

#### Fase 2. Diseño de la arquitectura
Responde a la pregunta: **¿Cómo lo vamos a construir?**
* Se divide el sistema en subsistemas y componentes independientes (módulo de generación visual, módulo de red, módulo de validación matemática).
* Se eligen las tecnologías, se diseñan los esquemas de bases de datos, los diagramas de clases y los bocetos de las interfaces de usuario.

#### Fase 3. Implementación (Codificación)
Es la fase en la que los desarrolladores de AzaharTech traducen los modelos y especificaciones de diseño a código fuente legible utilizando el lenguaje de programación elegido (**Java**). Se aplican estándares de estilo, control de versiones y buenas prácticas de ingeniería.

#### Fase 4. Pruebas y verificación (Testing)
El código escrito no puede entregarse al cliente sin someterse a pruebas rigurosas.
* Se ejecutan pruebas para detectar defectos de programación (*bugs*), fallos de seguridad y cuellos de botella de rendimiento.
* Se comprueba que el software cumple estrictamente con los requisitos definidos en la Fase 1.

#### Fase 5. Despliegue (Puesta en producción)
El software se instala en el entorno real del cliente: se configuran los servidores del IES El Caminàs, se instala la aplicación que proyectará en la pantalla del vestíbulo y se capacita al personal (profesorado de guardia y conserjería) en el uso de la herramienta.

#### Fase 6. Mantenimiento y evolución
Es la etapa más larga de todo el ciclo de vida (abarca años). Incluye cuatro vertientes de trabajo:
* **Mantenimiento correctivo:** Arreglar errores imprevistos que surgen durante el uso diario.
* **Mantenimiento adaptativo:** Modificar el software cuando cambia el entorno (por ejemplo, una actualización del sistema operativo de los servidores del centro educativo).
* **Mantenimiento perfectivo y evolutivo:** Añadir nuevas funcionalidades solicitadas por el instituto (por ejemplo, permitir que el profesorado fiche también las guardias de patio mediante la misma aplicación).

---

### 4. Lenguajes de programación, paradigmas y la Máquina Virtual de Java (JVM)

Para comunicarnos con el hardware, los desarrolladores escribimos texto en un archivo informático siguiendo las reglas sintácticas y semánticas de un lenguaje formal.

#### A. Niveles de abstracción del software
1. **Lenguaje máquina (Bajo nivel absoluto):** Es el único lenguaje que los circuitos electrónicos de la CPU comprenden directamente. Está compuesto exclusivamente por secuencias de dígitos binarios (`0` y `1`). Cada modelo de procesador (Intel x86, AMD, ARM) tiene su propio juego de instrucciones máquina incompatible con los demás.
2. **Lenguaje ensamblador (Bajo nivel simbólico):** Sustituye las cadenas binarias por códigos nemotécnicos legibles por humanos (`MOV`, `ADD`, `JMP`), pero sigue estando estrictamente acoplado a un procesador concreto.
3. **Lenguajes de alto nivel (Java, C#, Python):** Permiten al programador expresar algoritmos utilizando palabras en inglés (`class`, `public`, `if`, `while`) y conceptos matemáticos abstractos, independizándose del procesador físico sobre el que se ejecute la aplicación.

#### B. Paradigmas de programación
Un paradigma es la filosofía fundamental que determina cómo un desarrollador concibe y estructura la resolución de un problema mediante código:
* **Paradigma Imperativo / Estructurado:** El programa es una secuencia ordenada de pasos, instrucciones y llamadas a funciones que modifican el estado de las variables en memoria.
* **Paradigma Orientado a Objetos (POO):** El programa se concibe como una red de entidades independientes llamadas **objetos**, que encapsulan tanto sus características o datos internos (**atributos**) como las acciones o funciones que pueden realizar (**métodos**). En el caso de AzaharTech, modelaremos objetos como `Estudiante`, `TerminalQR`, `Fichaje` y `Incidencia`.

#### C. El modelo de ejecución de Java: Código fuente, Bytecode y la JVM
En los lenguajes tradicionales compilados (como C o C++), el compilador traduce el código fuente directamente a código máquina específico del sistema operativo. Esto obliga a recompilar el programa para Windows, para Linux y para macOS.

Java resolvió este problema introduciendo una capa intermedia de virtualización de software:

```
  [ Código Fuente ]          [ Compilador Java ]          [ Código Intermedio ]
     App.java       ───────►     (javac)       ───────►        App.class
  (Texto humano)                                               (Bytecode)
                                                                   │
                                                                   ▼
                                                       ┌───────────────────────┐
                                                       │ Máquina Virtual Java  │
                                                       │        (JVM)          │
                                                       └───────────┬───────────┘
                                                                   │
                              ┌────────────────────────────────────┼────────────────────────────────────┐
                              ▼                                    ▼                                    ▼
                    [ JVM para Windows ]                  [ JVM para Linux ]                   [ JVM para macOS ]
                              │                                    │                                    │
                              ▼                                    ▼                                    ▼
                     (Código Máquina)                     (Código Máquina)                     (Código Máquina)
```

1. El desarrollador escribe el código fuente en un archivo con extensión **`.java`**.
2. El compilador de Java (`javac`) traduce ese archivo a un lenguaje intermedio y universal llamado **Bytecode** (guardado en archivos con extensión **`.class`**).
3. La **Máquina Virtual de Java (Java Virtual Machine - JVM)** instalada en el ordenador del cliente lee ese Bytecode en tiempo real y lo traduce a instrucciones máquina específicas del procesador local.

> **El principio WORA:** Gracias a la JVM, se cumple el lema fundacional de Java: *«Write Once, Run Anywhere»* (Escribe una vez, ejecuta en cualquier parte). El software que desarrollemos para el IES El Caminàs funcionará exactamente igual en los ordenadores de gestión con Windows que en los servidores web del instituto con Linux.

---

### 5. Anatomía de un Entorno de Desarrollo Integrado (IDE)

Un programador que pretenda desarrollar software profesional utilizando únicamente un bloc de notas y ejecutando comandos manuales en la terminal perdería hasta el 80 % de su tiempo en tareas mecánicas y propensas a errores.

Un **Entorno de Desarrollo Integrado (IDE, por sus siglas en inglés *Integrated Development Environment*)** es una suite de software que centraliza en una única interfaz gráfica todas las herramientas requeridas para construir, depurar y probar aplicaciones con rapidez y fiabilidad:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                      ANATOMÍA DE UN IDE PROFESIONAL (INTELLIJ IDEA)                    │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ 1. Editor de código fuente avanzado:                                                  │
│    • Resaltado sintáctico de palabras clave, tipos y variables en colores.            │
│    • Autocompletado inteligente (IntelliSense) y plantillas de código vivas.           │
│    • Detección de errores léxicos y sintácticos en tiempo real mientras tecleas.      │
│                                                                                        │
│ 2. Gestor de compilación y construcción automática:                                    │
│    • Invocación transparente del compilador con un solo clic o atajo de teclado.       │
│    • Gestión de jerarquías de paquetes, librerías externas y dependencias (Maven).     │
│                                                                                        │
│ 3. Depurador interactivo (Debugger):                                                   │
│    • Capacidad de pausar la ejecución del programa en líneas concretas (breakpoints).  │
│    • Inspección en vivo del contenido de las variables en la memoria RAM.              │
│                                                                                        │
│ 4. Herramientas auxiliares y control de versiones integrado:                           │
│    • Conexión visual directa con Git y GitHub sin necesidad de comandos de consola.   │
│    • Terminal de comandos integrada, refactorización de código y linters automáticos. │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

En **AzaharTech** hemos estandarizado como IDE corporativo **IntelliJ IDEA Community Edition**, reconocido en el sector como el entorno más potente, ergonómico y extendido para el desarrollo en el ecosistema Java.

---

## PARTE II. LABORATORIO PRÁCTICO GUIADO (1 HORA): PUESTA A PUNTO DEL ENTORNO DE TRABAJO

### Caso de laboratorio
Alba Torres y Pau Ferrer acompañan al estudiante a su puesto de desarrollo:
> *«Bienvenido a tu estación técnica en AzaharTech. Durante los próximos sesenta minutos vamos a verificar que tu equipo cuenta con las herramientas oficiales de la empresa: instalaremos el compilador OpenJDK 21, configuraremos IntelliJ IDEA Community y crearemos nuestro primer programa Java para comprobar que todo el engranaje de compilación y ejecución funciona a la perfección»*.

---

### Procedimiento técnico paso a paso

#### Paso 1. Instalación y verificación del Java Development Kit (JDK 21)
El **JDK (Java Development Kit)** es el paquete indispensable que incluye el compilador (`javac`), las librerías estándar de Java y la Máquina Virtual (`java`).

1. Descarga e instala la distribución abierta y certificada **OpenJDK 21** (distribución recomendada: *Eclipse Temurin 21 (LTS)* o *Amazon Corretto 21*).
2. Durante el asistente de instalación, asegúrate de marcar las opciones para configurar automáticamente las variables de entorno del sistema (`JAVA_HOME` y adición al `PATH`).
3. Una vez finalizada la instalación, abre un terminal de comandos (*Símbolo del sistema / PowerShell* en Windows o *Terminal* en Linux/macOS).
4. Ejecuta las dos siguientes instrucciones de comprobación:
   ```bash
   java --version
   javac --version
   ```
5. **Verificación técnica obligatoria:** La consola del sistema debe devolver la información de versión del compilador y de la máquina virtual (por ejemplo: `openjdk 21.0.x 2026-xx-xx`). Si la terminal devuelve un error de tipo *«comando no reconocido»*, significa que la variable de entorno `PATH` no apunta correctamente a la carpeta `bin` del JDK.

---

#### Paso 2. Instalación y personalización de IntelliJ IDEA Community Edition
1. Descarga el instalador de **IntelliJ IDEA Community Edition** (versión de software libre).
2. Ejecuta el instalador marcando las siguientes opciones recomendadas:
    * *Create Desktop Shortcut* (Crear acceso directo en el escritorio).
    * *Add "bin" folder to the PATH* (Añadir carpeta binaria a las variables del sistema).
    * *Create Associations: .java* (Asociar archivos fuente Java al entorno).
3. Abre IntelliJ IDEA por primera vez:
    * Selecciona el tema visual de interfaz de trabajo (*Dark* o *Light*, según tu comodidad visual).
    * En la pantalla de bienvenida, dirígete a **Customize -> All settings...** (o *Settings* en Windows/Linux, *Preferences* en macOS).
    * En la barra de búsqueda escribe `File Encodings` y asegúrate de que **Global Encoding**, **Project Encoding** y **Default encoding for properties files** estén configurados en **`UTF-8`**. Esto evitará problemas con caracteres acentuados o símbolos de la lengua valenciana/castellana.

---

#### Paso 3. Configuración del SDK (Software Development Kit) en IntelliJ
1. En la ventana principal de IntelliJ IDEA, haz clic en el botón **New Project**.
2. En el panel izquierdo del asistente, selecciona **Java**.
3. En el desplegable denominado **JDK**, comprueba que IntelliJ detecta automáticamente la instalación de **OpenJDK 21**. Si aparece la opción *<No SDK>*, haz clic en *Add JDK...* y navega hasta el directorio donde se instaló el JDK en el Paso 1 (habitualmente `C:\Program Files\Eclipse Adoptium\jdk-21...` en Windows o `/usr/lib/jvm/...` en Linux).
4. Asigna como nombre temporal del proyecto: `VerificacionEntorno`.
5. Pulsa en **Create**.

---

#### Paso 4. Creación del primer programa ejecutable
1. En la barra lateral izquierda del IDE, despliega la carpeta del proyecto y localiza el directorio denominado **`src`** (código fuente).
2. Haz clic derecho sobre la carpeta `src` y selecciona: **New -> Java Class**.
3. Escribe como nombre de la clase: `HolaAzaharTech` y presiona *Enter*.
4. Observa cómo IntelliJ genera automáticamente la plantilla básica de la clase:
   ```java
   public class HolaAzaharTech {
   }
   ```
5. Escribe dentro de las llaves de la clase el método principal. Puedes utilizar el atajo de teclado inteligente de IntelliJ: escribe `main` y presiona la tecla *Tabulador* o *Enter*. El IDE autocompletará la línea por ti.
6. Dentro del método `main`, utiliza otro atajo inteligente: escribe `sout` y pulsa *Tabulador*. IntelliJ generará automáticamente la instrucción `System.out.println();`.
7. Completa el código para que quede exactamente de la siguiente forma:

```java
/**
 * Programa de verificación técnica de puesto de trabajo.
 * AzaharTech Software Consulting - Castellón de la Plana.
 */
public class HolaAzaharTech {
    public static void main(String[] args) {
        System.out.println("=================================================");
        System.out.println("   AZAHARTECH - CONSULTORÍA DE SOFTWARE          ");
        System.out.println("   Proyecto Guía: Control de Asistencia QR       ");
        System.out.println("   Cliente:       IES El Caminàs                 ");
        System.out.println("=================================================");
        System.out.println("Estado del entorno: OpenJDK 21 + IntelliJ OK.");
        System.out.println("Desarrollador/a: Puesto técnico verificado con éxito.");
    }
}
```

---

#### Paso 5. Compilación, ejecución y captura de evidencia oficial (Entregable ED-1)
1. Haz clic en el icono del triángulo verde situado a la izquierda de la línea `public class HolaAzaharTech` o pulsa el atajo de teclado universal **`Ctrl + Shift + F10`** (o `Shift + F10`).
2. Observa cómo en la parte inferior del IDE se abre automáticamente la ventana **Run**:
    * El IDE invoca a `javac` en segundo plano para compilar el código fuente a Bytecode.
    * La JVM ejecuta la clase y la salida formateada aparece en la consola sin advertencias ni errores.
3. **Generación de la evidencia técnica (Captura oficial):**
    * Ajusta la ventana de IntelliJ de manera que se visualice claramente el árbol de carpetas a la izquierda, el código fuente en el centro y la consola de ejecución con el mensaje de éxito en la parte inferior.
    * Realiza una captura de pantalla completa de tu monitor.
    * Guarda provisionalmente la imagen con el nombre exacto **`entorno.png`**. En la próxima sesión (Sesión 2 del viernes), crearemos la jerarquía de carpetas oficial y la subiremos al repositorio remoto de GitHub.

---

### Resumen de la Sesión 1
Al concluir estas dos horas:
* Comprendes la diferencia sustancial entre un programa aislado y un **Sistema de Información integral**.
* Conoces las seis etapas del **Ciclo de Vida del Software (SDLC)** y por qué el análisis previo ahorra costes en ingeniería.
* Entiendes cómo la combinación de **Bytecode y la JVM** dota a Java de portabilidad multiplataforma.
* Dispones en tu puesto de un entorno profesional plenamente configurado (**OpenJDK 21 + IntelliJ IDEA Community**) y has ejecutado con éxito tu primer programa Java.

**No, en la respuesta anterior únicamente redacté la Semana 1 — Sesión 1 (Lunes, 2 horas)** con ese nivel exhaustivo de detalle (teoría extensa más el laboratorio guiado).

Para completar las 3 semanas del **Sprint 1 de Entornos de Desarrollo (ED - 9 horas en total)** con este formato sesión a sesión, faltan las otras 5 sesiones:

* **Semana 1:**
    * ✅ *Sesión 1 (Lunes, 2 h): Fundamentos, SDLC, OpenJDK 21 e IntelliJ IDEA.* (Ya redactada en el mensaje anterior).
    * ⏳ **Sesión 2 (Viernes, 1 h):** *Metodología Scrum, estructura corporativa y primer repositorio en GitHub.*
* **Semana 2:**
    * ⏳ **Sesión 3 (Lunes, 2 h):** *Los 3 estados de Git, Git Diff y estándar de Conventional Commits.*
    * ⏳ **Sesión 4 (Viernes, 1 h):** *Documentación técnica con Markdown (`marco-scrum.md` y `README.md`).*
* **Semana 3:**
    * ⏳ **Sesión 5 (Lunes, 2 h):** *Ceremonias de cierre en Scrum e higiene técnica del repositorio (`.gitignore`).*
    * ⏳ **Sesión 6 (Viernes, 1 h):** *Versionado formal con Git Tags (`v0.1.0-sprint1`) y entrega oficial.*

---

Aquí tienes a continuación la **Sesión 2** para completar la primera semana:

---

# SEMANA 1 — SESIÓN 2 (Viernes, 18 de septiembre de 2026 — 1 hora lectiva)
### Bloque: Metodología ágil Scrum, estructura corporativa oficial y publicación del primer repositorio en GitHub
* **Distribución horaria:** 20 minutos de teoría metodológica + 40 minutos de taller práctico en GitHub.
* **Criterios de Evaluación vinculados:** RA1.g, RA2.b, RA4.f, RA4.h.

---

## PARTE I. SESIÓN TEÓRICA (20 MINUTOS): EL MARCO ÁGIL Y EL REPOSITORIO DIGITAL

### 1. Caso práctico narrativo
Es viernes por la mañana en **AzaharTech**. En las pantallas de los puestos de trabajo, los estudiantes tienen abierto IntelliJ IDEA con la clase `HolaAzaharTech.java` compilada el lunes anterior y la captura `entorno.png` guardada en el escritorio.

**Laia Claramunt** inicia la reunión de los viernes:

> *«El lunes dejamos nuestro taller físico a punto. Pero en la ingeniería del software actual, trabajar en un ordenador aislado no sirve de nada si el código no está versionado, protegido y compartido de forma transparente. Además, el IES El Caminàs necesita saber cómo vamos a avanzar semana a semana.*
>
> *En AzaharTech no utilizamos el viejo modelo en cascada donde el cliente no ve nada durante meses. Aplicamos **Scrum**: dividimos el trabajo en **sprints de 3 semanas**, y al final de cada sprint entregamos software que funciona. Hoy aprenderemos qué es Scrum y crearemos vuestro **primer repositorio en GitHub**, configurando la estructura de carpetas corporativa oficial que mantendremos durante todo el curso»*.

---

### 2. Fundamentos de Scrum y el Repositorio Digital

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                                EL FLUJO DE TRABAJO EN SCRUM                            │
├──────────────────────────┬─────────────────────────────┬───────────────────────────────┤
│ 1. Product Backlog       │ 2. Sprint Backlog           │ 3. Incremento de Software     │
├──────────────────────────┼─────────────────────────────┼───────────────────────────────┤
│ Lista global priorizada  │ Subconjunto de tareas que   │ Software 100% operativo,      │
│ de requisitos del cliente│ el equipo se compromete a   │ probado y potencialmente      │
│ (IES El Caminàs).        │ terminar en estas 3 semanas.│ desplegable en el cliente.    │
└──────────────────────────┴─────────────────────────────┴───────────────────────────────┘
```

#### A. Por qué Scrum frente al modelo en cascada (RA1.g)
En los proyectos de software, los requisitos del cliente cambian con frecuencia. El modelo tradicional (*en cascada*) exigía cerrar todos los documentos al principio; si el cliente cambiaba de idea meses después, el proyecto fracasaba.

**Scrum** es un marco de trabajo ágil e iterativo:
* El tiempo se divide en bloques fijos llamados **Sprints** (en nuestro curso, de **3 semanas**).
* En cada sprint se diseña, se programa, se prueba y se entrega una parte terminada del sistema (**Incremento**).
* **Los roles de AzaharTech:**
    * **Product Owner:** Representa las necesidades del cliente (equipo directivo del IES El Caminàs).
    * **Scrum Master (Laia Claramunt):** Vela por la metodología, elimina bloqueos y guía al equipo.
    * **Developers (Alba, Pau y el estudiante):** Profesionales responsables del diseño técnico, código y pruebas.

#### B. El Repositorio Remoto en GitHub como canal oficial de entregas (RA4.h)
Un **repositorio** es una base de datos gestionada por Git que almacena el historial completo de cambios de un proyecto.
* **Repositorio local:** Reside en el disco duro del estudiante.
* **Repositorio remoto (GitHub):** Reside en los servidores en la nube de GitHub. Funciona como el **buzón oficial de entregas**, garantizando la autoría de cada línea de código y permitiendo al docente calificar el avance en tiempo real.

---

## PARTE II. TALLER PRÁCTICO GUIADO (40 MINUTOS): ESTRUCTURA CORPORATIVA Y PRIMER PUSH

### Caso de laboratorio
Alba Torres y Pau Ferrer proyectan sus terminales:
> *«Vamos a crear la estructura oficial de carpetas de AzaharTech en vuestros puestos. Moveremos la captura de pantalla del lunes a su carpeta definitiva, configuraremos el archivo `.gitignore` para no subir basura y haremos nuestro primer **commit y push** a GitHub»*.

---

### Procedimiento técnico paso a paso

#### Paso 1. Creación de la estructura oficial de carpetas corporativa
1. Abre el explorador de archivos de tu sistema operativo o la terminal en tu directorio de trabajo personal.
2. Crea la jerarquía oficial de carpetas establecida para el curso:

```text
azahartech/
└── equipo-alfa/                        <-- Nombre de tu equipo asignado desde el día 1
    └── laia-claramunt/                 <-- Tus apellidos y nombre (sin espacios ni acentos)
        ├── README.md                   <-- Archivo de portada (lo editaremos en la Semana 2)
        ├── .gitignore                 <-- Archivo de exclusión de temporales
        ├── ed/                         <-- Módulo Entornos de Desarrollo
        │   └── docs/
        │       └── entorno.png         <-- Mueve aquí la captura guardada el lunes
        ├── pr/                         <-- Módulo Programación
        │   ├── pseudocodigo/
        │   └── src/
        └── pi/                         <-- Módulo Proyecto Intermodular
            └── docs/
```

3. Mueve el archivo `entorno.png` (generado en la Sesión 1) dentro del directorio recién creado: `ed/docs/entorno.png`.

---

#### Paso 2. Creación del archivo de exclusiones `.gitignore`
En los proyectos de desarrollo profesional **nunca se suben a GitHub los archivos compilados ni las carpetas privadas del IDE**.

1. Dentro de tu carpeta personal (`azahartech/equipo-alfa/tu-nombre/`), crea un archivo de texto con el nombre exacto **`.gitignore`** (con el punto al principio y sin extensión).
2. Ábrelo con IntelliJ o un editor de texto y añade las siguientes reglas de exclusión universales:

```text
# Exclusiones de IntelliJ IDEA
.idea/
*.iml
out/

# Exclusiones de compilación y Maven
target/
*.class

# Archivos del sistema operativo
.DS_Store
Thumbs.db
```

---

#### Paso 3. Vinculación de IntelliJ con tu cuenta de GitHub
1. Abre IntelliJ IDEA.
2. Ve al menú: **File -> Settings** (en Windows/Linux) o **IntelliJ IDEA -> Preferences** (en macOS).
3. En el buscador escribe **GitHub** (ubicado en *Version Control -> GitHub*).
4. Haz clic en el botón **+** (*Add account*) y selecciona **Log In via GitHub...**.
5. Se abrirá tu navegador web; autoriza la vinculación con JetBrains. Una vez aceptado, tu usuario y avatar aparecerán dentro de IntelliJ.

---

#### Paso 4. Inicialización del repositorio local y primer Commit convencional (RA4.f)
1. En el menú superior de IntelliJ IDEA, selecciona: **Git -> Create Git Repository...** (o *VCS -> Enable Version Control Integration*).
2. Selecciona la carpeta raíz de tu espacio personal (`azahartech/equipo-alfa/tu-nombre/`) y pulsa **OK**.
3. Abre el panel lateral **Commit** (`Alt + 0` o `Ctrl + K`):
    * Verás los archivos que acabas de estructurar (`.gitignore`, `ed/docs/entorno.png`, etc.).
    * Marca la casilla para incluir todos los archivos (*Stage*).
4. En el campo de mensaje de commit, escribe siguiendo el estándar formal de la industria:
   ```text
   feat: inicializar estructura corporativa oficial y verificar entorno con OpenJDK 21
   ```
5. Haz clic en el botón **Commit**.

---

#### Paso 5. Publicación en GitHub (*Push*) y verificación web (RA4.h)
1. En el menú superior de IntelliJ, selecciona: **Git -> GitHub -> Share Project on GitHub**.
2. Configura los parámetros de publicación:
    * **Repository Name:** `DAM-AzaharTech-Proyecto-TuNombre` (sustituyendo *TuNombre* por tu nombre real).
    * **Private:** Desmárcalo (debe ser público para que el docente pueda evaluarlo).
    * **Remote:** `origin`.
    * **Description:** *Repositorio corporativo de AzaharTech - 1.º DAM*.
3. Haz clic en **Share**. IntelliJ creará el repositorio remoto en los servidores de GitHub y subirá todos los archivos automáticamente.
4. Abre tu navegador web, entra en tu perfil de GitHub y accede al nuevo repositorio:
    * Comprueba que la carpeta `ed/docs/entorno.png` está subida y se visualiza la imagen correctamente.
    * Comprueba que el mensaje del commit aparece reflejado con el prefijo `feat:`.

---

### Resumen de la Semana 1 completada
Al término de estas dos primeras sesiones (3 horas lectivas):
* Has interiorizado los conceptos de **Sistema de Información**, **SDLC** y **Scrum**.
* Cuentas con un entorno profesional plenamente operativo (**OpenJDK 21 + IntelliJ IDEA Community**).
* Tu repositorio oficial en **GitHub** está publicado con la jerarquía corporativa de **AzaharTech**, completando el **Entregable ED-1**.

---

# MÓDULO PROFESIONAL: ENTORNOS DE DESARROLLO (ED)

## SPRINT 1. Ecosistema de desarrollo, marco ágil y repositorio digital (3 semanas | 9 horas)

---

# SEMANA 2 — SESIÓN 3 (Lunes, 21 de septiembre de 2026 — 2 horas lectivas)
### Bloque: Los tres estados de Git, inspección visual de diferencias (Git Diff) y estándar de Conventional Commits
* **Distribución horaria:** 1 hora de teoría conceptual y técnica + 1 hora de laboratorio práctico guiado en IntelliJ IDEA.
* **Criterios de Evaluación vinculados:** RA1.f, RA4.f, RA4.h.

---

## PARTE I. SESIÓN TEÓRICA (1 HORA): LA MECÁNICA INTERNA DE GIT Y LA TRAZABILIDAD

### 1. Caso práctico narrativo: El caos de los «cambios varios» en AzaharTech

Es lunes por la mañana en **AzaharTech**. En la pantalla de la sala de desarrollo, **Laia Claramunt** tiene abierto el panel de actividad de GitHub del proyecto del **IES El Caminàs**. Mientras revisa los registros del fin de semana, frunce el ceño.

Llama a **Pau Ferrer**, a **Alba Torres** y al estudiante:

> *«Mirad el registro de actividad de ayer a última hora. Hay tres confirmaciones seguidas de Pau con los siguientes mensajes: 'cambios', 'subiendo cosas que faltaban' y 'ahora sí que funciona'.*
>
> *Si mañana el jefe de estudios del IES El Caminàs nos llama porque la aplicación no arranca o introduce un error de cálculo, ¿alguien es capaz de saber en cuál de esos tres commits se rompió el código? Ninguno de nosotros. Hemos convertido un registro de ingeniería en una caja negra opaca.*
>
> *En una consultora profesional como AzaharTech, cada confirmación debe ser una **unidad atómica de cambio** perfectamente documentada. Para lograrlo, no podemos limitarnos a pulsar un botón sin entender qué ocurre por debajo: debemos dominar el viaje del archivo a través de los **tres estados de Git**, aprender a leer las **diferencias línea a línea (*Git Diff*)** y redactar mensajes bajo el estándar de la industria: **Conventional Commits**»*.

---

### 2. El modelo de datos de Git: Instantáneas frente a diferencias

La mayoría de los sistemas de control de versiones antiguos (como CVS o Subversion) almacenaban la información como una lista de archivos y los cambios de texto aplicados a cada uno a lo largo del tiempo (diferencias basadas en deltas).

**Git no funciona así.** Git concibe la información como un conjunto de **instantáneas completas (*snapshots*)** de un sistema de archivos en miniatura:

```
Versión 1: [ Archivo A ] [ Archivo B ] [ Archivo C ] ──► (Commit 1)
                                │
                                ▼ (Se modifica solo el archivo B)
Versión 2: [ Archivo A ] [ Archivo B' ] [ Archivo C ] ──► (Commit 2)
              (Enlace)                   (Enlace)
```

Cada vez que realizas un *commit*, Git «hace una fotografía» del estado exacto de todos los archivos del proyecto en ese momento y guarda una referencia a esa instantánea. Para ser eficiente, si un archivo no se ha modificado, Git no lo duplica: simplemente crea un enlace simbólico a la versión idéntica que ya tenía almacenada previamente.

---

### 3. Los tres estados locales de un archivo en Git

Para tener control absoluto sobre qué entra y qué no entra en cada fotografía del proyecto, Git divide tu espacio de trabajo local en **tres zonas lógicas**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                         EL VIAJE DE UN ARCHIVO EN EL FLUJO LOCAL                       │
├──────────────────────────┬─────────────────────────────┬───────────────────────────────┤
│ 1. Working Directory     │ 2. Staging Area (Index)     │ 3. Local Repository           │
│    (Directorio Trabajo)  │    (Área de Preparación)    │    (Repositorio Local)        │
├──────────────────────────┼─────────────────────────────┼───────────────────────────────┤
│ Archivos físicos en tu   │ Zona de preparación donde   │ Base de datos interna (.git/) │
│ disco duro que editas    │ seleccionas con precisión   │ donde la instantánea queda    │
│ en IntelliJ IDEA.        │ qué cambios irán al commit. │ registrada permanentemente.   │
└────────────┬─────────────┴──────────────┬──────────────┴───────────────┬───────────────┘
             │                            │                              │
             │     git add <archivo>      │     git commit -m "msg"      │
             │ ─────────────────────────► │ ───────────────────────────► │
             │      (Stage / Preparar)    │      (Confirmar / Grabar)    │
             │                            │                              │
             │ ◄───────────────────────── │ ◄─────────────────────────── │
             │    git restore / checkout  │         git reset            │
             │      (Descartar cambios)   │    (Deshacer confirmación)   │
```

#### Estado 1. Modificado (*Modified*) — En el Working Directory
Has editado un archivo existente (añadiendo código en Java o cambiando un texto en Markdown), pero todavía no has confirmado esos cambios ni los has preparado para la foto. Los cambios solo existen en tu editor.

#### Estado 2. Preparado (*Staged*) — En el Staging Area
Has seleccionado voluntariamente un archivo modificado y le has dicho a Git: *«Quiero que esta modificación concreta forme parte de mi próxima instantánea»*. El comando de consola equivalente es `git add <nombre-archivo>`.

> **¿Por qué existe el Staging Area?** Imagina que has trabajado durante una hora y has modificado dos cosas distintas: has corregido un cálculo matemático en `Reto1Calculo.java` y has retocado el diseño del `README.md`. No debes mezclarlos en un único commit. El Staging Area te permite preparar primero `Reto1Calculo.java`, hacer su commit específico de código, y después preparar `README.md` para hacer su commit de documentación.

#### Estado 3. Confirmado (*Committed*) — En el Local Repository
Los datos han quedado guardados de forma segura, comprimida y permanente en la base de datos interna de Git (la carpeta oculta `.git/` de tu proyecto). Cada commit genera un identificador alfanumérico único e irrepetible denominado **código hash SHA-1** (por ejemplo: `8f3a1b4c9e7...`), que actúa como la huella dactilar de esa versión.

---

### 4. Inspección de cambios: La lectura del *Git Diff*

Antes de preparar o confirmar un cambio, un ingeniero de software siempre inspecciona qué ha modificado mediante la herramienta **Git Diff** (comparador de diferencias).

El *diff* desglosa línea por línea las alteraciones exactas que ha sufrido el archivo:
* **Líneas eliminadas o reemplazadas:** Se resaltan visualmente en **rojo** (o precedidas por el signo `-`).
* **Líneas añadidas:** Se resaltan visualmente en **verde** (o precedidas por el signo `+`).
* **Líneas en blanco / contexto:** Muestran el código circundante para ubicar exactamente en qué método o bloque se ha producido la intervención.

```text
@@ -12,4 +12,5 @@ public class App {
     public static void main(String[] args) {
-        System.out.println("Sistema antiguo");
+        System.out.println("=== SISTEMA AZAHARTECH ===");
+        System.out.println("Terminal IES El Caminas v1.0");
     }
 }
```

Aprender a revisar el *diff* antes de hacer commit evita subir código experimental, comentarios personales o líneas que rompan la compilación del proyecto.

---

### 5. El estándar de calidad de la industria: *Conventional Commits*

Para erradicar los mensajes ambiguos en los equipos de desarrollo, se utiliza la especificación **Conventional Commits v1.0.0**, un estándar internacional que añade significado estructurado a los mensajes de confirmación:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        ESTRUCTURA DE UN CONVENTIONAL COMMIT                            │
├────────────────────────────────────────────────────────────────────────────────────────┤
│ <tipo>(<ámbito opcional>): <descripción concisa y directa en imperativo/presente>      │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

#### Catálogo de tipos oficiales utilizados en AzaharTech:
* **`feat:`** (de *feature*). Se utiliza exclusivamente cuando se introduce una nueva funcionalidad en el software (*ejemplo: `feat(pr): calcular desglose horario en modulo de tiempos`*).
* **`fix:`** Se emplea cuando se soluciona un error o defecto (*bug*) en el código fuente (*ejemplo: `fix(pr): corregir perdida de decimales en ratio de asistencia`*).
* **`docs:`** Modificaciones que afectan únicamente a la documentación técnica, diagramas o manuales Markdown (*ejemplo: `docs(pi): documentar mapa de actores del reto`*).
* **`style:`** Cambios visuales de formato que no alteran la lógica del programa: indentación, espacios en blanco, punto y coma omitidos (*ejemplo: `style(pr): formatear espaciado segun guia oficial de Java`*).
* **`refactor:`** Cambios en el código que ni corrigen un bug ni añaden una funcionalidad nueva, sino que mejoran su legibilidad o estructura (*ejemplo: `refactor(pr): renombrar variables de tiempos a nomenclatura camelCase`*).
* **`chore:`** Tareas auxiliares de configuración, mantenimiento de repositorios o actualización de herramientas que no modifican el código de producción (*ejemplo: `chore(git): anadir exclusion de temporales .DS_Store a gitignore`*).

#### Las cuatro reglas de oro del mensaje:
1. **Verbo en imperativo / presente:** Escribe *"añadir función"*, no *"añadida"* ni *"añadiendo"*.
2. **Minúsculas tras los dos puntos:** No empieces la descripción con mayúscula.
3. **Sin punto final:** Los mensajes de commit no llevan punto al final.
4. **Claridad atómica:** Un commit debe responder a la frase: *«Si aplico este commit, el proyecto ahora... [mensaje]»*.

---

## PARTE II. LABORATORIO PRÁCTICO GUIADO (1 HORA): DOMINIO DEL FLUJO GIT EN INTELLIJ IDEA

### Caso de laboratorio
Alba Torres y Pau Ferrer abren IntelliJ en sus pantallas:
> *«Vamos a realizar un entrenamiento práctico en vuestro entorno. Crearemos archivos de prueba, observaremos cómo cambian de estado en tiempo real dentro del IDE, inspeccionaremos las diferencias con la vista Diff de dos columnas y realizaremos commits atómicos con el estándar de Conventional Commits»*.

---

### Procedimiento técnico paso a paso

#### Paso 1. Localización del panel visual de Git en IntelliJ
1. Abre tu proyecto en **IntelliJ IDEA**.
2. En la barra lateral izquierda, localiza y haz clic sobre el icono **Commit** (icono vertical con una marca de verificación) o pulsa el atajo de teclado universal: **`Alt + 0`** (en Windows/Linux) o **`Cmd + 0`** (en macOS).
3. Verás la ventana de control de versiones dividida en dos secciones principales:
    * **Changes / Unstaged:** Archivos físicos que tienen cambios en tu disco duro pero que aún no están listos para la instantánea.
    * **Staged:** Archivos que ya forman parte del área de preparación.

---

#### Paso 2. Modificación de archivos y observación del estado *Modified*
1. Despliega tu carpeta `ed/docs/` y crea un archivo de texto plano provisional llamado `bitacora.txt`.
2. Escribe dentro la siguiente línea:
   ```text
   Registro de pruebas de Git - Sesion 3
   ```
3. Guarda el archivo (`Ctrl + S`).
4. Observa el panel **Commit** de IntelliJ:
    * El archivo aparece en color **rojo** (estado *Untracked* / sin seguimiento), lo que indica que Git ha detectado un archivo nuevo que no existía en el commit anterior.

---

#### Paso 3. Inspección visual con el visor Diff de dos columnas
1. Haz doble clic sobre el archivo `bitacora.txt` en el panel de Commit (o selecciónalo y pulsa `Ctrl + D`).
2. Se abrirá la herramienta **Diff Viewer** de IntelliJ:
    * A la izquierda verás la versión anterior del repositorio (vacía, porque el archivo no existía).
    * A la derecha verás tu versión actual en disco, con la línea resaltada en color **verde**.
3. Cierra la pestaña de diferencias y abre el archivo `.gitignore` que creaste en la Semana 1.
4. Añade al final del archivo una nueva línea de comentario:
   ```text
   # Exclusiones de archivos temporales de registro
   *.log
   ```
5. Abre el visor Diff sobre `.gitignore`: verás exactamente la línea que acabas de añadir en verde, mientras que el resto de reglas permanecen sin alteración.

---

#### Paso 4. Preparación selectiva (*Staging*) y descarte de cambios
1. En el panel de Commit de IntelliJ, selecciona únicamente el archivo `.gitignore` marcando su casilla de verificación.
2. Deja desmarcado el archivo `bitacora.txt`.
3. Esto equivale exactamente al comando de consola:
   ```bash
   git add .gitignore
   ```
   *El archivo `.gitignore` está ahora en el **Staging Area**, listo para su commit.*

---

#### Paso 5. Redacción del Commit Convencional y confirmación local
1. En el cuadro de texto inferior del panel de Commit (*Commit Message*), escribe:
   ```text
   chore(git): anadir regla de exclusion para archivos de registro log
   ```
2. Observa cómo IntelliJ analiza el mensaje: debe ser claro y no superar los 72 caracteres de longitud.
3. Haz clic en el botón **Commit** (no en *Commit and Push* todavía).
4. El archivo `.gitignore` desaparece de la lista de cambios y queda registrado de forma permanente en tu base de datos local de Git.

---

#### Paso 6. Descarte de cambios en el Directorio de Trabajo (*Rollback*)
1. Supongamos que el archivo `bitacora.txt` era solo una prueba temporal que no queremos mantener en el proyecto.
2. En IntelliJ, haz clic derecho sobre `bitacora.txt` en el panel de cambios y selecciona **Rollback...** (o pulsa `Ctrl + Alt + Z`).
3. Confirma la acción: IntelliJ eliminará los cambios del archivo o lo borrará si era un archivo sin seguimiento, devolviendo tu directorio de trabajo a un estado 100 % limpio.

---

#### Paso 7. Sincronización con el repositorio remoto (*Push*)
1. Ve al menú superior: **Git -> Push...** (o pulsa `Ctrl + Shift + K`).
2. Se abrirá un diálogo que te muestra la lista de commits locales pendientes de enviar a GitHub.
3. Comprueba que aparece tu commit con el mensaje `chore(git): anadir regla de exclusion...`.
4. Haz clic en el botón **Push**.
5. Abre tu navegador web, entra en tu repositorio de GitHub y comprueba que en el historial (*Commits*) aparece tu nueva confirmación impecablemente registrada.

---

### Resumen de la Sesión 3
Al concluir estas dos horas:
* Conoces el funcionamiento interno de Git mediante el modelo de instantáneas (*snapshots*).
* Dominas los **tres estados locales** (*Working Directory*, *Staging Area* y *Local Repository*).
* Sabes inspeccionar visualmente qué líneas añades o eliminas mediante la herramienta **Git Diff**.
* Aplicas con rigor profesional el estándar internacional de **Conventional Commits** para asegurar la trazabilidad del código.

---
---

# SEMANA 2 — SESIÓN 4 (Viernes, 25 de septiembre de 2026 — 1 hora lectiva)
### Bloque: Documentación técnica con Markdown (`.md`), memoria Scrum (ED-2) y panel de control en `README.md`
* **Distribución horaria:** 20 minutos de teoría de marcado + 40 minutos de redacción técnica guiada.
* **Criterios de Evaluación vinculados:** RA1.b, RA1.g, RA4.g.

---

## PARTE I. SESIÓN TEÓRICA (20 MINUTOS): EL ESTÁNDAR MARKDOWN EN LA INGENIERÍA DEL SOFTWARE

### 1. Caso práctico narrativo
Es viernes por la mañana en la sala técnica de **AzaharTech**. **Alba Torres** proyecta en la pantalla dos documentos técnicos: uno es un archivo `.docx` de Microsoft Word y el otro es un archivo `.md` de Markdown renderizado en GitHub:

> *«Fijaos en la diferencia. En el archivo Word, para ver qué ha cambiado entre dos versiones hay que descargarlo, tener el programa de pago instalado y aceptar revisiones manuales. En cambio, el archivo **Markdown (`.md`)** es texto plano universal. Git puede ver cada coma modificada línea por línea y GitHub lo maqueta con tipografía profesional automáticamente.*
>
> *En los proyectos de AzaharTech no aceptamos documentación viva en formatos binarios propietarios. Hoy aprenderemos la sintaxis de Markdown, redactaremos la **memoria técnica de nuestro marco Scrum (Entregable ED-2)** y transformaremos el archivo `README.md` en el panel de control del proyecto»*.

---

### 2. Sintaxis esencial de Markdown para documentación de software

Markdown fue creado para ser legible en su forma de texto plano sin procesar y convertirse limpiamente en documentos maquetados.

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        GUÍA DE SINTAXIS ESENCIAL DE MARKDOWN                           │
├───────────────────────────────┬────────────────────────────────────────────────────────┤
│ Elemento                      │ Sintaxis en texto plano                                │
├───────────────────────────────┼────────────────────────────────────────────────────────┤
│ Encabezado principal (H1)     │ # Título del Proyecto                                  │
│ Encabezado secundario (H2)    │ ## 1. Marco Metodológico                               │
│ Subapartado (H3)              │ ### 1.1 Fases del Ciclo de Vida                        │
│ Texto en negrita              │ **Texto en negrita para conceptos clave**              │
│ Texto en cursiva              │ *Texto en cursiva o términos en inglés*                │
│ Lista no ordenada             │ * Elemento de lista (o guion -)                        │
│ Lista de tareas (Checklist)   │ - [ ] Tarea pendiente / - [x] Tarea completada         │
│ Bloque de código con sintaxis │ ```` ```java ```` seguido del código y ```` ``` ````   │
│ Cita o nota importante        │ > Nota técnica o advertencia                           │
└───────────────────────────────┴────────────────────────────────────────────────────────┘
```

#### Tablas en Markdown:
Las tablas se construyen mediante barras verticales (`|`) y una línea divisoria de guiones (`---`):
```markdown
| Identificador | Tarea del Sprint | Módulo Responsable | Estado |
| :--- | :--- | :---: | :---: |
| T-01 | Configuración OpenJDK 21 | ED | Completada |
| T-02 | Reto 1 en Java | PR | En curso |
```
*(Los dos puntos `:` determinan la alineación: `:---` izquierda, `:---:` centrada, `---:` derecha).*

---

## PARTE II. TALLER PRÁCTICO GUIADO (40 MINUTOS): REDACCIÓN DE ED-2 Y PANEL README

### Procedimiento técnico paso a paso

#### Paso 1. Redacción de la Memoria Técnica Scrum: `ed/docs/marco-scrum.md` (Entregable ED-2)
1. En tu proyecto de IntelliJ, navega hasta la carpeta `ed/docs/`.
2. Haz clic derecho sobre `docs`: **New -> File** y nómbralo exactamente: `marco-scrum.md`.
3. Redacta el documento técnico formal aplicando la sintaxis Markdown y adaptándolo a **tu proyecto elegido de la bolsa de proyectos**:

```markdown
# Memoria Técnica: Marco Metodológico y Ciclo de Vida del Software
**Consultora:** AzaharTech Software Consulting  
**Proyecto:** [Nombre de tu Proyecto Elegido de la Bolsa de Proyectos]  
**Desarrollador/a:** [Tus Apellidos, Tu Nombre]  
**Fecha:** 25 de septiembre de 2026  
**Versión:** 1.0 (Sprint 1)  

---

## 1. Justificación del Modelo de Proceso: Cascada vs. Scrum
Para el desarrollo de este proyecto se descarta el modelo tradicional en cascada debido a su rigidez ante los cambios de requisitos y a la dilatación en la entrega de resultados tangibles al cliente.

Se adopta el marco de trabajo ágil **Scrum**, estructurado en ciclos iterativos de desarrollo (**Sprints de 3 semanas**). Al finalizar cada sprint, el equipo entrega un **Incremento de Software potencialmente desplegable**, permitiendo que el cliente valide el producto de forma continua y minimizando el riesgo de desviación temporal o económica.

---

## 2. Aplicación de las Fases del Ciclo de Vida del Software (SDLC)
En cada ciclo de sprint se ejecutan de forma coordinada las fases de la ingeniería del software adaptadas a nuestro sistema:

1. **Análisis:** Identificación de las necesidades del cliente y especificación de historias de usuario con criterios de aceptación claros.
2. **Diseño:** Modelado de la arquitectura de la solución, diagramas funcionales en Proyecto Intermodular y definición de estructuras de datos.
3. **Codificación:** Implementación de los algoritmos en lenguaje Java (OpenJDK 21) utilizando el entorno integrado IntelliJ IDEA Community.
4. **Pruebas (Testing):** Verificación de casos límite, validación de trazas de memoria e inspección visual con depurador.
5. **Despliegue:** Empaquetado y publicación de las entregas formales mediante etiquetas de versión (*Git Tags*) en el repositorio de GitHub.
6. **Mantenimiento:** Refactorización continua del código y corrección de incidencias detectadas en revisiones de sprint.

---

## 3. Organización y Roles en la Célula de Trabajo
* **Product Owner:** Representa los intereses del cliente de nuestro proyecto, priorizando los requisitos en el *Product Backlog*.
* **Scrum Master (Laia Claramunt):** Supervisa el cumplimiento de los tiempos de entrega, elimina bloqueos técnicos y vela por la calidad metodológica.
* **Developer (El estudiante):** Responsable técnico del diseño algorítmico, implementación en Java, control de versiones en Git y documentación técnica.
```

---

#### Paso 2. Transformación del `README.md` en Panel de Control del Proyecto
1. Abre el archivo `README.md` situado en la raíz de tu carpeta personal (`azahartech/equipo-alfa/tu-nombre/README.md`).
2. Reemplaza su contenido para que actúe como la tarjeta de presentación oficial de tu espacio de trabajo:

```markdown
# Sistema de Gestión: [Nombre de Tu Proyecto Propio]
**Consultora de Desarrollo:** AzaharTech (Castellón de la Plana)  
**Cliente:** [Nombre del Cliente de la Bolsa de Proyectos]  
**Desarrollador/a:** [Tus Apellidos, Tu Nombre]  
**Célula / Equipo:** [Nombre de tu equipo asignado]  

---

## 📌 Alcance del Proyecto
Desarrollo de una solución informática multiplataforma para optimizar los procesos operativos y de control de nuestro cliente, integrando lógica de procesamiento en Java, gestión ágil de tareas y documentación técnica viva.

---

## 🛠️ Taller Tecnológico
* **Lenguaje:** Java (OpenJDK 21 LTS)
* **Entorno Integrado (IDE):** IntelliJ IDEA Community Edition
* **Control de Versiones:** Git 2.x & GitHub
* **Estándar Documental:** Markdown

---

## 📋 Sprint Backlog 1 (14 sep - 2 oct) — Estado de Avance

### Módulo: Entornos de Desarrollo (ED)
- [x] Configuración de OpenJDK 21 e IntelliJ IDEA Community (`ed/docs/entorno.png`)
- [x] Elaboración de memoria técnica de marco Scrum y ciclo de vida (`ed/docs/marco-scrum.md`)
- [x] Práctica del flujo de Git de 3 estados y estándar Conventional Commits
- [ ] Auditoría de limpieza e higiene con `.gitignore` (Semana 3)
- [ ] Etiquetado formal de release `v0.1.0-sprint1` (Semana 3)

### Módulo: Programación (PR)
- [x] Declaración de variables y tipos primitivos de datos
- [x] Operadores aritméticos, expresiones y conversiones de tipo (*casting*)
- [ ] Algoritmo secuencial completo del Reto 1 integrado (Semana 3)

### Módulo: Proyecto Intermodular (PI)
- [x] Documento de especificación del reto y registro ético de IA (`pi/docs/analisis-reto.md`)
- [x] Diagrama de bloques funcional y viabilidad técnica (`pi/docs/viabilidad-tecnica.md`)
- [ ] Dossier técnico final y preparación de la Demo v0.1 (Semana 3)
```

---

#### Paso 3. Confirmación atómica y sincronización con GitHub
1. Abre el panel **Commit** en IntelliJ IDEA (`Alt + 0`).
2. Selecciona los dos archivos modificados: `ed/docs/marco-scrum.md` y `README.md`.
3. Escribe un mensaje de confirmación convencional riguroso:
   ```text
   docs(ed): redactar memoria tecnica de marco scrum y actualizar panel README
   ```
4. Haz clic en **Commit and Push**.
5. Accede a tu repositorio en GitHub desde el navegador:
    * Comprueba que el `README.md` se muestra maquetado en la portada con sus casillas de verificación activas.
    * Entra en `ed/docs/marco-scrum.md` y comprueba que la memoria de Scrum se visualiza con títulos limpios, tablas y negritas.

---

### Resumen de la Semana 2 completada
Al concluir estas dos sesiones (3 horas lectivas):
* Dominas los **tres estados de Git** y la inspección de cambios con **Git Diff**.
* Aplicas de forma sistemática el estándar de **Conventional Commits** en tus mensajes.
* Conoces y aplicas la sintaxis universal de **Markdown**.
* Has completado el **Entregable ED-2 (`ed/docs/marco-scrum.md`)** y tu repositorio cuenta con un panel de control profesional en su `README.md`.

**Todavía no: falta la Semana 3 (las sesiones 5 y 6)** para completar al 100 % el Sprint 1 de Entornos de Desarrollo.

Hasta ahora llevamos:
* ✅ **Semana 1 (Sesiones 1 y 2 — 3 h):** Fundamentos, SDLC, OpenJDK 21, IntelliJ IDEA y primer repositorio en GitHub.
* ✅ **Semana 2 (Sesiones 3 y 4 — 3 h):** Tres estados de Git, Conventional Commits, sintaxis Markdown, `marco-scrum.md` y `README.md`.
* ⏳ **Semana 3 (Sesiones 5 y 6 — 3 h):** *Las desarrollamos a continuación para cerrar definitivamente el Sprint 1.*

---

# SEMANA 3 — SESIÓN 5 (Lunes, 28 de septiembre de 2026 — 2 horas lectivas)
### Bloque: Ceremonias de cierre ágil (Sprint Review y Retrospective) e higiene técnica del repositorio (`.gitignore`)
* **Distribución horaria:** 1 hora de teoría conceptual y metodológica + 1 hora de laboratorio práctico de auditoría y limpieza en IntelliJ IDEA.
* **Criterios de Evaluación vinculados:** RA1.g, RA4.f, RA4.h.

---

## PARTE I. SESIÓN TEÓRICA (1 HORA): EL CIERRE DEL SPRINT Y LA CALIDAD DEL REPOSITORIO

### 1. Caso práctico narrativo: La recta final del Sprint 1 en AzaharTech

Es lunes 28 de septiembre por la mañana. Entramos en la última semana del primer ciclo de desarrollo. En la sala de reuniones de **AzaharTech**, **Pau Ferrer** y **Alba Torres** revisan el código de sus respectivos módulos con cierta euforia: el programa compila, las fórmulas matemáticas funcionan y los archivos están redactados en Markdown.

Pau comenta entusiasmado:
> *«Por mi parte el Sprint 1 está terminado: ya he subido todo lo que tenía en mi ordenador a GitHub»*.

**Laia Claramunt**, con su experiencia como Scrum Master, sonríe y niega con la cabeza:
> *«En el desarrollo de software profesional, un sprint no concluye cuando el programador termina de escribir la última línea de código. Eso es solo la mitad del trabajo.*
>
> *En Scrum, el final de un sprint exige ejecutar dos ceremonias obligatorias: la **Sprint Review**, donde demostramos al cliente el software que funciona y recibimos su validación, y la **Sprint Retrospective**, donde nos miramos al espejo como equipo para detectar qué fallos de organización hemos tenido y cómo los resolveremos en el Sprint 2.*
>
> *Además, antes de presentar nada al cliente, debemos realizar una **auditoría de higiene técnica sobre nuestro repositorio**. Si abro vuestro GitHub y encuentro archivos compilados `.class`, carpetas temporales de IntelliJ o rutas rotas, el trabajo no se considerará entregado. Un desarrollador junior solo se fija en si el código compila; un profesional se asegura de que el repositorio esté impecable»*.

---

### 2. Las ceremonias de cierre del Sprint: Review frente a Retrospective

En los modelos predictivos clásicos (*modelo en cascada*), los proyectos terminaban con una entrega masiva y fría de documentos al cabo de meses. Scrum introduce dos eventos de inspección y adaptación al término de cada ciclo de 3 semanas:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        COMPARATIVA DE LAS CEREMONIAS DE CIERRE                         │
├───────────────────────────────┬────────────────────────────────────────────────────────┤
│ SPRINT REVIEW (Revisión)      │ SPRINT RETROSPECTIVE (Retrospectiva)                   │
├───────────────────────────────┼────────────────────────────────────────────────────────┤
│ • Participan: Equipo + Cliente│ • Participan: Únicamente el equipo de desarrollo       │
│ • Pregunta: ¿QUÉ se ha hecho? │ • Pregunta: ¿CÓMO hemos trabajado juntos?              │
│ • Foco: El producto / software│ • Foco: Los procesos, las personas y las herramientas   │
│ • Salida: Feedback del cliente│ • Salida: Compromisos de mejora interna para el Sprint 2│
└───────────────────────────────┴────────────────────────────────────────────────────────┘
```

#### A. La Revisión del Sprint (*Sprint Review*)
* **Objetivo:** Inspeccionar el **Incremento de Software** completado durante las 3 semanas y adaptar el *Product Backlog* según las impresiones del cliente.
* **Dinámica:** El equipo realiza una demostración en vivo del software funcionando (la clase `App.java` del caso guía o de su proyecto propio). Se explican las decisiones tomadas y se verifica si se ha cumplido la **Definición de Hecho (*Definition of Done - DoD*)**.
* **El valor del feedback:** El cliente puede solicitar ajustes para el siguiente sprint (*ejemplo: «necesitamos que el tiempo de espera no supere los 5 segundos»*), los cuales se incorporan de forma natural al backlog del Sprint 2.

#### B. La Retrospectiva del Sprint (*Sprint Retrospective*)
* **Objetivo:** Analizar la salud operativa del equipo de desarrollo, identificando qué prácticas han funcionado y qué obstáculos técnicos o humanos han frenado el ritmo de trabajo.
* **El método de las 3 columnas de AzaharTech:**
    1. **Mantener (*Keep*):** Buenas prácticas consolidadas (*ej. diseñar en PSeInt antes de picar Java ahorró errores; los commits convencionales facilitaron rastrear cambios*).
    2. **Detener (*Stop*):** Prácticas perjudiciales que deben erradicarse (*ej. dejar la redacción de la memoria Markdown para la última noche; hacer commits gigantes con 15 archivos a la vez*).
    3. **Comenzar (*Start*):** Nuevos hábitos o herramientas a incorporar en el Sprint 2 (*ej. revisar los mensajes de commit con los compañeros antes de hacer push; configurar atajos de teclado en el IDE*).

---

### 3. Principios de higiene técnica: El control estricto de `.gitignore`

Uno de los errores más graves que comete un desarrollador novel al utilizar Git es confundir un **repositorio de código fuente** con una **carpeta compartida en la nube** (como Google Drive o Dropbox).

#### ¿Por qué jamás deben subirse archivos binarios o temporales al repositorio?
1. **Contaminación del historial:** Los archivos compilados (`.class`, `.jar`, `.exe`) son binarios que cambian por completo cada vez que recompilas. Si los subes a Git, la base de datos del repositorio aumentará de tamaño de forma exponencial con información inútil.
2. **Conflictos entre sistemas operativos:** Si subes la carpeta oculta `.idea/` o los archivos de configuración local de tu ordenador con Windows, cuando otro compañero (o el profesor que califica en Linux/macOS) clone el repositorio, el proyecto colapsará porque las rutas de disco no coincidirán.
3. **Seguridad:** Los archivos temporales pueden contener credenciales de sesión, rutas absolutas privadas de tu disco duro o cachés del sistema.

```
 [ REPOSITORIO PROFESIONAL LIMPIO ]             [ REPOSITORIO CONTAMINADO ]
 ├── ed/docs/marco-scrum.md   (Texto editable)   ├── ed/docs/marco-scrum.md
 ├── pr/src/App.java          (Código fuente)    ├── pr/src/App.java
 └── .gitignore               (Reglas activas)   ├── pr/src/App.class   ◄── [ERROR GRAVE: Binario]
                                                 ├── out/production/... ◄── [ERROR GRAVE: Salida]
                                                 └── .idea/workspace.xml◄── [ERROR GRAVE: Privado]
```

#### El archivo `.gitignore`: El guardián de la limpieza
El archivo `.gitignore` situado en la raíz del proyecto le indica al motor de Git qué archivos y directorios debe **ignorar por completo**, impidiendo que aparezcan en el *Staging Area* aunque se modifiquen en el disco duro.

* **Patrones y comodines estándar:**
    * `*.class`: Ignora cualquier archivo que termine con esa extensión, en cualquier subcarpeta.
    * `out/`: Ignora la carpeta de compilación de IntelliJ y todo su contenido.
    * `.idea/`: Ignora las configuraciones privadas del IDE.
    * `.DS_Store`: Ignora los archivos de previsualización ocultos de macOS.

---

## PARTE II. LABORATORIO PRÁCTICO GUIADO (1 HORA): AUDITORÍA DE HIGIENE Y PURGA DEL REPOSITORIO

### Caso de laboratorio
Alba Torres y Pau Ferrer abren la terminal integrada de IntelliJ:
> *«Ha llegado el momento de auditar nuestro repositorio antes de la entrega formal. Vamos a comprobar el estado de los archivos con Git, aprenderemos a eliminar del seguimiento cualquier archivo temporal que se haya colado por accidente y consolidaremos el archivo `.gitignore` definitivo de AzaharTech»*.

---

### Procedimiento técnico paso a paso

#### Paso 1. Auditoría de archivos mediante la terminal integrada
1. Abre tu proyecto en **IntelliJ IDEA**.
2. Abre la terminal de comandos integrada en el IDE pulsando el atajo **`Alt + F12`** (en Windows/Linux) o **`Option + F12`** (en macOS).
3. Ejecuta el comando de diagnóstico general:
   ```bash
   git status
   ```
4. **Comprobación:** La terminal debe indicar:
    * Que estás en la rama principal (`On branch main` o `master`).
    * Qué archivos están modificados pendientes de commit o si el árbol de trabajo está limpio (*«nothing to commit, working tree clean»*).
5. Ejecuta ahora el siguiente comando avanzado para listar **absolutamente todos los archivos que Git está rastreando en este momento**:
   ```bash
   git ls-files
   ```
6. Revisa la lista resultante en pantalla:
    * Solo deben aparecer archivos fuente y de documentación: `.gitignore`, `README.md`, archivos `.md`, imágenes `.png`, archivos `.psc` y archivos `.java`.
    * **⚠️ Alerta roja:** Si en esa lista aparece algún archivo que termine en `.class` o comience por `.idea/` u `out/`, ese archivo ha sido rastreado por error y debe purgarse de inmediato.

---

#### Paso 2. Procedimiento de purga de archivos no deseados (`git rm --cached`)
Si durante las semanas anteriores cometiste el error de confirmar un archivo `.class` o la carpeta `.idea/` antes de configurar el `.gitignore`, el archivo seguirá rastreado aunque añadas la regla después.

Para eliminarlo del historial de Git **sin borrar el archivo físico de tu disco duro**, ejecuta los siguientes comandos según el caso:

* Si se ha colado un archivo `.class`:
  ```bash
  git rm --cached pr/src/*.class
  ```
* Si se ha colado la carpeta de compilación `out/`:
  ```bash
  git rm -r --cached out/
  ```
* Si se ha colado la carpeta privada del IDE `.idea/`:
  ```bash
  git rm -r --cached .idea/
  ```

---

#### Paso 3. Consolidación de las reglas universales en `.gitignore`
1. Abre el archivo `.gitignore` situado en la raíz de tu proyecto en IntelliJ IDEA.
2. Comprueba que contiene exactamente las siguientes directivas de exclusión estructuradas por bloques:

```text
# ====================================================================
# EXCLUSIONES OFICIALES DE AZAHARTECH SOFTWARE
# ====================================================================

# 1. Entorno de Desarrollo (IntelliJ IDEA)
.idea/
*.iml
*.iws
*.ipr
out/

# 2. Compiladores y herramientas de construcción (Java / Maven)
*.class
target/
build/
*.jar
*.war

# 3. Archivos del Sistema Operativo
.DS_Store
Thumbs.db
desktop.ini

# 4. Archivos de registro y temporales
*.log
*.tmp
*.bak
```

3. Guarda los cambios en el archivo (`Ctrl + S`).

---

#### Paso 4. Commit de mantenimiento y sincronización con GitHub
1. Abre el panel lateral **Commit** de IntelliJ (`Alt + 0` o `Ctrl + K`).
2. Comprueba que el archivo `.gitignore` (y las eliminaciones del índice si hubo purga) están seleccionados en el área de preparación (*Stage*).
3. Redacta el mensaje de confirmación siguiendo el estándar convencional:
   ```text
   chore(git): auditar higiene del repositorio y consolidar reglas de gitignore
   ```
4. Haz clic en **Commit and Push**.
5. Abre el navegador web, entra en tu repositorio de GitHub y comprueba:
    * Que en la raíz no existe ninguna carpeta `.idea/` ni `out/`.
    * Que dentro de `pr/src/` solo existen los archivos fuente `.java`, sin ningún binario `.class`.

---

### Resumen de la Sesión 5
Al concluir estas dos horas:
* Conoces la finalidad e importancia de las ceremonias de **Sprint Review** y **Sprint Retrospective** en Scrum.
* Comprendes por qué un repositorio profesional solo almacena código fuente editable y documentación.
* Has auditado con comandos de consola (`git status`, `git ls-files`) el contenido exacto de tu proyecto.
* Tu repositorio en GitHub cumple los estándares de higiene técnica más exigentes gracias a la configuración del `.gitignore`.

---
---

# SEMANA 3 — SESIÓN 6 (Viernes, 2 de octubre de 2026 — 1 hora lectiva)
### Bloque: Versionado formal con Git Tags (`v0.1.0-sprint1`), cierre del Sprint Backlog 1 y entrega final del sprint
* **Distribución horaria:** 20 minutos de teoría de versionado semántico + 40 minutos de etiquetado y entrega oficial.
* **Criterios de Evaluación vinculados:** RA4.f, RA4.h.

---

## PARTE I. SESIÓN TEÓRICA (20 MINUTOS): VERSIONADO SEMÁNTICO Y ETIQUETAS (*TAGS*)

### 1. Caso práctico narrativo
Es viernes 2 de octubre por la mañana. Las tres semanas del Sprint 1 concluyen hoy formalmente. **Laia Claramunt** reúne por última vez en este sprint a toda la célula de desarrollo de AzaharTech:

> *«Equipo, el lunes que viene comenzaremos el Sprint 2: modificaremos clases en Programación, cambiaremos la configuración de Maven en Entornos de Desarrollo y ampliaremos la documentación.*
>
> *Si el docente o el cliente entran dentro de dos semanas a nuestro repositorio para evaluar el Sprint 1, ¿cómo sab