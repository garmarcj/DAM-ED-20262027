# Sprint 3. El ciclo del error: pruebas y depuración

---

## Semana 7. El depurador interactivo: puntos de ruptura (breakpoints), ejecución paso a paso e inspección de memoria

---

### Día 13 - 2 sesiones

---

#### Sesión 1. Teoría. La radiografía del código en ejecución: del print casero a la depuración interactiva

---

#### 1. Caso guía en AzaharTech

Son las tres de la tarde del lunes 26 de octubre de 2026. En la sede de **AzaharTech** en Castellón de la Plana, arranca formalmente la primera jornada del **Sprint 3**.

Tras haber consolidado la gestión de paquetes, compilación y dependencias con Maven en el Sprint anterior, el equipo se reúne en la sala principal. En la pantalla de proyección, **Laia Claramunt** muestra una captura del panel de incidencias enviado de urgencia a primera hora de la mañana por el coordinador TIC del **IES El Caminàs**:

> *«Equipo, entramos de lleno en el corazón de la lógica de programación. Durante estas semanas, en el módulo de Programación estáis implementando estructuras de control complejas: bucles anidados (`for`, `while`), bifurcaciones condicionales (`if`/`else`, `switch`) y control de excepciones.*
>
> *La aplicación del IES El Caminàs ya cuenta con su primer prototipo de validación de códigos QR en bucle continuo para procesar la cola de alumnos en el vestíbulo. Sin embargo, esta mañana el sistema ha entrado en un bloqueo total: tras procesar al décimo alumno, la aplicación se congela consumiendo el 100 % de la CPU de los terminales de conserjería, o bien asigna faltas injustificadas a estudiantes que sí habían registrado el código.*
>
> *El impulso natural de un programador principiante es llenar el código de sentencias `System.out.println("Pasa por aquí 1")`, `System.out.println("Variable x = " + x)` y `System.out.println("Pasa por aquí 2")`. En la industria del software profesional, esa práctica es un antipatrón ineficiente, peligroso y que ensucia el repositorio.*
>
> *Tanto en el caso del IES El Caminàs como en los proyectos singulares de vuestros equipos (**Aventura conversacional**, **Motor de recomendación**, **Simulador de físicas 2D** o **Bóveda de contraseñas**), hoy aprenderemos a congelar el tiempo: usaremos el* *depurador interactivo (Debugger)* *de IntelliJ IDEA para pausar la ejecución de la JVM, examinar la memoria RAM en vivo y alterar el comportamiento del programa sin tocar una sola línea de código fuente»*.

---

#### 2. La naturaleza de los errores y las limitaciones del «Print Debugging»

En el ciclo de vida del software, encontrar errores no es una anomalía, sino una certeza estadística. Sin embargo, para erradicarlos con rigor técnico, primero debemos categorizarlos:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                          TAXONOMÍA DE ERRORES EN EL SOFTWARE                           │
├─────────────────────┬─────────────────────────────────┬────────────────────────────────┤
│ Tipo de error       │ ¿Cuándo se manifiesta?          │ Comportamiento / Detección     │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 1. De compilación   │ Durante la fase de construcción │ El compilador (javac) detecta  │
│    (Sintaxis/Tipos) │ con Maven o en el propio IDE.   │ faltas sintácticas y no genera │
│                     │                                 │ el archivo .class.             │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 2. De ejecución     │ Con el programa ya en marcha    │ La JVM interrumpe el flujo     │
│    (Runtime / Crash)│ dentro de la máquina virtual.   │ lanzando una excepción fatal   │
│                     │                                 │ (NullPointerException, etc.).  │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 3. Lógico           │ El programa compila y corre sin │ El software hace exactamente   │
│    (Semántico)      │ romperse, pero da un resultado  │ lo que se escribió, pero no lo │
│                     │ funcional incorrecto o cuelgue. │ que el negocio requería.       │
└─────────────────────┴─────────────────────────────────┴────────────────────────────────┘
```

##### El problema del «Print Debugging»
Llenar el código con instrucciones de consola para rastrear variables presenta graves desventajas de ingeniería:
1. **Contaminación de código:** Obliga a modificar el archivo fuente, rompiendo la trazabilidad limpia de commits en Git.
2. **Coste temporal:** Requiere recompilar con Maven y relanzar la JVM cada vez que queremos observar una variable diferente.
3. **Pérdida de contexto:** Un `System.out.println` muestra una foto aislada en texto plano, pero oculta la pila de llamadas (*Call Stack*), los objetos en segundo plano y el estado global de la memoria.
4. **Riesgo en producción:** Olvidar trazas de depuración en despliegues reales puede filtrar contraseñas, tokens y saturar los ficheros de log de la empresa.

```
COMPARATIVA METODOLÓGICA:

A) Depuración primitiva (Print Debugging):
   [Modificar código] ──► [Recompilar] ──► [Ejecutar] ──► [Leer consola] ──► [Borrar print]
   (Ciclo lento, destructivo y ciego ante bucles complejos)

B) Depuración interactiva profesional (IDE Debugger):
   [Poner Breakpoint] ──► [Lanzar Debug] ──► [Congelar JVM] ──► [Inspeccionar / Modificar RAM]
   (Sin tocar código, inspección multidimensional e inmediata)
```

---

#### 3. Anatomía del depurador interactivo en IntelliJ IDEA

Cuando lanzamos nuestro software mediante el botón de depuración (**Debug** o `Shift + F9`), IntelliJ no invoca a la JVM de forma estándar: la arranca comunicándose mediante el protocolo **JDWP** (*Java Debug Wire Protocol*). Esto permite al IDE tomar el control absoluto del hilo de ejecución.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   MANDOS DEL DEPURADOR EN INTELLIJ IDEA                │
├──────────────┬──────────────┬──────────────────────────────────────────┤
│ Acción       │ Atajo        │ Propósito técnico                        │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Over    │ F8           │ Avanza a la siguiente línea del método   │
│              │              │ actual sin entrar dentro de funciones.   │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Into    │ F7           │ Entra al interior del método invocado    │
│              │              │ en la línea actual para ver su lógica.   │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Out     │ Shift + F8   │ Sale del método actual y regresa a la    │
│              │              │ línea del método llamador.               │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Resume       │ F9           │ Reanuda la ejecución libre hasta topar   │
│ Program      │              │ con el siguiente punto de ruptura.       │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Evaluate     │ Alt + F8     │ Abre una consola para evaluar fórmulas y │
│ Expression   │              │ métodos en caliente contra la RAM real.  │
└──────────────┴──────────────┴──────────────────────────────────────────┘
```

##### Puntos de ruptura (*Breakpoints*)
* **Punto de ruptura estándar:** Se activa haciendo clic en el canal lateral izquierdo (*gutter*) junto al número de línea. Marca una línea donde la JVM suspenderá el hilo de ejecución justo **antes** de procesarla.
* **Punto de ruptura condicional:** Clic derecho sobre el punto rojo del breakpoint. Permite definir una expresión lógica booleana en Java (por ejemplo, `alumnoId == 10` o `intentos > 3`). El programa solo se detendrá cuando la condición sea verdadera, evitando pulsar manualmente *Step Over* cientos de veces en bucles largos.

---

#### 4. Actividad activa interactiva: Caza visual del bug en la pizarra

Alba Torres y Pau Ferrer proyectan un extracto de código con la lógica de control del vestíbulo del IES El Caminàs que presenta una anomalía en un bucle:

```java
int totalLecturasValidas = 0;
int[] marcasHorarias = { 480, 482, 485, 495, 510 }; // Minutos desde medianoche (8:00 a 8:30)
int limiteRetraso = 485; // 8:05 h

for (int i = 0; i <= marcasHorarias.length; i++) {
    if (marcasHorarias[i] > limiteRetraso) {
        System.out.println("Acceso registrado con retraso.");
    } else {
        totalLecturasValidas++;
    }
}
```

*Los estudiantes analizan el fragmento durante 3 minutos en su libreta técnica:*
1. **Identificación de defectos:**
    - ¿Qué excepción arrojará la JVM en la última iteración del bucle? Un error de desbordamiento: `ArrayIndexOutOfBoundsException` provocado por la condición de parada incorrecta `i <= marcasHorarias.length` en lugar de `i < marcasHorarias.length`.
2. **Trazado de variables en tabla mental:**
    - ¿Qué valor acumulado tiene `totalLecturasValidas` en el instante inmediatamente anterior a que el programa colapse?
3. **Puesta en común:** Un estudiante voluntario marca en la pizarra en qué línea colocaría el punto de ruptura estratégico para interceptar el error antes de que la aplicación aborte la ejecución.

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

Pasamos al dojo de herramientas. Afrontaremos tres **katas de depuración interactiva, control de ejecución paso a paso e inspección de memoria** sobre el proyecto propio de cada equipo, trabajando la sinergia directa con las estructuras algorítmicas de control:

---

##### Kata 1 (Cinturón blanco / Nivel base). Diagnóstico con puntos de ruptura e inspección de variables en tu proyecto propio

