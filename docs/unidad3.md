# Sprint 3. El ciclo del error: pruebas y depuración

---

## Semana 7. Depuración interactiva e inspección de memoria

---

### Día 13 - 2 sesiones

---

#### Sesión 1. Teoría. La radiografía del código en ejecución: del print casero a la depuración interactiva en memoria RAM

---

#### 1. Caso guía en AzaharTech

Son las tres de la tarde del lunes 26 de octubre de 2026. En la sede de **AzaharTech** en Castellón de la Plana, arranca formalmente la primera jornada del **Sprint 3: «El ciclo del error: pruebas y depuración»**.

Tras haber consolidado la estructura estándar del proyecto, la compilación y la integración de librerías externas de Maven en el ciclo anterior, el equipo técnico se reúne en la sala de operaciones. En la pantalla de proyección, **Laia Claramunt** muestra una captura del panel de incidencias remitido a primera hora por el coordinador de infraestructuras del **IES El Caminàs**:

> *«Equipo, entramos en una de las fases más decisivas de la ingeniería de software: el diagnóstico y control del error. La aplicación de control de acceso al centro (`ControlAccesoQR`) ha incorporado lógica de decisión condicional (`if-else`, operadores lógicos combinados y bifurcaciones con `switch`) para evaluar a los visitantes en los tornos del vestíbulo.*
>
> *Sin embargo, esta mañana el sistema ha comenzado a fallar de forma errática: personas con temperatura corporal normal y DNI válido están siendo rechazadas en pantalla con el aviso de 'Fiebre o lectura anómala', mientras que en otros momentos el terminal abre el torno aunque el aforo del edificio figure como completamente agotado.*
>
> *El impulso natural del desarrollador principiante ante un fallo lógico es llenar el código fuente de impresiones por consola: `System.out.println("Pasa por el if 1")`, `System.out.println("Variable temp = " + temp)` y `System.out.println("Pasa por aquí 2")`. En la industria del software profesional, esa práctica es un antipatrón ineficiente, peligroso y sucio que degrada el repositorio y no permite interactuar con la máquina virtual.*
>
> *Hoy veremos cómo a congelar el tiempo: utilizaremos el* *depurador interactivo (Debugger)* *de IntelliJ IDEA para detener la JVM, examinar el estado exacto de la memoria RAM, evaluar predicados lógicos en caliente y alterar variables sin modificar ni una sola línea de código fuente»*.

---

#### 2. La naturaleza de los errores y las limitaciones del «Print Debugging»

En el ciclo de vida del software, los fallos no son accidentes aislados: son una consecuencia directa de la complejidad lógica. Para erradicarlos con rigor técnico, primero debemos clasificarlos según su comportamiento en el sistema:

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
│    (Semántico)      │ romperse, pero produce un       │ lo que se programó, pero no lo │
│                     │ resultado funcional incorrecto. │ que las reglas exigían.        │
└─────────────────────┴─────────────────────────────────┴────────────────────────────────┘
```

##### Las cuatro trampas de ingeniería del «Print Debugging»
Llenar el código con instrucciones de consola para rastrear variables presenta graves problemas en el entorno profesional:

1. **Contaminación del repositorio.** Obliga a modificar archivos fuente funcionales solo para depurar, ensuciando los commits con líneas provisionales que luego hay que recordar borrar a mano.  
2. **Coste temporal de recompilación.** Cada vez que queremos inspeccionar una variable nueva o cambiar el texto de un print, debemos guardar, reconstruir el proyecto con Maven y reiniciar la ejecución desde cero.  
3. **Pérdida de dimensionalidad y contexto.** Un `System.out.println` muestra únicamente una foto plana de texto en la consola, pero oculta la pila de llamadas (*Call Stack*), los objetos en segundo plano y el estado global de la memoria.  
4. **Riesgo crítico de fuga de datos en producción.** Olvidar trazas de depuración en despliegues reales puede filtrar contraseñas en texto plano, datos personales de usuarios o saturar el almacenamiento de logs del servidor.  

```
COMPARATIVA METODOLÓGICA EN INGENIERÍA:

A) Depuración artesanal primitiva (Print Debugging):
   [Modificar código] ──► [Recompilar] ──► [Lanzar app] ──► [Leer consola] ──► [Borrar print]
   (Flujo lento, destructivo y completamente ciego ante bifurcaciones complejas)

B) Depuración interactiva profesional (IDE Debugger):
   [Poner Breakpoint] ──► [Lanzar Debug] ──► [Congelar JVM] ──► [Inspeccionar / Alterar RAM]
   (Sin tocar código, inspección multidimensional e inmediata del estado interno)
```

---

#### 3. Anatomía del depurador interactivo en IntelliJ IDEA

Cuando lanzamos nuestro software mediante el botón de depuración (**Debug** o `Shift + F9`), IntelliJ no invoca a la JVM de forma estándar: la arranca comunicándose mediante el protocolo **JDWP** (*Java Debug Wire Protocol*). Esto permite al IDE tomar el control absoluto del hilo de ejecución en memoria.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   MANDOS DEL DEPURADOR EN INTELLIJ IDEA                │
├──────────────┬──────────────┬──────────────────────────────────────────┤
│ Acción       │ Atajo        │ Propósito técnico                        │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Over    │ F8           │ Avanza a la siguiente instrucción sin    │
│              │              │ entrar al interior de las funciones.     │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Into    │ F7           │ Entra dentro del método invocado en esa  │
│              │              │ línea para auditar su lógica interna.    │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Step Out     │ Shift + F8   │ Sale del método actual y regresa a la    │
│              │              │ línea del método que lo llamó en el main.│
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Resume       │ F9           │ Reanuda la ejecución libre hasta topar   │
│ Program      │              │ con el siguiente punto de ruptura.       │
├──────────────┼──────────────┼──────────────────────────────────────────┤
│ Evaluate     │ Alt + F8     │ Abre una consola para evaluar fórmulas y │
│ Expression   │              │ métodos en caliente contra la RAM real.  │
└──────────────┴──────────────┴──────────────────────────────────────────┘
```

