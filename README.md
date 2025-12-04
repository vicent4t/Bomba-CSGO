
## Arduino

### Objetivo

Hacer un minijuego de desactivación de bomba con Arduino, usando módulos de Código Morse y “caja fuerte” (safe cracker) contrarreloj.

### Equipo

Pau Albiol Duato y Vicent Benavent

### Roles

Pau Albiol Duato (Programación y lógica del minijuego) Vicent Benavent (Montaje en Tinkercad /Montaje en físico / Cableado y colocación de componentes)

## Plan de Sprints:

### Sprint 1:

Definir las mecánicas del juego (tiempo, presión, vidas y módulos), hacer el boceto del circuito en Tinkercad y subir las primeras versiones del código (temporizador y presión).

### Sprint 2:

Prototipo más avanzado (circuito completo en Tinkercad, LCD mostrando datos, lectura de potenciómetro, botón y teclado, implementación de Morse y safe cracker) y lista definitiva de sensores/actuadores.

### LiveDemo:

Funcionamiento del prototipo (en físico), explicar las reglas del juego (tiempo límite, gestión de presión y errores de código) y responder a las preguntas.

## Sprint 1


### Objetivo del sprint:
Dejar definido el diseño del minijuego y tener un primer prototipo funcional en Tinkercad con tiempo y presión implementados.


## Diseño de mecánicas (Pau & Vicent)

  * Concretar tiempo total de partida, número de vidas y condición de derrota.
  * Definir cómo funciona la presión (valor inicial, velocidad a la que sube, cuánto baja con el botón).
  * Decidir cuántos módulos habrá en total (Morse + safe cracker) y cómo se ganan/completan.

## Boceto del circuito en Tinkercad (Vicent)

  * Colocar Arduino, LCD, potenciómetro, botón, teclado 4x4 y zumbador.
  * Hacer el cableado básico suficiente para probar temporizador y presión.
  * Guardar el proyecto y compartir el enlace con Pau.

## Primera versión de código (Pau)

  * Configurar librerías (LCD, Keypad si hace falta ya).
  * Implementar el temporizador (minutos/segundos) y mostrarlo en la LCD.
  * Implementar la variable de presión que sube sola con el tiempo y baja al mantener pulsado el botón.
  * Mostrar en pantalla al menos: tiempo restante y presión actual.

## Revisión rápida (Pau & Vicent)

  * Probar el circuito en Tinkercad y comprobar que tiempo y presión funcionan como se ha diseñado.
  * Anotar errores o mejoras para el Sprint 2.

## Entregables Sprint 1

* Enlace al Tinkercad con el boceto del circuito.
* Archivo .ino con la primera versión del código comentado.
* Breve esquema/diagrama con las mecánicas básicas definidas (tiempo, presión, vidas y módulos).

<img width="846" height="710" alt="aaa" src="https://github.com/user-attachments/assets/af0a0123-dcdc-4f15-92e8-19857aa54b25" />

# Sprint 1

En este primer sprint hemos definido y plasmado las mecánicas básicas del minijuego de desactivación de bomba y hemos creado el primer prototipo funcional en Tinkercad.

A nivel de diseño, se ha establecido un tiempo límite de partida, un número de vidas máximo, un sistema de presión que aumenta de forma progresiva y una serie de módulos (Código Morse y “caja fuerte”) que el jugador debe completar para ganar. También se han fijado las condiciones de derrota: que se agote el tiempo, que la presión llegue al valor máximo o que el jugador falle un número determinado de intentos.

En Tinkercad se ha montado el circuito inicial con Arduino Nano, pantalla LCD, pulsador, potenciómetro, teclado 4x4 y zumbador, organizando el cableado de forma similar a cómo se montará luego en físico. Este prototipo nos permite comprobar la lectura de los componentes y la visualización de la información en pantalla.

En cuanto a la programación, se ha desarrollado la primera versión del código en Arduino. Esta versión incluye:

 - Un temporizador en minutos y segundos que se muestra en la pantalla LCD.
 - Un sistema de presión que parte de un valor inicial, aumenta de forma periódica y puede reducirse manteniendo pulsado el botón.
 - Variables de control para las vidas y el número total de módulos a completar, que más adelante se conectarán con el módulo de Código Morse y el “safe cracker”.