* **Objetivo.** Iniciar tu proyecto bajo el control del depurador, fijar puntos de ruptura estratégicos en bucles o bloques condicionales y auditar el valor de los datos almacenados en las variables locales desde la pestaña **Variables** de IntelliJ IDEA sin recurrir a impresiones por consola.
* **Aplicación según tu proyecto de la bolsa:**
    * En **Aventura conversacional**. Colocar un punto de ruptura al inicio del bucle del menú de decisiones del jugador. Verificar en el panel *Variables* cómo cambian los puntos de vida (`hp`) y el objeto seleccionado en el inventario antes de ramificar la escena.
    * En **Motor de recomendación**. Situar el breakpoint en el bucle que recorre la matriz de valoraciones de ítems. Observar en tiempo real la variación del sumatorio de afinidad y el identificador de usuario activo en cada iteración.
    * En **Simulador de físicas 2D**. Marcar un punto de ruptura dentro del bucle de cálculo temporal (`dt`). Comprobar la evolución de las componentes de velocidad `vx`, `vy` y las coordenadas `x`, `y` del cuerpo en colisión.
    * En **Bóveda de contraseñas**. Poner el breakpoint en el bucle que valida la política de seguridad (longitud, caracteres especiales, mayúsculas). Constatar cómo cambian las banderas booleanas (`tieneMayuscula`, `tieneNumero`) al evaluar cada carácter.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Navegación táctica (Step Over / Step Into) y evaluación de expresiones al vuelo (`Alt + F8`)

* **Contexto técnico.** Cuando un algoritmo falla en una rama condicional interna o en una llamada auxiliar, avanzar ciegamente es inútil. El desarrollador debe dominar la entrada y salida de funciones y calcular predicados lógicos sin reiniciar el software.
* **Misión de la kata:**
    1. En una zona de bifurcaciones (`if`/`else` o `switch`), coloca un punto de ruptura justo en la cabecera de la condición.
    2. Arranca en modo depuración (`Shift + F9`).
    3. Utiliza **Step Over (`F8`)** para seguir el avance secuencial. Al llegar a una invocación a un método de cálculo o validación, pulsa **Step Into (`F7`)** para zambullirte en su interior y comprobar qué parámetros recibe.
    4. Una vez verificada la primera línea, utiliza **Step Out (`Shift + F8`)** para retornar al flujo principal limpiamente.
    5. En una línea con variables numéricas o booleanas, abre la herramienta de evaluación interactiva mediante **`Alt + F8` (Evaluate Expression)**.
    6. Escribe en el cuadro de diálogo una consulta en vivo (por ejemplo, comprobar si una cadena cumple cierta condición o calcular una operación aritmética con las variables actuales). Verifica que IntelliJ evalúa y devuelve el resultado exacto consultando directamente la RAM del hilo en pausa.
    7. Convierte uno de tus breakpoints en **Breakpoint condicional** (clic derecho sobre el punto rojo) para que la ejecución solo se congele cuando el contador de iteraciones supere un valor determinado (por ejemplo, `i == 3` o `reintentos > 2`).

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Mutación de variables en memoria en caliente (*Set Value*) y depuración de excepciones sin tocar código

* **Contexto de arquitectura.** Reproducir fallos intermitentes de red, límites de desbordamiento o entradas anómalas en el cliente puede requerir horas de simulación. Un desarrollador senior altera el estado interno de la memoria durante la propia ejecución para someter al software a pruebas de estrés inmediatas.
* **Misión de la kata:**
    1. Pausa tu programa en un punto de ruptura antes de una bifurcación crítica (por ejemplo, la condición de si el usuario es administrador o si una cola de datos está llena).
    2. En el panel **Variables**, haz clic derecho sobre la variable objetivo y selecciona **Set Value...** (o pulsa **`F2`**).
    3. Modifica su valor directamente en la RAM (por ejemplo, cambia un valor `false` a `true`, o un identificador numérico por un límite negativo `-1`).
    4. Pulsa `Intro` y avanza con `F8`: observa cómo el programa toma la rama lógica alternativa sin que hayas tenido que reescribir código ni recompilar el proyecto con Maven.
    5. **Configuración de un Exception Breakpoint:**
        * Abre el gestor de puntos de ruptura (**`Ctrl + Shift + F8`** o Menú *Run -> View Breakpoints...*).
        * Pulsa el icono `+` (Add) en la esquina superior izquierda y selecciona **Java Exception Breakpoint**.
        * Escribe en el buscador `java.lang.NullPointerException` (o la excepción que desees monitorizar) y confirma.
        * Retira todos los demás puntos de ruptura de tu código y pulsa **Resume Program (`F9`)**.
    6. Comprueba que, en caso de desencadenarse dicha excepción en cualquier rincón del código, la JVM se congelará de forma quirúrgica en la línea exacta que originó el fallo antes de abortar el programa, permitiéndote examinar la pila de llamadas (*Frames*) con total precisión.

### Día 14 - 1 sesión

---

#### Sesión única. Taller práctico: Navegación por la pila de llamadas (*Call Stack*) y protocolo de informe técnico de incidencias (*Bug Report*)

---

#### 1. Caso guía en AzaharTech

Es la mañana del miércoles 28 de octubre de 2026. En la sala de integración de **AzaharTech**, el desarrollador junior se encuentra frente a la pantalla con el semblante desencajado: la consola de IntelliJ IDEA acaba de escupir una cascada de más de treinta líneas de texto en color rojo brillante tras procesar un lote de prueba de carnés digitales del **IES El Caminàs**.

**Pau Ferrer** se acerca con una sonrisa comprensiva y apoya una taza de café sobre la mesa:

> *«No te asustes. El primer reflejo al ver una traza roja es cerrar la terminal o hacer scroll frenético buscando una palabra conocida. Los programadores novatos ven un error catastrófico; los profesionales vemos una autopsia perfecta.*
>
> *Ese bloque de texto no es ruido: es el mapa exacto del hilo de ejecución. Nos indica qué método llamó a qué otro método y en qué línea exacta la máquina virtual se quedó sin instrucciones válidas. Hoy aprenderemos a viajar hacia atrás en el tiempo utilizando el panel de marcos de ejecución (**Frames / Call Stack**) de IntelliJ y a redactar un informe formal de incidencia técnica. Porque en un equipo de consultoría como AzaharTech, un bug que no se puede reproducir ni documentar con rigor simplemente no existe»*.

---

#### 2. La Pila de Llamadas (*Call Stack*): Viajando entre dimensiones del código

Cuando un programa Java se ejecuta, la JVM gestiona la memoria de ejecución mediante una estructura de pila **LIFO** (*Last In, First Out*). Cada vez que un método invoca a otro, se apila un nuevo **marco de pila (*Stack Frame*)** que contiene sus parámetros, variables locales y el contador de programa. Cuando el método termina con un `return`, su marco se destruye y el control regresa al nivel inmediatamente inferior.

```
       FLUJO DE APILAMIENTO EN LA JVM (CALL STACK)
       
       ┌──────────────────────────────────────────┐  ▲  Cima de la pila
       │  Frame 3: ValidadorQR.esFormatoValido()  │  │  (Lugar exacto del fallo)
       ├──────────────────────────────────────────┤  │
       │  Frame 2: GestorAcceso.registrarEntrada()│  │  (Método intermedio)
       ├──────────────────────────────────────────┤  │
       │  Frame 1: Main.main(String[] args)       │  │  (Punto de entrada)
       └──────────────────────────────────────────┘  │
                                                     
       ◄─── Retorno del control / Destrucción del frame
```

##### Navegación interactiva en el panel *Frames* de IntelliJ
Cuando la ejecución está pausada en un punto de ruptura (o interceptada por un *Exception Breakpoint*):
1. **La columna izquierda de la pestaña Debug muestra los *Frames*:** En la parte superior aparece el método en el que estamos detenidos en este milisegundo; debajo, el método que lo invocó, y así sucesivamente hasta llegar al `main`.
2. **Viaje en el tiempo sin alterar el flujo:** Si haces clic en `Frame 2` o `Frame 1`, el editor de código saltará automáticamente a esa línea y el panel **Variables** se actualizará al instante para mostrar cómo estaban las variables en ese método superior, permitiéndote comprobar qué argumentos le envió al método fallido.

---

#### 3. Estándar AzaharTech para el Registro de Incidencias Técnicas

En el ciclo de desarrollo profesional regulado por metodologías ágiles, cualquier error detectado durante las sesiones de depuración que no pueda subsanarse en el acto debe registrarse formalmente en el sistema de seguimiento de incidencias (*Issue Tracker* del repositorio Git).

Un informe técnico de incidencia (*Bug Report*) riguroso debe responder al siguiente estándar:

```markdown
### [BUG] Desbordamiento de índice al escanear lote con identificador vacío

**1. Entorno de ejecución:**
- Versión del software: v0.1-sprint3 (commit `a4f82bc`)
- Entorno: OpenJDK 21 LTS / IntelliJ IDEA 2026.2 / Linux x86_64
- Módulo afectado: Núcleo de validación de accesos

**2. Descripción concisa:**
La aplicación aborta de forma inesperada cuando la cola de escaneo procesa un registro
cuyo campo de datos biométricos o identificador de matrícula contiene una cadena vacía.

**3. Pasos precisos para reproducir (Steps to Reproduce):**
1. Iniciar la aplicación en modo Debug con el dataset de prueba `test_alumnos_v1.csv`.
2. Introducir por consola la opción 2 ("Validación por lotes").
3. Asignar un tamaño de lote de 5 elementos incluyendo un registro sin identificador.

**4. Comportamiento observado (Actual Behavior):**
La JVM lanza una excepción `StringIndexOutOfBoundsException: Index 0 out of bounds for length 0`
y la aplicación finaliza abruptamente.

**5. Comportamiento esperado (Expected Behavior):**
El sistema debería capturar la anomalía, marcar el registro como inválido en el log
y continuar procesando los restantes alumnos de la cola sin abortar.

**6. Diagnóstico técnico y Call Stack:**
- Punto de interrupción: `ValidadorQR.java:38`
- Pila de llamadas:
    `at es.azahartech.caminas.ValidadorQR.comprobarPrefijo(ValidadorQR.java:38)`
    `at es.azahartech.caminas.GestorAcceso.procesarRegistro(GestorAcceso.java:74)`
    `at es.azahartech.caminas.Main.main(Main.java:21)`
- Inspección de memoria: Variable `qrData = ""` (longitud 0).
```

---

#### 4. Dinámica de laboratorio: Simulación de fallo en cascada y auditoría cruzada

Durante los últimos 30 minutos de la sesión, cada estudiante pondrá a prueba la solidez de las estructuras de control que ha estado programando durante la semana:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        FASES DEL LABORATORIO PRÁCTICO (DÍA 14)                         │
├──────────────┬──────────────────┬──────────────────────────────────────────────────────┤
│ Tiempo       │ Fase             │ Operación técnica requerida                          │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 00 – 10  │ 1. Provocación   │ Forzar deliberadamente una excepción de ejecución en │
│              │    del fallo     │ un método auxiliar profundo (por ejemplo, enviando   │
│              │                  │ un parámetro nulo, una cadena vacía o índice fuera). │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 10 – 20  │ 2. Exploración   │ Pausar con *Exception Breakpoint*, navegar hacia     │
│              │    de Frames     │ atrás por los 3 primeros marcos en el panel *Frames* │
│              │                  │ y localizar en qué línea se originó el dato corrupto.│
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 20 – 30  │ 3. Redacción     │ Crear el fichero `docs/incidencias/INCIDENCIA-01.md` │
│              │    del informe   │ en el repositorio con la plantilla formal de bug.    │
└──────────────┴──────────────────┴──────────────────────────────────────────────────────┘
```

##### Adaptación inmediata según el proyecto de la bolsa:
* **En Aventura conversacional:** Provocar un fallo al intentar acceder a la posición `[0]` de un inventario vacío dentro del método `consultarPrimerObjeto()`. Utilizar el panel *Frames* para retroceder hasta la llamada en `procesarAccionJugador()` y comprobar qué comando escribió el usuario.
* **En Motor de recomendación:** Provocar una división por cero (`ArithmeticException`) o un cálculo indefinido al promediar valoraciones de un usuario sin histórico. Rastrear en los marcos de pila qué filtro condicional falló en validar el divisor.
* **En Simulador de físicas 2D:** Forzar un parámetro de tiempo transcurrido negativo (`dt < 0`) y verificar a través de los *Frames* qué método de la física propagó el valor erróneo antes de que el motor actualizara las coordenadas de pantalla.
* **En Bóveda de contraseñas:** Generar un puntero nulo (`NullPointerException`) al intentar cifrar una contraseña que no ha sido inicializada en el formulario de registro. Inspeccionar el marco del método llamador para comprobar por qué el objeto llegó sin instanciar.

Al sonar el timbre, los estudiantes dejan confirmado su primer informe técnico de incidencia en Git. El código aún no es perfecto, pero el equipo dispone ya de los ojos de diagnóstico necesarios para abordar la construcción formal de casos de prueba y matrices de riesgo en las próximas semanas.

## Semana 8. Diseño sistemático de pruebas: clases de equivalencia, análisis de valores límite y especificación de casos de prueba

---

### Día 15 - 2 sesiones

---

#### Sesión 1. Teoría. La ingeniería del caso de prueba: cómo probar sin perderse en el infinito

---

#### 1. Caso guía en AzaharTech

Lunes 2 de noviembre de 2026. Tras una primera semana de inmersión en el depurador interactivo, el equipo de primer curso de DAM regresa a las instalaciones de **AzaharTech** en Castellón de la Plana.

En la pantalla de la sala de reuniones, **Laia Claramunt** proyecta un formulario simple de la aplicación del **IES El Caminàs**: la ventana de registro de carnés temporales de visitante. El formulario solicita dos campos: la **edad del visitante** (de 12 a 99 años para acceder sin acompañante adulto) y un **código PIN de seguridad** de 4 dígitos (de `1000` a `9999`).

Laia mira a los estudiantes y lanza una pregunta directa:

> *«Si quisiéramos demostrar matemáticamente que esta pequeña pantalla no tiene ningún fallo lógico, ¿cuántas combinaciones tendríamos que probar a mano?*
>
> *La edad admite teóricamente cualquier número entero de 32 bits en memoria (más de cuatro mil millones de valores), y el PIN tiene 9000 combinaciones válidas más infinitas cadenas alfanuméricas inválidas. Probar todas las entradas posibles por fuerza bruta (**exhaustion testing**) requeriría semanas de trabajo humano o miles de horas de computación inútil.*
>
> *En la industria del software no probamos al azar ni probamos todo: aplicamos técnicas formales de ingeniería para seleccionar el conjunto mínimo de datos que garantice la máxima detección de defectos. Hoy dejaremos de cazar bugs a ciegas y aprenderemos a diseñar* *casos de prueba profesionales* *utilizando dos pilares de las pruebas de caja negra:* *las clases de equivalencia* *y* *el análisis de valores límite»*.

---

#### 2. Metodología de caja negra: Partición de Clases de Equivalencia

En las pruebas de **caja negra** (*Black-Box Testing*), nos centramos exclusivamente en las entradas y salidas del sistema según sus especificaciones funcionales, sin preocuparnos por cómo está escrito el código fuente por dentro.

```
                   MODELO DE CAJA NEGRA (BLACK-BOX)
                   
                     ┌───────────────────────────┐
  [Datos de Entrada] │    Módulo / Algoritmo     │ [Resultado Observado]
  ──────────────────►│  (Lógica interna oculta)  ├──────────────────────►
                     └───────────────────────────┘
                                   │
                                   ▼
             ¿Coincide el resultado con la especificación?
```

##### Partición en clases de equivalencia (*Equivalence Partitioning*)
Esta técnica asume que el conjunto total de entradas posibles puede dividirse en grupos llamados **clases de equivalencia**. Si el programa trata correctamente un valor representativo de una clase, con alta probabilidad tratará igual a todos los demás valores de esa misma clase.

Para cada dato de entrada debemos identificar dos tipos de clases:
1. **Clases de equivalencia válidas:** Valores que cumplen las reglas de negocio y deben procesarse satisfactoriamente.
2. **Clases de equivalencia inválidas:** Valores incorrectos, fuera de rango o con formatos anómalos que el sistema debe rechazar de forma controlada (sin abortar el hilo de ejecución).

```
DIVISIÓN DE ESPACIO DE ENTRADAS (Ejemplo: Edad válida entre 12 y 99 años):

    Clase Inválida 1          Clase Válida            Clase Inválida 2
    (Menores de 12)        (Rango permitido)           (Mayores de 99)
◄─────────────────────────┬──────────────────────────┬────────────────────────►
 ... -5, 0, 1, ..., 11    │   12, 13, ..., 98, 99    │ 100, 101, 150 ...
                          │                          │
  Representante: 8        │   Representante: 25      │ Representante: 105
  (Espera: Rechazo)       │   (Espera: Aceptado)     │ (Espera: Rechazo)
```

---

#### 3. Análisis de Valores Límite (*Boundary Value Analysis - BVA*)

La experiencia en ingeniería del software demuestra que la inmensa mayoría de los defectos lógicos no ocurren en el centro de las clases de equivalencia (probar la edad `25` rara vez hace fallar a un algoritmo), sino **justo en los bordes de decisión**.

En Programación, los fallos en estructuras condicionales suelen deberse a confusiones sintácticas entre operadores: escribir `<` en lugar de `<=`, o arrancar un bucle en `0` en vez de `1` (*errores off-by-one*).

El **análisis de valores límite** complementa a las clases de equivalencia obligando a probar:
* El valor mínimo y el valor máximo permitidos en la frontera.
* Los valores inmediatamente adyacentes a cada frontera (justo por debajo y justo por encima).

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        MAPA DE VALORES LÍMITE (RANGO [12, 99])                         │
├─────────────────────┬───────────────┬──────────────────────┬───────────────────────────┤
│ Posición del límite │ Valor probado │ Tipo de clase        │ Resultado esperado        │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite inferior - 1 │ 11            │ Inválida (demasiado  │ Error: "Menor de edad no  │
│                     │               │ joven)               │ autorizado"               │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite inferior     │ 12            │ Válida (mínimo legal)│ Registro admitido         │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite inferior + 1 │ 13            │ Válida (interior)    │ Registro admitido         │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite superior - 1 │ 98            │ Válida (interior)    │ Registro admitido         │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite superior     │ 99            │ Válida (máximo legal)│ Registro admitido         │
├─────────────────────┼───────────────┼──────────────────────┼──────────────────────────┤
│ Límite superior + 1 │ 100           │ Inválida (edad fuera │ Error: "Edad excede el    │
│                     │               │ de rango operativo)  │ límite del sistema"       │
└─────────────────────┴───────────────┴──────────────────────┴───────────────────────────┘
```