##### Tipos de puntos de ruptura (*Breakpoints*)
* **Punto de ruptura estándar.** Se activa haciendo clic en el canal lateral izquierdo (*gutter*) junto al número de línea. Marca una línea donde la JVM suspenderá el hilo de ejecución justo **antes** de procesarla.
* **Punto de ruptura condicional.** Clic derecho sobre el punto rojo del breakpoint. Permite definir una expresión lógica booleana en Java (por ejemplo, `tempVestibulo > 37.5` o `aforoDisponible <= 0`). El programa solo se detendrá cuando la condición sea verdadera, evitando detenerse manualmente en accesos rutinarios válidos.

---

#### 4. Actividad activa interactiva: caza visual del fallo en la pizarra

Alba Torres y Pau Ferrer proyectan un extracto de la lógica condicional del terminal del IES El Caminàs que presenta una anomalía en la toma de decisiones:

```java
// Estado de la memoria RAM en el momento de la evaluación:
double tempVestibulo = 36.8;
int aforoDisponible = 0;
String dni = "12345678Z";

boolean temperaturaApta = (tempVestibulo >= 35.0 && tempVestibulo <= 37.5);
boolean aforoSuficiente = (aforoDisponible > 0);

if (!temperaturaApta) {
    System.out.println("RECHAZADO: Fiebre o lectura anómala.");
} else if (!aforoSuficiente) {
    System.out.println("RECHAZADO: Aforo saturado.");
} else {
    System.out.println("ACCESO AUTORIZADO - TORNO ABIERTO");
}
```

*Los estudiantes analizan el fragmento durante 3 minutos en su puesto de trabajo:*

1. **Trazado mental de predicados booleanos.**

    - ¿Qué valor exacto tiene `temperaturaApta`? (`true`).  
    - ¿Qué valor evalúa `!aforoSuficiente`? (`true`, ya que el aforo es `0`).  

2. **Evaluación de prioridades de salto.**

    - ¿Por qué el mensaje de fiebre no se ejecuta pero sí el de aforo?  

3. **Puesta en común en la pizarra.** Un estudiante voluntario marca con el rotulador en qué línea exacta colocaría el punto de ruptura para comprobar si las variables contienen los datos esperados antes de que la consola imprima el veredicto.  

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

> **Dinámica del dojo de entrenamiento:**
> 
> * La **Kata 1 es de realización obligatoria** para todo el grupo: constituye el núcleo técnico imprescindible y la base del entregable de la sesión.  
> * Las **Katas 2 y 3 son optativas y de ampliación**: están diseñadas para que los estudiantes que resuelvan rápidamente la primera práctica no se queden inactivos, permitiéndoles subir de nivel y adquirir competencias avanzadas en depuración profesional.  

---

##### Kata 1 · Obligatoria (cinturón blanco / nivel base). Diagnóstico de bifurcaciones e inspección de variables

* **Objetivo.** Iniciar el proyecto bajo el control del depurador, fijar puntos de ruptura en las estructuras de selección condicional e inspeccionar el estado de las variables locales desde la pestaña **Variables** de IntelliJ IDEA sin recurrir a impresiones por consola.
* **Misión según tu proyecto elegido:**
    * **En Aventura conversacional.** Pon un punto de ruptura en la línea del condicional de combate `if (vidaActual <= 0)`. Lanza la aplicación en modo Debug (`Shift + F9`). Comprueba en el panel lateral el valor de `vidaActual` y el nombre del arma activa antes de ramificar la escena hacia la victoria o la derrota.
    * **En Motor de recomendación.** Coloca el breakpoint en la condición de afinidad `if (afinidad >= 75.0)`. Inspecciona el valor de punto flotante de `afinidad` y confirma en memoria qué etiqueta de recomendación se asignará al usuario.
    * **En Simulador de físicas 2D.** Sitúa el breakpoint en la condición de frontera `if (posicionX >= LIMITE_DERECHO)`. Observa el valor de la coordenada horizontal y el signo de la velocidad antes de ejecutarse el rebote.
    * **En Bóveda de contraseñas.** Pon el breakpoint en la validación de formato `if (password.length() >= 8)`. Inspecciona el contenido del texto en memoria y el valor booleano resultante.

---

##### Kata 2 · Optativa (cinturón marrón / nivel avanzado). Navegación táctica (Step Over / Step Into) y evaluación al vuelo (`Alt + F8`)

* **Contexto técnico.** Cuando una condición invoca un método auxiliar (por ejemplo, una función que calcula un descuento, valida un perfil o realiza una operación matemática), saltar la línea a ciegas no es suficiente: debemos zambullirnos en su interior y comprobar qué parámetros recibe.
* **Misión de la kata:**
    1. Sitúa un punto de ruptura en la cabecera de la llamada a una función auxiliar dentro de tu bifurcación condicional.
    2. Arranca en modo depuración (`Shift + F9`).
    3. Utiliza **Step Over (`F8`)** para avanzar línea a línea. Al llegar a la llamada del método auxiliar, pulsa **Step Into (`F7`)** para saltar dentro de su código y revisar sus variables locales.
    4. Una vez auditada la primera instrucción interna, utiliza **Step Out (`Shift + F8`)** para regresar limpiamente a la línea de llamada en el método `main`.
    5. En una línea de bifurcación, pulsa **`Alt + F8` (Evaluate Expression)**.
    6. Escribe una fórmula de consulta en vivo en el cuadro emergente (por ejemplo, evaluar una comparación compuesta como `vidaActual > 0 && tieneArma`). Comprueba que IntelliJ evalúa la condición consultando directamente la RAM del hilo en pausa.
    7. Convierte el breakpoint en **Breakpoint condicional** (clic derecho sobre el punto rojo) para que la ejecución solo se congele cuando una variable tome un valor crítico (por ejemplo: `aforoDisponible == 0` o `vidaActual < 10`).