Con este sprint dejamos listo un prototipo jugable básico sobre el que seguiremos iterando en el Sprint 2, donde se implementarán por completo los módulos de juego y el resto de la lógica.

## Codigo:

```cpp
#include <Keypad.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x20, 16, 2);

// Pines
const int BUTTON_PIN = A0;
const int POT_PIN = A1;
const int LED_PIN = 10;
const int SPEAKER_PIN = 13;

// Juego
int minutos = 1;
int segundos = 59;
int vidas = 3;
int tareas = 11;
int presion = 100;

// Código secreto
char codigo[6];
char entrada[6];
int idx = 0;

// Keypad
char teclas[4][4] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte filas[4] = {9,8,7,6};
byte cols[4]  = {5,4,3,2};

Keypad keypad = Keypad(makeKeymap(teclas), filas, cols, 4, 4);

unsigned long t1, tLatido;

// ---- FUNCIONES ----

void generarCodigo() {
  char chars[] = "0123456789ABCD";
  for (int i = 0; i < 5; i++) {
    codigo[i] = chars[random(14)];
  }
  codigo[5] = '\0';
}

void pantalla() {
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print(minutos); lcd.print(":");
  if (segundos < 10) lcd.print("0");
  lcd.print(segundos);

  lcd.setCursor(9,0);
  lcd.print("V:");
  lcd.print(vidas);

  lcd.setCursor(0,1);
  lcd.print("P:");
  lcd.print(presion);

  lcd.setCursor(9,1);
  lcd.print("T:");
  lcd.print(tareas);
}

void reiniciar() {
  minutos = 1;
  segundos = 59;
  vidas = 3;
  tareas = 11;
  presion = 100;
  idx = 0;
  generarCodigo();
}

// ---- SETUP ----
void setup() {
  lcd.init();
  lcd.backlight();

  pinMode(BUTTON_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
  pinMode(SPEAKER_PIN, OUTPUT);

  randomSeed(analogRead(A3));
  reiniciar();

  lcd.clear();
  lcd.print("Presiona Boton");
  while (digitalRead(BUTTON_PIN) == LOW);
  lcd.clear();
}

// ---- LOOP ----
void loop() {

  // Latido
  if (millis() - tLatido > 500) {
    digitalWrite(SPEAKER_PIN, !digitalRead(SPEAKER_PIN));
    tLatido = millis();
  }

  // Tiempo
  if (millis() - t1 > 1000) {
    t1 = millis();
    if (segundos > 0) segundos--;
    else if (minutos > 0) { minutos--; segundos = 59; }
    else vidas = 0;
  }

  // Presión
  if (digitalRead(BUTTON_PIN) == HIGH) presion--;
  else presion++;

  // Potenciómetro
  int pot = analogRead(POT_PIN);
  if (pot > 480 && pot < 540) {
    digitalWrite(LED_PIN, HIGH);
    tareas--;
    delay(500);
  } else {
    digitalWrite(LED_PIN, LOW);
  }

  // Keypad
  char k = keypad.getKey();
  if (k) {
    entrada[idx++] = k;
    if (idx == 5) {
      entrada[5] = '\0';
      if (strcmp(entrada, codigo) == 0) tareas--;
      else vidas--;
      idx = 0;
    }
  }

  // Dibujar pantalla
  pantalla();

  // Pérdida
  if (vidas <= 0 || presion <= 0 || presion >= 1000) {
    lcd.clear();
    lcd.print("BOOM!");
    delay(1500);
    reiniciar();
  }

  // Victoria
  if (tareas <= 0) {
    lcd.clear();
    lcd.print("Desactivada!");
    delay(1500);
    reiniciar();
  }

  delay(20);
}
```

## Sprint 2

### Objetivo del sprint

Completar el prototipo del minijuego de desactivación de bomba, con el circuito en Tinkercad más avanzado y el código principal (Morse + safe cracker) ya implementado y funcional a nivel básico.