---

#### 4. Estructura formal de un Caso de Prueba (*Test Case*)

En el estándar corporativo de AzaharTech, cada prueba formal de caja negra se documenta como un registro trazable con los siguientes atributos:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        PLANTILLA DE ESPECIFICACIÓN DE CASO DE PRUEBA                   │
├──────────────────────┬─────────────────────────────────────────────────────────────────┤
│ Identificador (ID)   │ CP-ACC-001 (Código unívoco vinculado al módulo funcional)       │
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Nombre / Descripción │ Validación de acceso en frontera inferior de edad permitida     │
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Precondiciones       │ Base de datos del IES El Caminàs iniciada; terminal en espera   │
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Datos de entrada     │ edad = 12; pin = 5540                                           │
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Pasos de ejecución   │ 1. Arrancar el validador. 2. Introducir valores. 3. Pulsar OK.  │
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Resultado esperado   │ El sistema responde `ACCESO_CONCEDIDO` y genera un pase temporal│
├──────────────────────┼─────────────────────────────────────────────────────────────────┤
│ Criterio de éxito    │ Salida real idéntica al resultado esperado; sin excepciones JVM │
└──────────────────────┴─────────────────────────────────────────────────────────────────┘
```

---

#### 5. Actividad activa interactiva: Caza de fronteras en la pizarra

Alba Torres plantea en la pizarra el requisito del identificador temporal de visitante para el IES El Caminàs:
> *«El sistema genera una clave de acceso temporal de 4 cifras numéricas. Para evitar códigos triviales de prueba de fábrica, el valor del PIN debe estar comprendido estrictamente entre `1000` y `9999` (ambos inclusive)»*.

Durante 5 minutos, los alumnos debaten y completan en la pizarra la tabla de valores críticos:
1. ¿Cuáles son los **valores límite** exactos que hay que probar? (`999`, `1000`, `1001`, `9998`, `9999`, `10000`).
2. ¿Qué ocurre con los casos especiales de frontera cualitativa? (por ejemplo, valor nulo `null`, cadena vacía `""` o texto con letras `"100A"`).
3. Conclusión guiada: *«Con solo 6 pruebas numéricas y 2 cualitativas cubrimos con rigor técnico el comportamiento de miles de combinaciones posibles»*.

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

Nos trasladamos a los puestos de trabajo para ejecutar las katas prácticas de diseño formal de pruebas sobre los módulos que cada estudiante está construyendo en su proyecto individual:

---

##### Kata 1 (Cinturón blanco / Nivel base). Matriz de clases de equivalencia de tu módulo de lógica

* **Objetivo.** Tomar una función crítica de validación o cálculo de tu proyecto y definir formalmente en un documento Markdown (`docs/pruebas/matriz-equivalencia.md`) todas las clases válidas e inválidas posibles, asignando un dato representativo para cada una.
* **Aplicación según tu proyecto de la bolsa:**
    * En **Aventura conversacional.** Analizar la función que procesa la compra de pociones en la tienda del juego. Parámetro: `oroDisponible` (debe ser mayor o igual al coste del ítem). Definir clases: saldo suficiente ($> coste$), saldo exacto ($== coste$), saldo insuficiente ($< coste$) y saldo negativo anómalo ($< 0$).
    * En **Motor de recomendación.** Analizar la función de asignación de puntuaciones de afinidad (escala de 1 a 5 estrellas). Identificar clases válidas (rango entero $[1, 5]$) e inválidas (números $< 1$, números $> 5$, decimales o cadenas de texto no parseables).
    * En **Simulador de físicas 2D.** Analizar la función que calcula el rebote elástico. Parámetro: coeficiente de restitución `e` (válido en el intervalo real $[0.0, 1.0]$). Definir clases válidas (rebote inelástico a elástico perfecto) e inválidas ($e < 0.0$ o $e > 1.0$).
    * En **Bóveda de contraseñas.** Analizar la regla de longitud mínima y máxima para la contraseña maestra (longitud válida entre 8 y 32 caracteres). Definir clases válidas (longitud entre 8 y 32) e inválidas (longitud $< 8$, longitud $> 32$, cadena vacía y `null`).

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Batería formal de casos de prueba con análisis de valores límite (BVA)

* **Contexto técnico.** Una vez delimitadas las clases de equivalencia, el desarrollador debe aterrizar los valores frontera en una tabla técnica de casos de prueba ejecutables, detallando entradas exactas y salidas esperadas.
* **Misión de la kata:**
    1. Diseña una tabla en Markdown dentro de `docs/pruebas/casos-de-prueba.md` con un mínimo de **6 casos de prueba** derivados del análisis de valores límite de tu regla de negocio (tres para el límite inferior y tres para el límite superior).
    2. Cada caso de prueba debe incluir: `ID`, `Entrada`, `Tipo de Límite (Inferior / Superior)`, `Resultado Esperado` y `Estado (Pendiente / Pasado / Fallido)`.
    3. Ejecuta manualmente en tu programa cada uno de los casos diseñados introduciendo los valores por teclado o mediante llamadas en tu método `main`.
    4. Anota con precisión quirúrgica el resultado obtenido. Si encuentras una discrepancia (por ejemplo, el programa acepta la longitud `7` cuando la especificación marcaba mínimo `8`), marca el caso como **Fallido** y no toques el código fuente todavía: el fallo ha sido formalmente acorralado.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Trazabilidad de frontera en memoria con depurador y aserciones

* **Contexto de arquitectura.** Comprobar los valores límite por consola puede enmascarar si el programa superó la condición por el camino lógico correcto o por casualidad. En esta kata combinamos el depurador de la Semana 7 con la batería de pruebas de la Semana 8.
* **Misión de la kata:**
    1. Abre el archivo Java donde reside la condición lógica evaluada en tu batería de pruebas.
    2. Coloca un **punto de ruptura condicional** en la línea exacta del `if`. Configúralo para que se pause únicamente cuando la variable evaluada coincida con los valores límite críticos (por ejemplo: `longitud == 8 || longitud == 7`).
    3. Lanza el programa en modo depuración (**Debug**, `Shift + F9`) y suministra el caso de prueba del límite conflictivo.
    4. Cuando la ejecución se congele en la línea del `if`, abre la consola de evaluación (**`Alt + F8`**) y evalúa la expresión booleana completa de la condición.
    5. Inspecciona paso a paso (**`F8`**) si el salto condicional entra por la rama verdadera o la rama falsa.
    6. Si detectas un error de frontera (un clásico `<` en vez de `<=`), documenta el diagnóstico en la columna de observaciones de tu batería de pruebas, corrige el operador en el código fuente, recompila con Maven y vuelve a ejecutar la prueba para certificar que el caso cambia de **Fallido** a **Pasado**.
    7. Haz commit en tu repositorio Git de los nuevos documentos de pruebas y de la corrección realizada bajo el mensaje:
       `fix(core): corregido error off-by-one en validacion de frontera segun CP-002`.

### Día 16 - 1 sesión

---

#### Sesión única. Taller práctico: Medición del cubrimiento de código (*Code Coverage*) y procedimiento de ejecución de pruebas

---

#### 1. Caso guía en AzaharTech

Miércoles 4 de noviembre de 2026. En el ecuador de la segunda semana del **Sprint 3**, el equipo de desarrollo se concentra en la sala de operaciones de **AzaharTech**.

Los estudiantes tienen sobre sus pantallas las baterías de casos de prueba diseñadas el lunes mediante partición de equivalencia y valores límite. **Alba Torres** se sitúa junto a la mesa del desarrollador junior y revisa el documento de pruebas del validador de accesos del **IES El Caminàs**:

> *«Habéis hecho un trabajo excelente acorralando los límites de entrada: el código ya no revienta con edades de 11 o 100 años ni con contraseñas vacías. Sin embargo, os hago una pregunta crítica de aseguramiento de la calidad (QA):*
>
> *¿Estáis seguros de que vuestros casos de prueba han obligado a la JVM a pasar por todas las ramas de vuestro código? ¿O tenéis bloques de sentencias enteras dentro de los `else` o de los `catch` que jamás se han ejecutado en la realidad?*
>
> *Si una línea de código no se ejecuta durante las pruebas, es código en la sombra: una bomba de relojería esperando a que un usuario real entre por ese camino en producción. Hoy utilizaremos el motor de* *cubrimiento de código (Code Coverage)* *integrado en IntelliJ IDEA para iluminar visualmente qué partes de nuestro software están blindadas y cuáles permanecen completamente a oscuras»*.

---

#### 2. Fundamentos del cubrimiento de código (*Code Coverage*)

El **cubrimiento de código** es una métrica de **caja blanca** (*White-Box Testing*) que cuantifica el porcentaje de código fuente ejecutado cuando se somete una aplicación a una batería de pruebas.

No todas las métricas de cobertura ofrecen el mismo nivel de garantía:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        NIVELES DE CUBRIMIENTO DE CÓDIGO                                │
├─────────────────────┬─────────────────────────────────┬────────────────────────────────┤
│ Métrica             │ ¿Qué mide exactamente?          │ Nivel de rigor técnico         │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 1. Cubrimiento de   │ Porcentaje de líneas de código  │ BÁSICO: Garantiza que la JVM   │
│    sentencias/líneas│ que han sido ejecutadas al      │ ha pisado la instrucción, pero │
│    (Line Coverage)  │ menos una vez.                  │ ignora si se probaron todos los│
│                     │                                 │ caminos de una decisión.       │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 2. Cubrimiento de   │ Porcentaje de ramas evaluadas en│ MEDIO-ALTO: En un `if (A)`,    │
│    ramas/decisiones │ estructuras condicionales       │ comprueba que se han probado   │
│    (Branch Coverage)│ (`if`, `switch`, bucles).       │ tanto el camino TRUE como el   │
│                     │                                 │ camino FALSE.                  │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 3. Cubrimiento de   │ Porcentaje de todas las         │ EXHAUSTIVO: En condiciones     │
│    condiciones      │ combinaciones booleanas         │ complejas `if (A && B)`, exige │
│    múltiples        │ posibles dentro de cada rama.   │ probar todas las combinaciones │
│                     │                                 │ de verdad de A y B.            │
└─────────────────────┴─────────────────────────────────┴────────────────────────────────┘
```