---

##### Kata 3 · Optativa (cinturón negro / «hacker AzaharTech»). Mutación de variables en memoria (*Set Value*) y Exception Breakpoint

* **Contexto de arquitectura.** Reproducir situaciones límite de error o permisos administrativos puede requerir múltiples entradas por teclado. Un desarrollador profesional altera el estado interno de la memoria durante la propia ejecución para someter al software a pruebas de estrés inmediatas.
* **Misión de la kata:**
    1. Pausa el programa en un punto de ruptura antes de una bifurcación condicional crítica.
    2. En el panel **Variables**, haz clic derecho sobre la variable objetivo y selecciona **Set Value...** (o pulsa **`F2`**).
    3. Modifica su valor directamente en la RAM (por ejemplo, cambia un valor `false` por `true`, o una variable de aforo de `0` a `50`).
    4. Pulsa `Intro` y avanza con **`F8`**: observa cómo el programa toma la rama lógica alternativa sin que hayas tenido que reescribir código ni recompilar el proyecto con Maven.
    5. **Configuración de un Exception Breakpoint:**
        * Abre el gestor de puntos de ruptura (**`Ctrl + Shift + F8`** o menú *Run -> View Breakpoints...*).
        * Pulsa el icono `+` (Add) y selecciona **Java Exception Breakpoint**.
        * Escribe en el buscador `java.lang.NullPointerException` o `java.lang.NumberFormatException`.
        * Retira los demás breakpoints de tu código y pulsa **Resume Program (`F9`)**.
        * Comprueba que, en caso de desencadenarse dicha excepción, la JVM se detendrá quirúrgicamente en la línea exacta que originó el fallo antes de abortar el programa.

---

#### Entregables de la sesión

Al concluir el trabajo en el dojo, debes dejar consolidada la preparación de tu código fuente en el repositorio personal:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                               ENTREGABLES DEL DÍA 13                                   │
├─────────────────────┬──────────────────────────────────────────────────────────────────┤
│ Archivo modificado  │ src/main/java/... (Clase de tu proyecto)                         │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Qué contiene        │ 1. Limpieza de código: erradicar cualquier sentencia de          │
│                     │    "System.out.println" utilizada como depuración.               │
│                     │ 2. Modularización: asegurar que la condición lógica principal    │
│                     │    está encapsulada en un método auxiliar para permitir la       │
│                     │    navegación con Step Into (F7).                                │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Operación en el IDE │ Commit en IntelliJ IDEA (Ctrl + K)                               │
│                     │ Mensaje: refactor(core): modularizada seleccion condicional      │
│                     │          para depuracion interactiva                             │
└─────────────────────┴──────────────────────────────────────────────────────────────────┘
```

##### Pasos visuales paso a paso en IntelliJ IDEA:
1. Pulsa **`Ctrl + K`** (o abre la pestaña lateral vertical **Commit** / `Alt + 0`).
2. En la lista de archivos modificados, haz doble clic sobre tu clase Java para verificar con el visor comparativo (*Diff viewer*) que se han eliminado, si había, las impresiones de depuración de consola.
3. Marca únicamente la casilla de verificación de tu archivo fuente Java.
4. Redacta el mensaje formal estructurado:
   `refactor(core): modularizada seleccion condicional para depuracion interactiva`
5. Haz clic en el botón azul **Commit** (sin Push todavía; la subida remota se formalizará junto con la documentación de la siguiente sesión).

---

### Día 14 - 1 sesión

---

#### Sesión única. Taller práctico: la pila de llamadas (*Call Stack*), diagnóstico de excepciones y registro del primer defecto técnico

---

#### 1. Caso guía en AzaharTech

Miércoles 28 de octubre de 2026. Es la segunda jornada de la **Semana 7** en AzaharTech. El equipo ha modularizado el código del sistema de accesos del **IES El Caminàs** mediante funciones auxiliares que encapsulan la evaluación de tipos de usuario con `switch` (`obtenerDescripcionPerfil`, `determinarZonaAutorizada`, etc.).

Al ejecutar el terminal de acceso con una entrada de usuario imprevista, la consola de IntelliJ arroja una cascada de más de treinta líneas de texto en color rojo:

```text
Exception in thread "main" java.lang.StringIndexOutOfBoundsException: String index out of range: 0
    at java.base/java.lang.StringLatin1.charAt(StringLatin1.java:48)
    at java.base/java.lang.String.charAt(String.java:1517)
    at es.azahartech.caminas.ControlAccesoQR.determinarPerfil(ControlAccesoQR.java:82)
    at es.azahartech.caminas.ControlAccesoQR.main(ControlAccesoQR.java:134)
```

**Pau Ferrer** se acerca a la mesa del desarrollador júnior:

> *«No te asustes ni cierres la consola. El primer reflejo del programador novato al ver una traza roja es hacer scroll frenético buscando una palabra conocida o borrar código a ciegas. Los inexpertos ven un fallo catastrófico; los profesionales vemos una autopsia perfecta.*
>
> *Ese bloque de texto es el mapa exacto del hilo de ejecución: la pila de llamadas (**Call Stack**). Nos dice que en la línea 134 del `main`, invocamos a `determinarPerfil`, y que este intentó ejecutar `charAt(0)` sobre un String que el usuario había introducido completamente vacío al pulsar directamente la tecla Intro.*
>
> *Hoy aprenderemos a viajar hacia atrás en el tiempo por los marcos de la pila (**Frames**) en IntelliJ y crearemos el documento técnico oficial del Sprint 3: `ed/docs/ciclo-error.md`, donde redactaremos el primer informe formal de incidencia técnica (Bug Report)»*.

---

#### 2. La Pila de Llamadas (*Call Stack*) y los marcos de ejecución (*Frames*)

Cuando un programa Java se ejecuta, la máquina virtual gestiona la memoria de ejecución mediante una estructura de pila **LIFO** (*Last In, First Out*). Cada vez que un método llama a otro, se apila un nuevo **marco de pila (*Stack Frame*)** que contiene sus parámetros, variables locales y el contador de programa. Cuando el método termina, su marco se destruye y el control regresa a la línea del método llamador.

```
       FLUJO DE APILAMIENTO EN LA JVM (CALL STACK)
       
       ┌────────────────────────────────────────────────────────┐ ▲ Cima de la pila
       │ Frame 3: StringLatin1.charAt(0)                        │ │ (Lugar exacto del fallo)
       ├────────────────────────────────────────────────────────┤ │
       │ Frame 2: ControlAccesoQR.determinarPerfil(perfilBruto) │ │ (Método auxiliar con switch)
       ├────────────────────────────────────────────────────────┤ │
       │ Frame 1: ControlAccesoQR.main(args)                    │ │ (Punto de entrada de la app)
       └────────────────────────────────────────────────────────┘ │
                                                                  
       ◄─── Retorno del control / Destrucción del marco