## Tareas

**Pau Albiol Duato**

Ampliar y refactorizar el código de Arduino para integrar las mecánicas principales:

Módulo de “caja fuerte” (safe cracker) usando el potenciómetro.

Conectar estos módulos con el sistema de tiempo, presión, vidas y tareas totales.

Probar el flujo completo de partida (aciertos/fallos, condiciones de victoria y derrota) y ajustar parámetros básicos de dificultad.

**Vicent Benavent**

Completar el circuito en Tinkercad con todos los componentes definitivos (Arduino, LCD, potenciómetro, botón, teclado 4x4, zumbador, etc.).

Revisar y mejorar el cableado para que sea coherente con el futuro montaje físico.

Subir capturas del circuito y el archivo del proyecto al repositorio.

Pau Albiol Duato & Vicent Benabent

Elaborar y subir al repositorio la lista definitiva de sensores y componentes (hardware.md).

Actualizar el README con la evolución del proyecto, problemas encontrados y cómo se han resuelto, y las tareas pendientes para la LiveDemo.

<img width="1173" height="743" alt="image" src="https://github.com/user-attachments/assets/c680c8d9-b291-43e6-853a-014b8bd691e9" />

# Resumen Sprint 2

En este segundo sprint hemos pasado de un prototipo básico a un circuito y un código mucho más completos y cercanos a la versión final del minijuego.
El circuito en Tinkercad se ha completado con todos los elementos definitivos (LCD, teclado 4x4, potenciómetro, botón, zumbador y LEDs), y se ha revisado el cableado pensando en el montaje físico.

A nivel de software, el código se ha ampliado para integrar las mecánicas principales del juego: el módulo de Código Morse usando el zumbador y el teclado, y el módulo de “caja fuerte” utilizando el potenciómetro. Estos módulos ya afectan al sistema de tiempo, presión, vidas y tareas, permitiendo jugar partidas completas con condiciones de victoria y derrota.

El README y el archivo hardware.md se han actualizado con la evolución del proyecto, la lista definitiva de componentes y los problemas encontrados junto con sus soluciones, dejando preparado el trabajo para la LiveDemo.

## Entregables del Sprint 2

Circuito en Tinkercad

## Prototipo completo del minijuego de desactivación de bomba.

**Incluye:**

 - Placa Arduino (modelo UNO en el prototipo de Tinkercad).

 - Pantalla LCD 16x2.

 - Teclado matricial 4x4.

 - Potenciómetro (control de “caja fuerte”).

 - Pulsador (control de presión).

 - Zumbador piezoeléctrico (Código Morse / avisos sonoros).

 - Varios LEDs con sus resistencias en protoboard.

Lista de sensores y componentes (hardware.md)

**Sensores / actuadores:**

 - (1×) Pantalla LCD 16x2.

 - (1×) Teclado matricial 4x4.

 - (1×) Potenciómetro (≈10 kΩ).

 - (1×) Pulsador.

 - (1×) Zumbador piezoeléctrico.

 - (4×) LED rojos (indicadores de estado / feedback).

**Otros componentes:**

 - (4×) Resistencias para LEDs (≈220 Ω–330 Ω).

 - (1×) Protoboard.

 - Cables de conexión macho-macho/macho-hembra.

 - Placa Arduino (UNO en Tinkercad; equivalente en el montaje físico).

**Esquema de conexiones:**

Basado en el circuito de Tinkercad mostrado en img/circuito_sprint2.png.

El LCD, el teclado 4x4, el potenciómetro, el pulsador, el zumbador y los LEDs están conectados a pines digitales/analógicos de la placa según dicho esquema.

Lista de tareas pendientes para la LiveDemo:

Montaje físico definitivo.

Ajuste de dificultad (velocidad de presión, tiempo disponible, margen de error en safe cracker).

Pruebas finales de estabilidad.

## Code:

```cpp
#include <Keypad.h>
#include <LiquidCrystal_I2C.h>

// LCD igual que en el código original que funciona
LiquidCrystal_I2C lcd_1(0x27, 16, 2);

// Pines
const int BUTTON_PIN = A0;
const int POT_PIN    = A1;
const int LED_PIN    = 10;
const int SPEAKER_PIN = 13;

// Juego
int minutos = 1;
int segundos = 59;
int vidas = 3;
int tareas = 11;
int presion = 100;

// Código secreto
char codigo[6];
char entrada[6];
int idx = 0;

// Keypad
char teclas[4][4] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte filas[4] = {9,8,7,6};
byte cols[4]  = {5,4,3,2};

Keypad keypad = Keypad(makeKeymap(teclas), filas, cols, 4, 4);

unsigned long t1, tLatido;

// ---- FUNCIONES ----

void generarCodigo() {
  const char chars[] = "0123456789ABCD";
  for (int i = 0; i < 5; i++) {
    codigo[i] = chars[random(14)];
  }
  codigo[5] = '\0';
}

void pantalla() {
  lcd_1.clear();

  lcd_1.setCursor(0,0);
  lcd_1.print(minutos);
  lcd_1.print(":");
  if (segundos < 10) lcd_1.print("0");
  lcd_1.print(segundos);

  lcd_1.setCursor(9,0);
  lcd_1.print("V:");
  lcd_1.print(vidas);

  lcd_1.setCursor(0,1);
  lcd_1.print("P:");
  lcd_1.print(presion);

  lcd_1.setCursor(9,1);
  lcd_1.print("T:");
  lcd_1.print(tareas);
}

void reiniciar() {
  minutos = 1;
  segundos = 59;
  vidas = 3;
  tareas = 11;
  presion = 100;
  idx = 0;
  generarCodigo();
}

// ---- SETUP ----
void setup() {
  // MISMA FORMA QUE EN TU CÓDIGO ORIGINAL
  lcd_1.backlight();
  lcd_1.begin(16, 2);

  pinMode(BUTTON_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
  pinMode(SPEAKER_PIN, OUTPUT);

  randomSeed(analogRead(A3));
  reiniciar();

  lcd_1.clear();
  lcd_1.setCursor(0,0);
  lcd_1.print("Presiona Boton");

  // Esperar a que se pulse el botón para empezar
  while (digitalRead(BUTTON_PIN) == LOW) {
    // Esperando…
  }

  lcd_1.clear();
}

// ---- LOOP ----
void loop() {

  // Latido de la bomba en el altavoz
  if (millis() - tLatido > 500) {
    digitalWrite(SPEAKER_PIN, !digitalRead(SPEAKER_PIN));
    tLatido = millis();
  }

  // Tiempo
  if (millis() - t1 > 1000) {
    t1 = millis();
    if (segundos > 0) {
      segundos--;
    } else if (minutos > 0) {
      minutos--;
      segundos = 59;
    } else {
      vidas = 0;
    }
  }

  // Presión (sube sola, baja con el botón)
  if (digitalRead(BUTTON_PIN) == HIGH) {
    presion--;
  } else {
    presion++;
  }

  // Potenciómetro → “safe cracker”
  int pot = analogRead(POT_PIN);
  if (pot > 480 && pot < 540) {
    digitalWrite(LED_PIN, HIGH);
    tareas--;
    delay(500);
  } else {
    digitalWrite(LED_PIN, LOW);
  }

  // Keypad → código secreto
  char k = keypad.getKey();
  if (k) {
    entrada[idx++] = k;
    if (idx == 5) {
      entrada[5] = '\0';
      if (strcmp(entrada, codigo) == 0) {
        tareas--;
      } else {
        vidas--;
      }
      idx = 0;
    }
  }

  // Dibujar pantalla
  pantalla();

  // Pérdida
  if (vidas <= 0 || presion <= 0 || presion >= 1000) {
    lcd_1.clear();
    lcd_1.setCursor(0,0);
    lcd_1.print("BOOM!");
    delay(1500);
    reiniciar();
  }

  // Victoria
  if (tareas <= 0) {
    lcd_1.clear();
    lcd_1.setCursor(0,0);
    lcd_1.print("Desactivada!");
    delay(1500);
    reiniciar();
  }

  delay(20);
}
```