```
ILUSTRACIÓN DE LA TRAMPA DEL CUBRIMIENTO DE LÍNEAS:

    if (esFinDeSemana && carnetCaducado) {   ◄─── Línea evaluada
        denegarAcceso();                     ◄─── Línea ejecutada (100% Line Coverage)
    }

    * Si solo probamos el caso (TRUE && TRUE), la cobertura de líneas dirá 100%.
    * Pero la cobertura de ramas alertará: "Falta probar qué ocurre cuando es FALSE".
```

> **Axioma de calidad de Laia Claramunt:**
> *«Un cubrimiento de código del 100 % no demuestra que un programa carezca de fallos: no puede detectar requisitos que olvidaste programar. Sin embargo, un cubrimiento del 40 % demuestra matemáticamente que el 60 % de tu software es un territorio desconocido y sin control»*.

---

#### 3. El mapa visual de cobertura en IntelliJ IDEA

IntelliJ IDEA incorpora un motor nativo de instrumentación de bytecode que monitoriza cada instrucción ejecutada por la JVM.

##### Cómo lanzar la ejecución con cobertura
En lugar del botón habitual de *Run* (`Shift + F10`) o *Debug* (`Shift + F9`), seleccionamos la opción **Run '...' with Coverage** (icono del triángulo verde superpuesto sobre un pequeño escudo de seguridad).

Al finalizar la ejecución, el editor de IntelliJ activa su semáforo de cobertura en el margen lateral izquierdo (*gutter*):

```
       CÓDIGO DE COLORES EN EL EDITOR DE INTELLIJ IDEA
       
       ┌───┬────────────────────────────────────────────────────────┐
  Verde│   │ if (puntosVida > 0) {                                  │ ◄── Rama completamente cubierta
       │   │     personaje.continuarAventura();                     │
       ├───┼────────────────────────────────────────────────────────┤
Amarillo│   │ if (intentosRestantes > 0 && servidorActivo) {         │ ◄── Rama parcialmente cubierta
       │   │     reintentarConexion();                              │     (solo se probó una condición)
       ├───┼────────────────────────────────────────────────────────┤
   Rojo│   │ } else {                                               │
       │   │     notificarAdministradorEmergencia();                 │ ◄── Código no ejecutado
       │   │     bloquearTerminal();                                │     (0% cubrimiento / Punto ciego)
       └───┴────────────────────────────────────────────────────────┘
```

Además, IntelliJ despliega la ventana acoplable **Coverage**, que desglosa el porcentaje exacto de cobertura por paquetes, clases, métodos y líneas de todo el proyecto.

---

#### 4. Dinámica de laboratorio: Cierre de ángulos muertos (30 minutos)

Durante la segunda mitad de la sesión, los estudiantes auditan su propio software aplicando la técnica de **pruebas dirigidas por cobertura (*Coverage-Driven Testing*)**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        FASES DEL LABORATORIO DE COBERTURA (DÍA 16)                     │
├──────────────┬──────────────────┬──────────────────────────────────────────────────────┤
│ Tiempo       │ Fase             │ Tarea técnica requerida                              │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 00 – 10  │ 1. Ejecución con │ Lanzar la clase principal o módulo de validación con │
│              │    Coverage      │ **Run with Coverage**. Registrar el % inicial de     │
│              │                  │ cobertura de líneas en el cuaderno de bitácora.      │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 10 – 20  │ 2. Detección de  │ Inspeccionar los bloques marcados en **rojo** o      │
│              │    bloques rojos │ **amarillo**. Identificar qué rama lógica jamás fue  │
│              │                  │ alcanzada por los casos de prueba del Día 15.        │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 20 – 30  │ 3. Diseño del    │ Crear un nuevo caso de prueba específico para        │
│              │    caso de cierre│ forzar la ejecución del bloque rojo y conseguir que  │
│              │                  │ todo el margen del editor se ilumine en **verde**.   │
└──────────────┴──────────────────┴──────────────────────────────────────────────────────┘
```

##### Casuística según el proyecto de la bolsa:
* **En Aventura conversacional:** Identificar si la rama que gestiona la muerte del jugador (`hp <= 0`) o el rechazo de comandos desconocidos en el `default` del `switch` está en rojo. Diseñar un caso de prueba con un comando inválido (ej. `"VOLAR"`) para teñir la rama de verde.
* **En Motor de recomendación:** Comprobar si el bloque de control que gestiona usuarios sin ninguna valoración previa (*cold start*) está desatendido. Introducir un vector de valoraciones vacío para verificar que el mensaje de cortesía se ejecuta correctamente.
* **En Simulador de físicas 2D:** Verificar si las ramas que controlan la parada por fricción total (`velocidad < 0.001f`) o el choque contra el techo del escenario están sin cobertura. Diseñar una prueba que simule una caída libre prolongada para activar los límites de parada.
* **En Bóveda de contraseñas:** Comprobar si la rama que captura cadenas con caracteres de escape o espacios en blanco quedó sin evaluar en el bucle de validación. Suministrar una contraseña de prueba con espacios intermedios (ej. `"Admin 2026*"`) para forzar la ejecución del rechazo.

##### Entregable de cierre de sesión
Cada alumno formaliza el estado de sus pruebas en el archivo `docs/pruebas/informe-cobertura.md`:

```markdown
### Registro de Ejecución y Cubrimiento de Pruebas (Sprint 3 - Semana 8)