```

##### Navegación interactiva por Frames en IntelliJ IDEA
Cuando la ejecución está detenida en un punto de ruptura o interceptada por una excepción:

1. **La columna izquierda de la pestaña Debug lista los Frames:** En la parte superior aparece el método en el que estamos detenidos en este milisegundo; debajo, el método que lo invocó, y así sucesivamente hasta llegar al `main`.  
2. **Viaje en el tiempo sin alterar el flujo:** Si haces clic en `Frame 2` o `Frame 1`, el editor de código saltará automáticamente a esa línea y el panel **Variables** se actualizará al instante para mostrar cómo estaban las variables en ese método superior, permitiéndote comprobar qué argumentos le envió al método que falló.  

---

#### 3. Laboratorio práctico. Provocación de fallo y auditoría en tu proyecto

> **Dinámica de laboratorio:**
> 
> * La **actividad principal (diagnóstico y redacción del informe)** es obligatoria para todos los alumnos.  
> * **Ampliación para quienes terminen antes:** Configura filtros en la pestaña Debug de IntelliJ (*Settings -> Build, Execution, Deployment -> Debugger -> Stepping*) para activar *Do not step into library classes* y omite automáticamente clases internas de Java (`java.*`, `javax.*`), observando cómo se limpia la vista de frames.  

Durante los primeros 20 minutos de la sesión, provoca deliberadamente una excepción en tu software suministrando una entrada inesperada por teclado para auditarla mediante el depurador:

* **En Aventura conversacional:** Pulsa directamente la tecla Intro sin escribir ningún comando en la selección de acción del menú. Pausa la ejecución con el depurador y diagnostica en el panel *Frames* por qué `comando.charAt(0)` falla cuando la cadena está vacía.  
* **En Motor de recomendación:** Introduce texto alfabético en un campo numérico procesado con `Integer.parseInt(teclado.nextLine())`. Rastrear en el *Call Stack* qué método de captura originó el `NumberFormatException`.  
* **En Simulador de físicas 2D:** Pasa un valor nulo o no inicializado a una función cinemática y comprueba en los marcos de pila qué método propagó el cálculo erróneo.  
* **En Bóveda de contraseñas:** Genera un fallo al evaluar la longitud de una contraseña nula o vacía (`password.length()`) y examina las variables del método llamador.  

---

#### Entregables de la sesión

En esta sesión debes crear en tu repositorio el documento técnico del Sprint 3: **`ed/docs/ciclo-error.md`**, y redactar su primer apartado:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                               ENTREGABLES DEL DÍA 14                                   │
├─────────────────────┬──────────────────────────────────────────────────────────────────┤
│ Archivo creado      │ ed/docs/ciclo-error.md                                           │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Ubicación           │ azahartech/tu-equipo/tus-apellidos-nombre/ed/docs/ciclo-error.md │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Apartado a redactar │ APARTADO 1. Ficha técnica de la incidencia (Bug Report)          │
│                     │ Completar la ficha técnica oficial del fallo detectado (INC-01): │
│                     │ • Entorno técnico (OpenJDK 21, IntelliJ IDEA, Maven).            │
│                     │ • Pasos exactos para reproducir el fallo.                        │
│                     │ • Comportamiento observado VS comportamiento esperado.           │
│                     │ • Diagnóstico del Call Stack (Frames inspeccionados).            │
│                     │ • Estado actual de la incidencia: EN RESOLUCIÓN (In Progress).   │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Operación en el IDE │ Commit visual en IntelliJ IDEA (Ctrl + K):                       │
│                     │ Mensaje: docs(qa): creado ciclo-error.md con apartado 1          │
│                     │          de incidencia tecnica INC-01                            │
└─────────────────────┴──────────────────────────────────────────────────────────────────┘
```

##### Plantilla obligatoria a incluir en `ed/docs/ciclo-error.md`:

```markdown
# Memoria Técnica - Sprint 3: El ciclo del error: pruebas y depuración

## Apartado 1: Ficha técnica de la incidencia (Bug Report)

- **Identificador de incidencia:** INC-01
- **Módulo afectado:** [Indicar el método, clase o componente donde ocurrió el fallo]
- **Entorno de ejecución:** OpenJDK 21 LTS / IntelliJ IDEA Community / Apache Maven 3.9
- **Estado inicial:** EN RESOLUCIÓN (In Progress)

### 1. Descripción concisa del defecto
[Explicar con precisión qué ocurre cuando se introduce una entrada inválida o anómala]

### 2. Pasos exactos para reproducir
1. Ejecutar la aplicación en modo depuración (`Shift + F9`).
2. Introducir los datos previos requeridos por el programa.
3. [Describir la acción exacta que detona el error, ej: presionar Enter sin escribir texto].

### 3. Comportamiento observado frente a comportamiento esperado
- **Comportamiento observado:** La aplicación se interrumpe de forma no controlada con la excepción `[NombreDeLaExcepcion]` en la consola.
- **Comportamiento esperado:** El programa debe validar previamente los datos mediante una bifurcación condicional, mostrar un mensaje de aviso amigable y permitir continuar la ejecución sin colapsar.

### 4. Diagnóstico técnico con el depurador y Call Stack
- **Punto de interrupción:** Archivo `[NombreArchivo.java]`, línea `[Nº]`.
- **Inspección de memoria (panel Variables):** La variable `[nombreVariable]` contenía el valor `[valor observado en RAM]`.
- **Trazabilidad en la pila de llamadas (Frames):**
    - `Frame 1 (origen):` Método `main` en la línea `[Nº]`.
    - `Frame 2 (llamada):` Método `[nombreMetodo]` en la línea `[Nº]`.
    - `Frame 3 (fallo):` Instrucción interna donde la JVM aborta la ejecución.
```

