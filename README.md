# Practica-2

# Practica 2A

## CODIGO:

```
#include <Arduino.h>

struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
};

// Asignar el pulador en el pin 18
Button button1 = {18, 0, false};

void IRAM_ATTR isr() {
  button1.numberKeyPresses += 1;
  button1.pressed = true;
}

void setup() 
{
  Serial.begin(115200);
  pinMode(button1.PIN, INPUT_PULLUP);
  attachInterrupt(button1.PIN, isr, FALLING);
}

void loop() 
{ 
  // Aqui detectará cuando pulsamos el boton
  if (button1.pressed) {
    Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
    button1.pressed = false;
  }

// función de apagado
static uint32_t lastMillis = 0;

  if (millis() - lastMillis > 60000) {
    lastMillis = millis();
    detachInterrupt(button1.PIN);
    Serial.println("Interrupt Detached!");
  }

}

```

## FUNCIONAMIENTO

Crear la estuctura Button con tres variables: numero de pin, número de pulsaciones y estado de pulsación.
```
struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
};
```

Tras haber asigando un pin de la ESP32 al boton en este caso el 18,
```
Button button1 = {18, 0, false};
```
en el loop tendremos que tener una función que en cuanto detecte que se a pulsado nos saque por pantalla el numero de veces total que se ha pulsado.
```
if (button1.pressed) {
    Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
    button1.pressed = false;
```
En cuanto el boton lleve mas de 60000ms (1min) sin ser pulsado se "apagará", es decir, se imprimirá por pantalla "Interrupt Detached!" y se deberá hacer un reset para volver a poner en funcionamiento el programa.
```
static uint32_t lastMillis = 0;

  if (millis() - lastMillis > 60000) {
    lastMillis = millis();
    detachInterrupt(button1.PIN);
    Serial.println("Interrupt Detached!");
  }

}
```