- **Módulo auditado:** Validador de reglas de negocio
- **Cubrimiento inicial de líneas:** 68% (Ramas `else` y excepciones no cubiertas)
- **Ángulo muerto detectado:** La excepción de argumento inválido nunca se disparaba.
- **Acción correctora:** Se añadió el caso de prueba `CP-VAL-007` con entrada nula.
- **Cubrimiento final alcanzado:** 92% de líneas / 85% de ramas.
```

Con la ejecución confirmada y registrada en el control de versiones, el equipo dispone de un código verificado con métricas objetivas, sentando la base para las pruebas estructurales avanzadas y la depuración de algoritmos complejos de la próxima semana.

## Semana 9. Pruebas estructurales, complejidad ciclomática y depuración de algoritmos complejos

---

### Día 17 - 2 sesiones

---

#### Sesión 1. Teoría. Desmontando el laberinto: grafos de flujo, métrica de McCabe y depuración no invasiva

---

#### 1. Caso guía en AzaharTech

Lunes 9 de noviembre de 2026. Arranca la tercera y última semana del **Sprint 3** en la sede de **AzaharTech** en Castellón de la Plana.

En los módulos troncales, la exigencia técnica se encuentra en su punto álgido: en Programación, los alumnos están implementando algoritmos con bucles anidados (`for`, `while`), menús ramificados con `switch` y filtros encadenados con múltiples operadores lógicos (`&&`, `||`).

Sobre la pizarra de la sala técnica, **Alba Torres** y **Pau Ferrer** han transcrito el método principal del módulo de cálculo de sanciones y retrasos acumulados del **IES El Caminàs**. El código ocupa casi setenta líneas repletas de bifurcaciones anidadas:

```java
public String calcularEstadoAsistencia(int minutosRetraso, boolean esJustificado, int faltasPrevias, boolean esReincidente) {
    String estado;
    if (minutosRetraso > 0) {
        if (!esJustificado) {
            if (minutosRetraso > 30) {
                estado = "FALTA_GRAVE";
            } else {
                estado = "RETRASO_LEVE";
            }
            if (faltasPrevias >= 3 || esReincidente) {
                estado += "_CON_EXPEDIENTE";
            }
        } else {
            estado = "JUSTIFICADO";
        }
    } else {
        estado = "PUNTUAL";
    }
    return estado;
}
```

**Laia Claramunt** toma el rotulador y rodea el método:

> *«Mirad este bloque. Cuando las condiciones se anidan, la mente humana tiende a colapsar: es el temido 'bloqueo algorítmico'. Es fácil perder la pista de qué camino toma la ejecución cuando se combinan variables booleanas y contadores numéricos.*
>
> *A mediados de los años setenta, el matemático y científico de la computación Thomas McCabe formuló una regla que sigue siendo el estándar de oro en la ingeniería de software: la* *Complejidad Ciclomática*.*
>
> *Hoy aprenderemos a traducir cualquier fragmento de código a un* *Grafo de Flujo de Control (CFG)* *para saber exactamente cuántos caminos independientes existen en un algoritmo. Y para no volvernos locos depurando bucles que se repiten cientos de veces, utilizaremos dos herramientas avanzadas de IntelliJ IDEA que no alteran el código:* *las variables monitorizadas (Watches)* *y* *los puntos de traza silenciosos (Logpoints)»*.

---

#### 2. Grafos de Flujo de Control (CFG) y Complejidad Ciclomática de McCabe

El **Grafo de Flujo de Control** (*Control Flow Graph* o CFG) es una representación gráfica de todos los caminos que puede atravesar un programa durante su ejecución.
* **Nodos ($N$):** Representan bloques de código secuencial (sin saltos ni bifurcaciones).
* **Aristas ($E$):** Flechas dirigidas que representan la transferencia de control entre instrucciones.

```
       CONVERSIÓN DE ESTRUCTURAS DE CONTROL A GRAFO (CFG)
       
          BIFURCACIÓN CONDICIONAL (IF)                 BUCLE (WHILE / FOR)
                     ┌───┐                                    ┌───┐
                     │ 1 │ (Condición)                        │ 1 │◄─────┐
                     └─┬─┘                                    └─┬─┘      │
                   TRUE│  ▲FALSE                            TRUE│  ▲     │
              ┌────────┘  └────────┐                       ┌────┘  │     │
              ▼                    ▼                       ▼       │Itera│
            ┌───┐                ┌───┐                   ┌───┐     │     │
            │ 2 │ (Rama Then)    │ 3 │ (Rama Else)       │ 2 │(Cuerpo)───┘
            └───┬┘               ┌───┘                   └───┬┘
                │   ┌───┐        │                      FALSE│
                └──►│ 4 │◄───────┘                           ▼
                    └───┘ (Fin If)                         ┌───┐
                                                           │ 3 │ (Salida)
                                                           └───┘
```

##### La fórmula de Thomas McCabe
La **Complejidad Ciclomática** ($V(G)$) cuantifica la complejidad lógica de un método y determina el número de **caminos linealmente independientes** que lo forman. Esto define el número mínimo de casos de prueba necesarios para garantizar que cada instrucción se ejecuta al menos una vez.

Se puede calcular de dos formas equivalentes:
1. **Fórmula topológica:**
   $$V(G) = E - N + 2P$$
   *(Donde $E$ es el número de aristas, $N$ el de nodos y $P$ el número de componentes conexos, normalmente $P = 1$ para un único método).*
2. **Regla de las regiones o puntos de decisión:**
   $$V(G) = \text{Número de puntos de decisión} + 1$$
   *(Cada `if`, `while`, `for`, `case` o condición booleana compuesta con `&&` o `||` añade un punto de decisión).*

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        ESCALA DE RIESGO DE LA COMPLEJIDAD CICLOMÁTICA                  │
├──────────────┬──────────────────┬──────────────────────┬───────────────────────────────┤
│ Valor V(G)   │ Nivel de riesgo  │ Mantenibilidad       │ Recomendación técnica         │
├──────────────┼──────────────────┼──────────────────────┼───────────────────────────────┤
│ 1 – 10       │ Código simple    │ Alta mantenibilidad  │ Módulo bien estructurado.     │
│              │ (Riesgo bajo)    │ Fácil de probar      │ Diseño limpio y legible.      │
├──────────────┼──────────────────┼──────────────────────┼───────────────────────────────┤
│ 11 – 20      │ Complejidad      │ Moderada dificultad  │ Requiere atención. Conviene   │
│              │ moderada         │ de seguimiento       │ extraer métodos auxiliares.   │
├──────────────┼──────────────────┼──────────────────────┼───────────────────────────────┤
│ 21 – 50      │ Alta complejidad │ Código inestable     │ ALERTA: Muy difícil de testear│
│              │ (Riesgo alto)    │ Propensión a bugs    │ Refactorización prioritaria.  │
├──────────────┼──────────────────┼──────────────────────┼───────────────────────────────┤
│ > 50         │ Inasumible       │ Intesteable          │ Código no aceptable según los │
│              │ (Riesgo crítico) │ "Código espagueti"   │ estándares de AzaharTech.     │
└──────────────┴──────────────────┴──────────────────────┴───────────────────────────────┘
```

---

#### 3. Herramientas avanzadas del depurador para algoritmos complejos

Cuando un algoritmo ejecuta bucles de decenas o cientos de iteraciones, pulsar `F8` repetidamente es inviable. Para estos escenarios, IntelliJ IDEA ofrece dos herramientas de inspección no invasiva:

##### A) Variables y expresiones monitorizadas (*Watches*)
En la pestaña **Variables** del panel Debug, podemos pulsar el icono `+` (**New Watch...**) para escribir expresiones dinámicas complejas que queramos vigilar constantemente (por ejemplo: `listaAlumnos.size()`, `i * factorEscala` o `persona != null && persona.esActivo()`).
* A diferencia de inspeccionar una variable estática, el **Watch** reevalúa la fórmula matemática o lógica en cada micro-paso de la ejecución, destacando en color azul cualquier cambio de valor.

##### B) Puntos de traza silenciosos (*Logpoints / Tracepoints*)
Los **Logpoints** resuelven de raíz la tentación de meter sentencias `System.out.println` en el código fuente:
1. Coloca un punto de ruptura normal en el margen lateral.
2. Haz clic derecho sobre el punto rojo para abrir sus propiedades avanzadas.
3. **Desmarca** la casilla **Suspend** (para que la JVM **no detenga** la ejecución del programa).
4. **Marca** la casilla **Evaluate and log** (o *Breakpoint hit message*) y escribe una expresión Java entrecomillada o una variable:
   `"Iteración: " + i + " | Minutos acumulados: " + totalMinutos`
5. Al ejecutar con Debug, IntelliJ imprimirá ese mensaje directamente en la consola de depuración cada vez que el hilo pase por esa línea, a máxima velocidad y sin haber modificado ni una sola coma del archivo fuente.

```
VENTANA DE CONFIGURACIÓN DE UN LOGPOINT EN INTELLIJ:

    ┌────────────────────────────────────────────────────────┐
    │ Breakpoint: GestorAcceso.java:45                       │
    ├────────────────────────────────────────────────────────┤
    │ [ ] Suspend   ◄── (Desmarcado: NO detiene el programa) │
    │                                                        │
    │ Log:                                                   │
    │ [x] "Breakpoint hit" message                           │
    │ [x] Evaluate and log:                                  │
    │     ┌────────────────────────────────────────────┐     │
    │     │ "Alumno: " + id + " -> Estado: " + estado  │     │
    │     └────────────────────────────────────────────┘     │
    └────────────────────────────────────────────────────────┘
```

---

#### 4. Actividad activa interactiva: Cálculo de caminos en la pizarra

Alba Torres retoma el método `calcularEstadoAsistencia` presentado al inicio.
Los estudiantes analizan los puntos de decisión del código:
1. Condición 1: `if (minutosRetraso > 0)` (+1)
2. Condición 2: `if (!esJustificado)` (+1)
3. Condición 3: `if (minutosRetraso > 30)` (+1)
4. Condición 4: `if (faltasPrevias >= 3 || esReincidente)` (Contiene un operador `||`, sumando dos caminos booleanos: +2)

* **Cálculo:**
  $$\text{Puntos de decisión} = 1 + 1 + 1 + 2 = 5$$
  $$V(G) = 5 + 1 = 6$$
* **Conclusión técnica:** *«Para tener una cobertura completa de caminos independientes de este método, necesitamos exactamente 6 casos de prueba distintos. Ni 5, ni 20: seis casos bien dirigidos garantizan que ningún recoveco quede sin auditar»*.

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

Pasamos a los puestos de trabajo para aplicar las técnicas de análisis estructural y depuración avanzada sobre los algoritmos individuales de cada proyecto:

---

##### Kata 1 (Cinturón blanco / Nivel base). Grafo de flujo y cálculo de $V(G)$ de tu algoritmo central

* **Objetivo.** Seleccionar el método más complejo desarrollado hasta la fecha en tu proyecto propio (aquel que combine bucles y estructuras condicionales), dibujar su grafo de flujo de control elemental y calcular formalmente su Complejidad Ciclomática $V(G)$.
* **Aplicación según tu proyecto de la bolsa:**
    * En **Aventura conversacional.** Analizar el método del bucle de combate o de resolución de acertijos (evaluación de vida del monstruo, tiradas de dados, estado de aturdimiento y huida).
    * En **Motor de recomendación.** Analizar el algoritmo de ordenación o filtrado condicional por umbral de similitud (bucle que compara vectores de usuarios con condiciones múltiples).
    * En **Simulador de físicas 2D.** Analizar el algoritmo de detección y respuesta de colisiones contra los cuatro bordes de la ventana y otros obstáculos rígidos.
    * En **Bóveda de contraseñas.** Analizar la función de cálculo del nivel de entropía o fortaleza de contraseña (bucle que recorre la cadena comprobando caracteres contiguos repetidos, símbolos y números).