##### Pasos visuales para el commit en IntelliJ IDEA:
1. Abre la herramienta visual de control de versiones pulsando **`Ctrl + K`** (o haz clic en la pestaña lateral izquierda **Commit**).
2. En la lista de archivos, selecciona únicamente **`ed/docs/ciclo-error.md`**.
3. Redacta el mensaje formal estructurado:
   `docs(qa): creado ciclo-error.md con apartado 1 de incidencia tecnica INC-01`
4. Haz clic en el botón azul **Commit**.

El primer defecto técnico queda así formalmente registrado y diagnosticado en la memoria del proyecto.

## Semana 8. Clases de equivalencia y cobertura

---

### Día 15 - 2 sesiones

---

#### Sesión 1. Teoría. La ingeniería del caso de prueba: cómo probar sin perderse en el infinito

---

#### 1. Caso guía en AzaharTech

Lunes 2 de noviembre de 2026. Tras una primera semana de inmersión en el depurador interactivo, el equipo entra en las instalaciones de **AzaharTech**.

En la pantalla de la sala de reuniones, **Laia Claramunt** proyecta un formulario simple de la aplicación del **IES El Caminàs**: la ventana de registro de carnés temporales de visitante. El formulario solicita dos campos: la **edad del visitante** (de 12 a 99 años para acceder sin acompañante adulto) y un **código PIN de seguridad** de 4 dígitos (de `1000` a `9999`).

Laia mira a los desarrolladores y lanza una pregunta directa:

> *«Si quisiéramos demostrar matemáticamente que esta pequeña pantalla no tiene ningún fallo lógico, ¿cuántas combinaciones tendríamos que probar a mano?*
>
> *La edad admite teóricamente cualquier número entero de 32 bits en memoria (más de cuatro mil millones de valores), y el PIN tiene 9000 combinaciones válidas más infinitas cadenas alfanuméricas inválidas. Probar todas las entradas posibles por fuerza bruta (**exhaustion testing**) requeriría semanas de trabajo humano o miles de horas de computación inútil.*
>
> *En la industria del software no probamos al azar ni probamos todo: aplicamos técnicas formales de ingeniería para seleccionar el conjunto mínimo de datos que garantice la máxima detección de defectos. Hoy dejaremos de cazar bugs a ciegas y aprenderemos a diseñar* *casos de prueba profesionales* *utilizando dos pilares de las pruebas de caja negra:* *las clases de equivalencia* *y* *el análisis de valores límite»*.

---

#### 2. Metodología de caja negra: partición de clases de equivalencia

En las pruebas de **caja negra** (*Black-Box Testing*), nos centramos exclusivamente en las entradas y salidas del sistema según sus especificaciones funcionales, sin preocuparnos por cómo está escrito el código fuente por dentro.

```
                   MODELO DE CAJA NEGRA (BLACK-BOX)
                   
                     ┌───────────────────────────┐
  [Datos de entrada] │    Módulo / Algoritmo     │ [Resultado observado]
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
DIVISIÓN DE ESPACIO DE ENTRADAS (Ejemplo: edad válida entre 12 y 99 años):

    Clase inválida 1          Clase válida            Clase inválida 2
    (Menores de 12)        (Rango permitido)           (Mayores de 99)
◄─────────────────────────┬──────────────────────────┬────────────────────────►
 ... -5, 0, 1, ..., 11    │   12, 13, ..., 98, 99    │ 100, 101, 150 ...
                          │                          │
  Representante: 8        │   Representante: 25      │ Representante: 105
  (Espera: rechazo)       │   (Espera: aceptado)     │ (Espera: rechazo)
```

---

#### 3. Análisis de valores límite (*Boundary Value Analysis - BVA*)

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

#### 5. Actividad activa interactiva: caza de fronteras en la pizarra

Alba Torres y Pau Ferrer plantean en la pizarra el requisito del identificador temporal de visitante para el IES El Caminàs:
> *«El sistema genera una clave de acceso temporal de 4 cifras numéricas. Para evitar códigos triviales de prueba de fábrica, el valor del PIN debe estar comprendido estrictamente entre `1000` y `9999` (ambos inclusive)»*.

Durante 5 minutos, los estudiantes debaten y completan en la pizarra la tabla de valores críticos:

1. ¿Cuáles son los **valores límite** exactos que hay que probar? (`999`, `1000`, `1001`, `9998`, `9999`, `10000`).  
2. ¿Qué ocurre con los casos especiales de frontera cualitativa? (por ejemplo, valor nulo `null`, cadena vacía `""` o texto con letras `"100A"`).  
3. Conclusión guiada: *«Con solo 6 pruebas numéricas y 2 cualitativas cubrimos con rigor técnico el comportamiento de miles de combinaciones posibles»*.  

---

#### Sesión 2. Dojo de entrenamiento y katas de herramientas

> **Dinámica del dojo de entrenamiento:**
>
> * La **Kata 1 es de realización obligatoria** para todo el grupo: constituye el núcleo técnico imprescindible y la base del entregable de la sesión.
> * Las **Katas 2 y 3 son optativas y de ampliación**: están diseñadas para que los estudiantes que resuelvan rápidamente la primera práctica no se queden inactivos, permitiéndoles subir de nivel y adquirir competencias avanzadas en depuración profesional.

