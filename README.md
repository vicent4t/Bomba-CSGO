
Arduino

Objetivo: Hacer un minijuego de desactivación de bomba con Arduino, usando módulos de Código Morse y “caja fuerte” (safe cracker) contrarreloj.

Equipo: Pau Albiol Duato y Vicent Benabent

Roles: Pau Albiol Duato (Montaje en físico / Cableado y colocación de componentes / Programación y lógica del minijuego)
Vicent Benabent (Montaje en Tinkercad)

Plan de Sprints:

Sprint 1: Definir las mecánicas del juego (tiempo, presión, vidas y módulos), hacer el boceto del circuito en Tinkercad y subir las primeras versiones del código (temporizador y presión).

Sprint 2: Prototipo más avanzado (circuito completo en Tinkercad, LCD mostrando datos, lectura de potenciómetro, botón y teclado, implementación de Morse y safe cracker) y lista definitiva de sensores/actuadores.

LiveDemo: Funcionamiento del prototipo (en físico), explicar las reglas del juego (tiempo límite, gestión de presión y errores de código) y responder a las preguntas.


Sprint 1

Objetivo del sprint:
Dejar definido el diseño del minijuego y tener un primer prototipo funcional en Tinkercad con tiempo y presión implementados.



* Diseño de mecánicas (Pau & Vicent)

  * Concretar tiempo total de partida, número de vidas y condición de derrota.
  * Definir cómo funciona la presión (valor inicial, velocidad a la que sube, cuánto baja con el botón).
  * Decidir cuántos módulos habrá en total (Morse + safe cracker) y cómo se ganan/completan.

* Boceto del circuito en Tinkercad (Vicent)

  * Colocar Arduino, LCD, potenciómetro, botón, teclado 4x4 y zumbador.
  * Hacer el cableado básico suficiente para probar temporizador y presión.
  * Guardar el proyecto y compartir el enlace con Pau.

  Primera versión de código (Pau)

  * Configurar librerías (LCD, Keypad si hace falta ya).
  * Implementar el temporizador (minutos/segundos) y mostrarlo en la LCD.
  * Implementar la variable de presión que sube sola con el tiempo y baja al mantener pulsado el botón.
  * Mostrar en pantalla al menos: tiempo restante y presión actual.

  Revisión rápida (Pau & Vicent)

  * Probar el circuito en Tinkercad y comprobar que tiempo y presión funcionan como se ha diseñado.
  * Anotar errores o mejoras para el Sprint 2.

Entregables Sprint 1

* Enlace al Tinkercad con el boceto del circuito.
* Archivo .ino con la primera versión del código comentado.
* Breve esquema/diagrama con las mecánicas básicas definidas (tiempo, presión, vidas y módulos).



https://www.tinkercad.com/things/edwomiDdRjm/editel?returnTo=%2Fdashboard%2Fdesigns%2Fcircuits&sharecode=_J3_5_-QxDp3ceBTR7kbHDX_drTWIpASMO7VuU-rxPc


<img width="846" height="710" alt="aaa" src="https://github.com/user-attachments/assets/af0a0123-dcdc-4f15-92e8-19857aa54b25" />

Sprint 1

En este primer sprint hemos definido y plasmado las mecánicas básicas del minijuego de desactivación de bomba y hemos creado el primer prototipo funcional en Tinkercad.

A nivel de diseño, se ha establecido un tiempo límite de partida, un número de vidas máximo, un sistema de presión que aumenta de forma progresiva y una serie de módulos (Código Morse y “caja fuerte”) que el jugador debe completar para ganar. También se han fijado las condiciones de derrota: que se agote el tiempo, que la presión llegue al valor máximo o que el jugador falle un número determinado de intentos.

En Tinkercad se ha montado el circuito inicial con Arduino Nano, pantalla LCD, pulsador, potenciómetro, teclado 4x4 y zumbador, organizando el cableado de forma similar a cómo se montará luego en físico. Este prototipo nos permite comprobar la lectura de los componentes y la visualización de la información en pantalla.

En cuanto a la programación, se ha desarrollado la primera versión del código en Arduino. Esta versión incluye:

 Un temporizador en minutos y segundos que se muestra en la pantalla LCD.
 Un sistema de presión que parte de un valor inicial, aumenta de forma periódica y puede reducirse manteniendo pulsado el botón.
 Variables de control para las vidas y el número total de módulos a completar, que más adelante se conectarán con el módulo de Código Morse y el “safe cracker”.

Con este sprint dejamos listo un prototipo jugable básico sobre el que seguiremos iterando en el Sprint 2, donde se implementarán por completo los módulos de juego y el resto de la lógica.