* **Entregable:** Registrar en `docs/pruebas/complejidad-algoritmo.md` el fragmento de código, el desglose de los puntos de decisión identificados y el valor resultante de $V(G)$.

---

##### Kata 2 (Cinturón marrón / Nivel avanzado). Pruebas de camino básico (*Basis Path Testing*) e instrumentación con Logpoints

* **Contexto técnico.** Una vez determinado que un método tiene $V(G) = k$, debemos definir exactamente $k$ caminos independientes que garanticen que cada arista del grafo se recorre al menos una vez, verificando su ejecución mediante *Logpoints*.
* **Misión de la kata:**
    1. A partir del valor $V(G)$ obtenido en la Kata 1, define en Markdown la lista de los caminos básicos independientes enumerando los nodos que recorre cada uno (por ejemplo: *Camino 1: Inicio -> Entrada no válida -> Fin*; *Camino 2: Inicio -> Entrada válida -> Rama A -> Fin*, etc.).
    2. En el archivo fuente Java, sustituye cualquier sentencia `System.out.println` de prueba que tuvieras por **Logpoints silenciosos** de IntelliJ IDEA (con la opción *Suspend* desmarcada).
    3. Configura el mensaje del Logpoint para que imprima en cada iteración:
       `"[TRACE] Camino activo: " + nombreVariableEstado + " | Valor: " + variableControl`
    4. Ejecuta sucesivamente las entradas diseñadas para cada camino básico y comprueba en la pestaña **Debugger Console** cómo se generan las trazas limpias sin ensuciar la salida estándar del programa.

---

##### Kata 3 (Cinturón negro / «Hacker AzaharTech»). Detección de bucles infinitos en caliente y salida forzada (*Force Return*)

* **Contexto de arquitectura.** El mayor riesgo de programar bucles complejos en el Sprint 3 es el bucle infinito: el programa deja de responder, la CPU se satura y el programador suele verse obligado a forzar el cierre violento del proceso perdiendo los datos de la sesión. Los desarrolladores senior interceptan y desarman bucles infinitos en caliente con el depurador.
* **Misión de la kata:**
    1. En una rama experimental de tu proyecto, simula intencionadamente una condición de parada defectuosa en un bucle `while` (por ejemplo, omitiendo el incremento del contador o estableciendo una condición inalcanzable).
    2. Arranca el programa en modo depuración (**Debug**, `Shift + F9`).
    3. Introduce los datos para activar el bucle infinito: observa cómo la aplicación se queda congelada y no devuelve el control a la consola.
    4. En la barra de herramientas del depurador de IntelliJ, pulsa el botón **Pause Program** (icono de dos barras verticales de pausa). La JVM suspenderá inmediatamente el hilo en la línea exacta del bucle donde se encuentra atrapado.
    5. En el panel **Variables**, crea un **Watch** (`+`) con la condición de salida del bucle para entender por qué nunca se vuelve falsa.
    6. **Maniobra de rescate (*Force Return*):**
        * En el panel **Frames**, haz clic derecho sobre el marco del método actual atascado en el bucle.
        * Selecciona la opción **Force Return**.
        * Si el método devuelve un valor, introduce un retorno seguro (por ejemplo, `false`, `0` o `null`).
    7. Observa cómo el depurador aborta la ejecución del bucle anómalo y devuelve el control al método llamador sin que la aplicación se caiga ni se corrompa el proceso de depuración.
    8. Restaura la condición correcta en el código fuente, recompila con Maven y haz commit de las pruebas estructurales bajo el mensaje:
       `test(core): verificado conjunto de caminos basicos y complejidad ciclomática VG=X`.

### Día 18 - 1 sesión

---

#### Sesión única. Taller de consolidación: El ciclo de vida del defecto (*Bug Lifecycle*), verificación de regresión y cierre formal del Sprint 3

---

#### 1. Caso guía en AzaharTech

Viernes 13 de noviembre de 2026. Son las cuatro de la tarde en las oficinas de **AzaharTech** en Castellón de la Plana. La jornada representa un hito fundamental del primer trimestre: hoy concluye formalmente el **Sprint 3: «El ciclo del error: pruebas y depuración»**.

A lo largo de las últimas tres semanas, el equipo ha dado un salto cualitativo en su madurez técnica: ha dejado atrás la depuración artesanal basada en imprimir mensajes por consola para dominar el depurador interactivo de IntelliJ IDEA, la inspección de variables en memoria RAM, el análisis de la pila de llamadas (*Frames*), el diseño de pruebas de caja negra (clases de equivalencia y valores límite), la medición de cubrimiento (*Coverage*) y el cálculo de la complejidad ciclomática de McCabe.

En la pantalla principal, **Laia Claramunt** y **Pau Ferrer** abren el tablero del proyecto del **IES El Caminàs**. Todos los módulos que la semana pasada provocaban bloqueos en los bucles o excepciones por valores fuera de rango se encuentran ahora corregidos.

Laia se dirige a los desarrolladores del equipo:

> *«Habéis transformado un código frágil e inestable en un producto predecible y blindado contra entradas inesperadas. Sin embargo, en el desarrollo profesional de software corporativo existe una regla inquebrantable:* *un fallo no está solucionado simplemente porque el programador que lo escribió diga 'en mi ordenador ya no falla'»* [10, 12].
>
> *Para dar por cerrado un defecto, debemos someterlo a una* *prueba de regresión* *—garantizando que el arreglo no ha destruido accidentalmente una funcionalidad previa—, verificarlo mediante una auditoría técnica cruzada con otro compañero de consultoría y actualizar formalmente su estado en el ciclo de vida de la incidencia.*
>
> *Hoy cerraremos el ciclo completo del Sprint 3: formalizaremos la* *matriz de trazabilidad de pruebas*, *auditaremos por parejas los módulos desarrollados y utilizaremos la* *interfaz gráfica de IntelliJ IDEA* *para realizar el commit y push visual definitivo que dejará el repositorio sellado antes de iniciar el lunes el Sprint 4: la programación orientada a objetos propia y la gestión de ramas en Git»* [4, 11].

---

#### 2. El Ciclo de Vida del Defecto (*Bug Lifecycle*) y las Pruebas de Regresión

En la ingeniería de software profesional regulada por estándares de calidad, un error o defecto técnico (*bug*) no es una simple anotación en una libreta: es una entidad con ciclo de vida propio que atraviesa estados unívocos desde que se detecta hasta que se certifica su corrección definitiva [3, 4].

```
                  EL CICLO DE VIDA FORMAL DE UNA INCIDENCIA
                  
        ┌─────────────┐         ┌──────────────┐         ┌───────────────┐
   ────►│    NUEVO    ├────────►│  ASIGNADO    ├────────►│ EN RESOLUCIÓN │
        │   (Open)    │         │  (Assigned)  │         │ (In Progress) │
        └─────────────┘         └──────────────┘         └───────┬───────┘
                                                                 │
                                                                 ▼
        ┌─────────────┐         ┌──────────────┐         ┌───────────────┐
   ◄────┤   CERRADO   │◄────────┤  VERIFICADO  │◄────────┤   RESUELTO    │
        │  (Closed)   │ (Pasa)  │ (Regression) │ (Fix)   │  (Resolved)   │
        └─────────────┘         └───────┬──────┘         └───────────────┘
                                        │
                                        │ (Falla)
                                        ▼
                                ┌──────────────┐
                                │  REABIERTO   │
                                │  (Reopened)  │
                                └──────────────┘
```

##### Los estados de una incidencia técnica
1. **Nuevo (*Open*):** La anomalía ha sido detectada y registrada formalmente con su plantilla de informe técnico (*Bug Report*), detallando pasos para reproducirla y captura del comportamiento observado.
2. **Asignado (*Assigned*):** El equipo asigna la incidencia al desarrollador responsable de ese componente de software.
3. **En resolución (*In Progress*):** El desarrollador analiza el fallo con el depurador, identifica la causa raíz en memoria y modifica la lógica algorítmica.
4. **Resuelto (*Resolved*):** Se ha aplicado una corrección técnica en el código que supera el caso de prueba conflictivo.
5. **Verificado (*Verified / Re-test*):** Un segundo desarrollador (auditor de aseguramiento de calidad) reproduce de nuevo la prueba de forma independiente para certificar la corrección.
6. **Cerrado (*Closed*):** El defecto se archiva formalmente en el sistema documental tras superar las pruebas de regresión.
7. **Reabierto (*Reopened*):** Si la verificación falla o la corrección introduce nuevos fallos colaterales, la incidencia vuelve a asignarse al programador.

##### Las Pruebas de Regresión (*Regression Testing*)
Cuando se altera una línea de código para subsanar un fallo (por ejemplo, corregir un operador relacional `<` por `<=` en una condición de parada), existe siempre el peligro inminente de causar **regresión**: romper caminos lógicos o casos de uso que antes funcionaban a la perfección.