---

##### Kata 1 · Obligatoria (cinturón blanco / nivel base). Matriz de clases de equivalencia y batería BVA de tu proyecto

* **Objetivo.** Tomar una función crítica de validación o cálculo condicional de tu proyecto individual y diseñar su matriz formal de clases de equivalencia y su batería de valores límite en Markdown.
* **Misión según tu proyecto elegido:**
    * **En Aventura conversacional:** Analizar la función que procesa la compra de ítems en la tienda del juego. Parámetro: `oroDisponible` (debe ser mayor o igual al coste del ítem). Definir clases: saldo suficiente ($> coste$), saldo exacto ($== coste$), saldo insuficiente ($< coste$) y saldo negativo anómalo ($< 0$).
    * **En Motor de recomendación:** Analizar la función de asignación de puntuaciones de afinidad o valoración (rango entero válido de 1 a 5 estrellas). Identificar clases válidas (rango entero $[1, 5]$) e inválidas (números $< 1$, números $> 5$ y entradas no numéricas).
    * **En Simulador de físicas 2D:** Analizar la función que valida el coeficiente de restitución o rebote `e` (válido en el intervalo real $[0.0, 1.0]$). Definir clases válidas (rebote entre 0 y 1) e inválidas ($e < 0.0$ o $e > 1.0$).
    * **En Bóveda de contraseñas:** Analizar la regla de longitud de la contraseña maestra (longitud válida entre 8 y 32 caracteres). Definir clases válidas (longitud entre 8 y 32) e inválidas (longitud $< 8$, longitud $> 32$, cadena vacía y `null`).
* **Especificación técnica:** Redactar una tabla con un **mínimo de 6 casos de prueba formales** derivados de los límites inferior y superior, ejecutarlos en el programa y anotar el resultado real obtenido (*Pasado* o *Fallido*).

---

##### Kata 2 · Optativa (cinturón marrón / nivel avanzado). Trazabilidad de frontera en memoria con depurador condicional

* **Contexto técnico.** Comprobar los valores límite por consola puede enmascarar si el programa superó la condición por el camino lógico correcto o por pura casualidad. En esta kata combinamos el depurador con la batería de pruebas.
* **Misión de la kata:**
    1. Abre el archivo Java donde reside la condición lógica evaluada en tu batería de pruebas.
    2. Coloca un **punto de ruptura condicional** en la línea exacta del `if`. Configúralo para que se pause únicamente cuando la variable evaluada coincida con los valores límite críticos (por ejemplo: `longitud == 8 || longitud == 7`).
    3. Lanza el programa en modo depuración (**Debug**, `Shift + F9`) y suministra el caso de prueba del límite conflictivo.
    4. Cuando la ejecución se congele en la línea del `if`, abre la consola de evaluación (**`Alt + F8`**) y evalúa la expresión booleana completa de la condición.
    5. Inspecciona paso a paso (**`F8`**) si el salto condicional entra por la rama verdadera o la rama falsa.
    6. Si detectas un error de frontera (un clásico `<` en vez de `<=`), anota la observación en tu tabla de pruebas y corrige el operador en el código fuente.

---

##### Kata 3 · Optativa (cinturón negro / «hacker AzaharTech»). Automatización de aserciones en el método `main`

* **Contexto de arquitectura.** Para no tener que teclear manualmente los 6 casos de prueba cada vez que se modifica el código, los desarrolladores avanzados crean métodos de prueba sencillos con aserciones básicas en consola antes de dar el salto formal a JUnit en trimestres posteriores.
* **Misión de la kata:**
    1. Crea un método auxiliar estático `ejecutarPruebasFrontera()` en tu clase de pruebas o en una clase auxiliar.
    2. Invoca a tu método de validación pasándole directamente los valores límite (por ejemplo: `validarLongitud(7)`, `validarLongitud(8)`, `validarLongitud(32)`, `validarLongitud(33)`).
    3. Utiliza estructuras `if` para comprobar si el resultado devuelto coincide con el esperado: si coincide, imprime `[PASS] CP-XX superado`; si no, imprime `[FAIL] Discrepancia en límite`.
    4. Comprueba cómo tu software se auto-evalúa en menos de un segundo al pulsar **Run (`Shift + F10`)**.

---

#### Entregables de la sesión

Al finalizar la sesión, debes actualizar el documento del sprint: **`ed/docs/ciclo-error.md`**, añadiendo el **Apartado 2**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                               ENTREGABLES DEL DÍA 15                                   │
├─────────────────────┬──────────────────────────────────────────────────────────────────┤
│ Archivo modificado  │ ed/docs/ciclo-error.md                                           │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Ubicación           │ azahartech/tu-equipo/tus-apellidos-nombre/ed/docs/ciclo-error.md │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Apartado a añadir   │ APARTADO 2. Batería de pruebas de caja negra                     │
│                     │ 1. Tabla de partición en clases de equivalencia (válidas e       │
│                     │    inválidas con valores representativos).                       │
│                     │ 2. Tabla de especificación de casos de prueba (mínimo 6 casos)  │
│                     │    incluyendo ID, entrada, límite evaluado, resultado esperado,  │
│                     │    resultado obtenido y estado final (Pasado / Fallido).         │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Operación en el IDE │ Commit en IntelliJ IDEA (Ctrl + K):                              │
│                     │ Mensaje: docs(qa): añadido apartado 2 con clases de equivalencia │
│                     │          y casos de prueba BVA                                   │
└─────────────────────┴──────────────────────────────────────────────────────────────────┘
```

##### Plantilla a incorporar en `ed/docs/ciclo-error.md`:

```markdown
## Apartado 2. Batería de pruebas de caja negra

### 1. Partición de clases de equivalencia
- **Parámetro analizado:** `[Nombre del parámetro o variable]`
- **Rango o especificación válida:** `[Indicar la regla de negocio]`

| Identificador | Tipo de Clase | Rango o Condición | Dato de Prueba | Resultado Esperado |
| :---: | :---: | :---: | :---: | :---: |
| CE-01 | Válida | [Rango permitido] | [Ej: 25] | Aceptado / Válido |
| CE-02 | Inválida | [Límite inferior no permitido] | [Ej: 8] | Rechazado con error |
| CE-03 | Inválida | [Límite superior no permitido] | [Ej: 105] | Rechazado con error |

### 2. Tabla de casos de prueba con Análisis de Valores Límite (BVA)

| ID Caso | Parámetro Entrada | Tipo de Límite Evaluado | Precondición | Resultado Esperado | Resultado Real | Estado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| CP-01 | [Dato] | Límite inferior - 1 | Sistema iniciado | Rechazado | Rechazado | Pasado |
| CP-02 | [Dato] | Límite inferior (Exacto) | Sistema iniciado | Aceptado | Aceptado | Pasado |
| CP-03 | [Dato] | Límite inferior + 1 | Sistema iniciado | Aceptado | Aceptado | Pasado |
| CP-04 | [Dato] | Límite superior - 1 | Sistema iniciado | Aceptado | Aceptado | Pasado |
| CP-05 | [Dato] | Límite superior (Exacto) | Sistema iniciado | Aceptado | Aceptado | Pasado |
| CP-06 | [Dato] | Límite superior + 1 | Sistema iniciado | Rechazado | Rechazado | Pasado |
```

##### Pasos visuales para el commit en IntelliJ IDEA:
1. Abre el panel de control de versiones con **`Ctrl + K`** (o pestaña vertical izquierda **Commit**).
2. Haz doble clic sobre `ed/docs/ciclo-error.md` para verificar en el visor de diferencias que las dos tablas se han insertado limpiamente tras el Apartado 1.
3. Marca la casilla de verificación de `ed/docs/ciclo-error.md` (y de tu archivo Java si corregiste algún operador off-by-one).
4. Escribe el mensaje formal:
   `docs(qa): añadido apartado 2 con clases de equivalencia y casos de prueba BVA`
5. Haz clic en el botón azul **Commit**.

---

### Día 16 - 1 sesión

---

#### Sesión única. Taller práctico: medición del cubrimiento de código (*Code Coverage*) y procedimiento de ejecución de pruebas

---

#### 1. Caso guía en AzaharTech

Miércoles 4 de noviembre de 2026. En el ecuador de la segunda semana del **Sprint 3**, el equipo de desarrollo se concentra en la sala de operaciones de **AzaharTech**.

**Pau Ferrer** tiene en sus pantallas las baterías de casos de prueba diseñadas el lunes mediante partición de equivalencia y valores límite. **Alba Torres** se sitúa junto a la mesa del desarrollador junior y revisa el documento de pruebas del validador de accesos del **IES El Caminàs**:

> *«Has hecho un trabajo excelente acorralando los límites de entrada: el código ya no revienta con edades de 11 o 100 años ni con contraseñas vacías. Sin embargo, tengo una duda sobre aseguramiento de la calidad (QA):*
>
> *¿Estás seguro de que los casos de prueba han obligado a la JVM a pasar por todas las ramas de vuestro código? ¿O tienes bloques de sentencias enteras dentro de los `else` o de los `catch` que jamás se han ejecutado en la realidad?*
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
│ 1. Cubrimiento de   │ Porcentaje de líneas de código  │ BÁSICO. Garantiza que la JVM   │
│    sentencias/líneas│ que han sido ejecutadas al      │ ha pisado la instrucción, pero │
│    (line coverage)  │ menos una vez.                  │ ignora si se probaron todos los│
│                     │                                 │ caminos de una decisión.       │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 2. Cubrimiento de   │ Porcentaje de ramas evaluadas en│ MEDIO-ALTO. En un `if (A)`,    │
│    ramas/decisiones │ estructuras condicionales       │ comprueba que se han probado   │
│    (branch coverage)│ (`if`, `switch`, bucles).       │ tanto el camino TRUE como el   │
│                     │                                 │ camino FALSE.                  │
├─────────────────────┼─────────────────────────────────┼────────────────────────────────┤
│ 3. Cubrimiento de   │ Porcentaje de todas las         │ EXHAUSTIVO. En condiciones     │
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

#### 4. Laboratorio: cierre de ángulos muertos

Durante el resto de la sesión, los estudiantes auditan su propio software aplicando la técnica de **pruebas dirigidas por cobertura (*Coverage-Driven Testing*)**:

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

* **En Aventura conversacional:** Identificar si la rama que gestiona la derrota del jugador (vida <= 0) o la opción por defecto de un menú (default del switch) está en rojo. Probar introduciendo por consola una opción no contemplada (por ejemplo, teclear un 9) para forzar la ejecución de la rama de error.  
* **En Motor de recomendación:** Comprobar si la condición que protege contra divisiones por cero (totalValoraciones == 0) está desatendida en rojo. Ejecutar introduciendo un 0 en el contador de valoraciones para verificar que se muestra el mensaje de bienvenida a usuarios nuevos.  
* **En Simulador de físicas 2D:** Comprobar si la rama que detiene el movimiento cuando la velocidad cae por debajo del umbral de fricción (velocidad < 0.05) está en rojo. Probar introduciendo una velocidad inicial nula (0.0) para iluminar en verde la parada inmediata.  
* **En Bóveda de contraseñas:** Comprobar si la rama que rechaza cadenas demasiado cortas (password.length() < 8) quedó sin ejecutar. Introducir una contraseña de 3 caracteres (ej. "abc") para comprobar que el bloque de advertencia se ejecuta y se tiñe de verde.  

#### Entregables de la sesión

Al finalizar el taller, debes actualizar **`ed/docs/ciclo-error.md`** incorporando el **Apartado 3**:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                               ENTREGABLES DEL DÍA 16                                   │
├─────────────────────┬──────────────────────────────────────────────────────────────────┤
│ Archivo modificado  │ ed/docs/ciclo-error.md                                           │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Ubicación           │ azahartech/tu-equipo/tus-apellidos-nombre/ed/docs/ciclo-error.md │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Apartado a añadir   │ APARTADO 3. Análisis de cubrimiento de código (Code Coverage)    │
│                     │ 1. Métricas iniciales obtenidas con IntelliJ Coverage (% líneas).│
│                     │ 2. Identificación del ángulo muerto (bloque en rojo detectado).  │
│                     │ 3. Caso de prueba añadido para forzar la ejecución de la rama.   │
│                     │ 4. Métrica final alcanzada (>= 80% de líneas cubiertas).         │
├─────────────────────┼──────────────────────────────────────────────────────────────────┤
│ Operación en el IDE │ Commit en IntelliJ IDEA (Ctrl + K):                              │
│                     │ Mensaje: docs(qa): añadido apartado 3 con auditoria de cobertura │
│                     │          y cierre de ramas muertas                               │
└─────────────────────┴──────────────────────────────────────────────────────────────────┘
```