Una **prueba de regresión** consiste en volver a ejecutar de forma íntegra **toda** la batería de casos de prueba previamente aprobados tras aplicar el parche. Solo si se cumplen dos condiciones simultáneas se valida el cambio:
* El nuevo caso de prueba que antes fallaba ahora se ejecuta con éxito.
* Todos los casos de prueba históricos antiguos continúan pasando sin ninguna alteración ni degradación.

---

#### 3. Matriz de Trazabilidad de Pruebas (*Traceability Matrix*)

Para certificar ante el cliente (en nuestro caso, el IES El Caminàs) que el software cumple los estándares contractuales, AzaharTech exige estructurar la **matriz de trazabilidad bidireccional**, vinculando cada Requisito Funcional (**RF**) con sus Casos de Prueba (**CP**) y sus Incidencias resueltas (**INC**) [2, 3]:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        MATRIZ DE TRAZABILIDAD (SPRINT 3 - AZAHARTECH)                  │
├──────────────┬────────────────────────┬──────────────┬───────────────┬─────────────────┤
│ Requisito    │ Descripción Funcional  │ Caso Prueba  │ Incidencia    │ Estado técnico  │
├──────────────┼────────────────────────┼──────────────┼───────────────┼─────────────────┤
│ RF-CAM-01    │ Validación de carné    │ CP-ACC-001   │ INC-01 (Crash │ CERRADO         │
│              │ de visitante [12-99]   │ CP-ACC-002   │ por nulo)     │ y verificado    │
├──────────────┼────────────────────────┼──────────────┼───────────────┼─────────────────┤
│ RF-CAM-02    │ Control horario de     │ CP-HOR-001   │ INC-02 (Bucle │ CERRADO         │
│              │ retraso lectivo        │ CP-HOR-002   │ off-by-one)   │ y verificado    │
├──────────────┼────────────────────────┼──────────────┼───────────────┼─────────────────┤
│ RF-CAM-03    │ Notificación tras 3    │ CP-SAN-001   │ Ninguna       │ VERIFICADO      │
│              │ faltas consecutivas    │              │ detectada     │ (sin defectos)  │
└──────────────┴────────────────────────┴──────────────┴───────────────┴─────────────────┘
```

---

#### 4. Dinámica de laboratorio: Auditoría técnica cruzada por pares (30 minutos)

Durante la parte práctica de la sesión, los desarrolladores trabajan por parejas intercambiando sus puestos de trabajo para realizar una **Auditoría Técnica Cruzada (*Peer QA Review*)**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                        FASES DE LA AUDITORÍA CRUZADA (DÍA 18)                          │
├──────────────┬──────────────────┬──────────────────────────────────────────────────────┤
│ Tiempo       │ Fase             │ Operación técnica requerida                          │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 00 – 10  │ 1. Inspección de │ Abrir en IntelliJ IDEA el proyecto del compañero.    │
│              │    incidencias   │ Abrir el archivo `docs/incidencias/INCIDENCIA-01.md`. │
│              │                  │ Reproducir bajo depuración el fallo originalmente    │
│              │                  │ documentado para verificar el punto de ruptura.      │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 10 – 20  │ 2. Prueba de     │ Ejecutar toda la batería de casos de prueba del      │
│              │    regresión     │ compañero con la herramienta **Run with Coverage**.  │
│              │                  │ Comprobar que no hay regresiones y que las líneas    │
│              │                  │ críticas se mantienen en verde.                      │
├──────────────┼──────────────────┼──────────────────────────────────────────────────────┤
│ Min 20 – 30  │ 3. Firma del     │ Redactar conjuntamente el informe de cierre técnico  │
│              │    acta de cierre│ en `docs/pruebas/cierre-sprint3.md` con las métricas │
│              │                  │ finales consolidadas.                                │
└──────────────┴──────────────────┴──────────────────────────────────────────────────────┘
```

##### Guía de verificación según el proyecto de la bolsa:
* **En Aventura conversacional:** El auditor introduce el valor de daño crítico exacto que reduce la vida del personaje a `0` puntos. Comprueba que el juego redirige a la escena de derrota limpiamente, sin excepciones de puntero nulo y confirmando que el inventario no se desborda en el bucle principal.
* **En Motor de recomendación:** El auditor ejecuta el cálculo de afinidad suministrando un vector de usuario con `0` valoraciones coincidentes. Verifica que el algoritmo maneja la división sin generar valores indefinidos (`NaN`) y que la lista ordenada final no se rompe.
* **En Simulador de físicas 2D:** El auditor simula una partícula impactando a velocidad máxima contra la esquina superior derecha ($x_{max}, y_{max}$). Confirma bajo el depurador que las dos componentes de aceleración y rebote se recalculan simultáneamente sin atrapar la simulación en un bucle infinito de colisión.
* **En Bóveda de contraseñas:** El auditor introduce una contraseña de prueba con la longitud máxima permitida (32 caracteres exactos). Verifica que el algoritmo de cifrado no trunca la cadena ni genera excepciones de desbordamiento de índice en memoria.

##### Plantilla de cierre técnico
Cada pareja consolida el informe de calidad en el archivo `docs/pruebas/cierre-sprint3.md`:

```markdown
### Acta de Cierre Técnico de Pruebas y Depuración (Sprint 3)

- **Desarrollador responsable:** [Nombre del Alumno]
- **Auditor de calidad asignado:** [Nombre del Compañero]
- **Estado de incidencias registradas:** 100% resueltas y verificadas (0 incidencias abiertas).
- **Métrica de cubrimiento alcanzada:** >= 85% de líneas / >= 80% de ramas.
- **Complejidad Ciclomática máxima auditada:** V(G) <= 6 (Módulo estructurado según estándares).
- **Resultado de pruebas de regresión:** SATISFACTORIO (Sin efectos colaterales detectados).
- **Dictamen técnico final:** APTO para integración en Sprint 4.
```

---

#### 5. Cierre formal mediante Commit y Push visual en IntelliJ IDEA

En cumplimiento de las directrices metodológicas de AzaharTech para los primeros sprints, **todas las operaciones de control de versiones con Git se realizan exclusivamente a través del entorno gráfico de IntelliJ IDEA**, utilizándolo como un buzón visual de subida seguro y trazable [10]:

```
                VENTANA VISUAL DE COMMIT EN INTELLIJ IDEA (Ctrl + K)
                
 ┌────────────────────────────────────────────────────────────────────────┐
 │ Commit: Local Changes                                                  │
 ├────────────────────────────────────────────────────────────────────────┤
 │ Changes (3 files)                                                      │
 │  [x] docs/pruebas/cierre-sprint3.md       (acta de cierre y auditoría) │
 │  [x] docs/incidencias/INCIDENCIA-01.md    (estado cambiado a CERRADO)  │
 │  [x] src/core/ValidadorLogica.java        (algoritmo blindado final)   │
 ├────────────────────────────────────────────────────────────────────────┤
 │ Mensaje de confirmación:                                               │
 │ ┌────────────────────────────────────────────────────────────────────┐ │
 │ │ docs(qa): auditoria cruzada completada y cierre formal del sprint 3│ │
 │ └────────────────────────────────────────────────────────────────────┘ │
 │                                                                        │
 │ [ Commit v ]  ──► Desplegar flecha: [ Commit and Push... ]            │
 └────────────────────────────────────────────────────────────────────────┘
```

##### Pasos visuales paso a paso:
1. **Abrir el panel de confirmación:**
    * Pulsa el atajo universal **`Ctrl + K`** (en Windows/Linux) o haz clic directo en la pestaña vertical izquierda **Commit** (`Alt + 0`).
2. **Auditoría visual de diferencias (*Diff Viewer*):**
    * En la lista de archivos modificados (*Changes*), haz doble clic sobre `cierre-sprint3.md` y `ValidadorLogica.java`. Comprueba en el panel comparativo que las líneas agregadas coinciden con el acta y las correcciones de frontera.
3. **Selección de casillas de verificación:**
    * Marca con un visto bueno (`[x]`) los archivos documentales y de código validados durante la sesión.
4. **Redacción del mensaje de commit:**
    * En la caja de texto inferior escribe el mensaje estructurado de cierre:
      `docs(qa): auditoria cruzada completada y cierre formal del sprint 3`
5. **Confirmación y sincronización con GitHub (*Commit and Push*):**
    * Haz clic sobre la pequeña flecha desplegable situada en el botón azul inferior de **Commit** y selecciona la opción **Commit and Push...** (o pulsa **`Ctrl + Alt + K`**).
    * Se abrirá el cuadro de diálogo de confirmación de subida (*Push Commits*). Verifica que el destino sea tu repositorio remoto personal en GitHub y pulsa el botón azul **Push**.

Al aparecer la notificación flotante de confirmación en verde en la esquina inferior del IDE (*«Pushed 1 commit to origin/main»*), el **Sprint 3: «El ciclo del error: pruebas y depuración»** queda oficialmente completado y auditado [10, 11]. El equipo está preparado para comenzar el próximo lunes el **Sprint 4**, donde nos adentraremos en el diseño de clases orientadas a objetos propias y el flujo de ramas de Git [4, 11].