##### Plantilla a incorporar en `ed/docs/ciclo-error.md`:

```markdown
## Apartado 3. Análisis de cubrimiento de código (Code Coverage)

### 1. Métricas de cobertura iniciales
- **Herramienta empleada:** Motor de cobertura integrado de IntelliJ IDEA.
- **Clase / Módulo auditado:** `[Nombre de la clase Java]`
- **Cubrimiento de líneas inicial:** `[Ej: 65%]`
- **Cubrimiento de ramas inicial:** `[Ej: 50%]`

### 2. Detección de ángulos muertos (bloques no ejecutados)
- **Líneas o bloques en rojo identificados:** Líneas `[Nº a Nº]` correspondientes a `[describir el bloque, ej: rama else de bloqueo por aforo saturado o default del switch]`.
- **Causa de la falta de cobertura:** Ninguno de los casos de prueba iniciales suministraba una entrada que cumpliera simultáneamente la condición de fallo.

### 3. Acción correctora y cierre del ángulo muerto
- **Nuevo caso de prueba diseñado:** `CP-COV-01` con entrada `[especificar datos concretos]`.
- **Comportamiento obtenido:** La JVM entra en el bloque alternativo, ejecuta las instrucciones de aviso y el margen del editor se ilumina en color verde.

### 4. Métrica de cubrimiento consolidada
- **Cubrimiento final de líneas:** `[Ej: 88%]` (Objetivo mínimo corporativo superado: >= 80%).
- **Cubrimiento final de ramas:** `[Ej: 83%]`.
```

##### Pasos visuales para el commit en IntelliJ IDEA:
1. Pulsa **`Ctrl + K`** para abrir la herramienta visual de confirmación.
2. En la lista de cambios, haz doble clic sobre `ed/docs/ciclo-error.md` y revisa que el Apartado 3 contiene los porcentajes reales auditados.
3. Marca la casilla de `ed/docs/ciclo-error.md`.
4. Redacta el mensaje formal estructurado:
   `docs(qa): añadido apartado 3 con auditoria de cobertura y cierre de ramas muertas`
5. Haz clic en el botón azul **Commit**.

Con la cobertura objetiva verificada y documentada, el equipo dispone de un código contrastado.

## Semana 9. Complejidad ciclomática y regresión

---

### Día 17 - 2 sesiones

---

#### Sesión 1. Teoría. Desmontando el laberinto: grafos de flujo, métrica de McCabe y depuración no invasiva

---

#### 1. Caso guía en AzaharTech

Lunes 9 de noviembre de 2026. Arranca la tercera y última semana del **Sprint 3** en la sede de **AzaharTech**.

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

> **Dinámica del dojo de entrenamiento:**
>
> * La **Kata 1 es de realización obligatoria** para todo el grupo: constituye el núcleo técnico imprescindible y la base del entregable de la sesión.
> * Las **Katas 2 y 3 son optativas y de ampliación**: están diseñadas para que los estudiantes que resuelvan rápidamente la primera práctica no se queden inactivos, permitiéndoles subir de nivel y adquirir competencias avanzadas en depuración profesional.

---

##### Kata 1 (Cinturón blanco / Nivel base). Grafo de flujo y cálculo de $V(G)$ de tu algoritmo central

* **Objetivo.** Seleccionar el método más complejo desarrollado hasta la fecha en tu proyecto propio (aquel que combine bucles y estructuras condicionales), dibujar su grafo de flujo de control elemental y calcular formalmente su Complejidad Ciclomática $V(G)$.
* **Aplicación según tu proyecto de la bolsa:**
    * En **Aventura conversacional.** Analizar el método del bucle de combate o de resolución de acertijos (evaluación de vida del monstruo, tiradas de dados, estado de aturdimiento y huida).
    * En **Motor de recomendación.** Analizar el algoritmo de ordenación o filtrado condicional por umbral de similitud (bucle que compara vectores de usuarios con condiciones múltiples).
    * En **Simulador de físicas 2D.** Analizar el algoritmo de detección y respuesta de colisiones contra los cuatro bordes de la ventana y otros obstáculos rígidos.
    * En **Bóveda de contraseñas.** Analizar la función de cálculo del nivel de entropía o fortaleza de contraseña (bucle que recorre la cadena comprobando caracteres contiguos repetidos, símbolos y números).
* **Entregable:** Registrar en `pruebas.md` el fragmento de código, el desglose de los puntos de decisión identificados y el valor resultante de $V (G)$.

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

Viernes 13 de noviembre de 2026. Son las cuatro de la tarde en las oficinas de **AzaharTech**. La jornada representa un hito fundamental del primer trimestre: hoy concluye formalmente el **Sprint 3: «El ciclo del error: pruebas y depuración»**.

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
Cada pareja consolida el informe de calidad en el archivo `pruebas.md`:

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


