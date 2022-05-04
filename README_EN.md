# GyverRelay
GyverRelay - classic relay controller library for Arduino
- Feedback on the rate of change of value
- Adjustment of hysteresis, feedback gain, regulation direction
- Returns the result by the built-in timer or in manual mode

### Compatibility
Compatible with all Arduino platforms (using Arduino functions)

### Documentation
The library has [extended documentation](https://alexgyver.ru/GyverRelay/)

## Content
- [Install](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found by the name **GyverRelay** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download library](https://github.com/GyverLibs/GyverRelay/archive/refs/heads/main.zip) .zip archive for manual installation:
    - Unzip and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unzip and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/*
    - (Arduino IDE) automatic installation from .zip: *Sketch/Include library/Add .ZIP library…* and specify the downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0 %B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)

<a id="init"></a>
## Initialization
```cpp
GyverRelay regulator;
GyverRelay regulator(mode); // mode: NORMAL / REVERSE
```

<a id="usage"></a>
## Usage
```cpp
// calculation returns the state for the control device (relay, transistor) (1 on, 0 off)
boolean compute(float dt = 0); // instant calculation. Takes dt in seconds for OS mode
boolean getResult(); // instant calculation. Built-in timer for OS mode
boolean getResultTimer(); // calculation by built-in timer

void setDirection(boolean dir); // regulation direction (NORMAL, REVERSE)

float input = 0; // signal from the sensor (for example, the temperature that we regulate)
float setpoint = 0; // set value that the controller must maintain (temperature)
bool output = 0; // controller output (0 or 1)

float hysteresis = 0; // hysteresis window width
float k = 0; // speed gain (default 0)
int16_t dT = 1000; // iteration time, ms (default second)
```

<a id="example"></a>
## Example
See **examples** for other examples!
```cpp
/*
   An example of the operation of a relay controller in automatic mode using the built-in timer
   Let's imagine that on pin 3 we have a heating coil connected via a relay
   And there is some kind of abstract temperature sensor, which is affected by the spiral
*/
#include "GyverRelay.h"

// setting, hysteresis, regulation direction
GyverRelay regulator(REVERSE);
// either GyverRelay regulator(); without direction indication (will be REVERSE)

void setup() {
  pinMode(3, OUTPUT); // relay pin
  regulator.setpoint = 40; // set (set to 40 degrees)
  regulator.hysteresis = 5; // hysteresis width
  regulator.k = 0.5; // feedback coefficient (selected according tocranberry fact)
  //regulator.dT = 500; // set iteration time for getResultTimer
}

// variant with delay
void loop() {
  inttemp; // for example, read the temperature from the sensor
  regulator.input = temp; // tell the controller the current temperature

  // getResult returns the value for the control device
  digitalWrite(3, regulator.getResult()); // send to the relay (OS works on its own timer)
  delay(100);
}

/*
  // option with built-in timer
  void loop() {
  inttemp; // for example, read the temperature from the sensor
  regulator.input = temp; // tell the controller the current temperature

  // getResult returns the value for the control device
  digitalWrite(3, regulator.getResultTimer()); // send to relay
  
  // you can also get the value from the controller output
  // controller
  }
*/

/*
// variant with own timer
void loop() {
  static uint32_t myTimer = 0;
  if (millis() - myTimer > 2000) { // custom timer for millis, 2 seconds
    myTimer = millis();
    inttemp; // for example, read the temperature from the sensor
    regulator.input = temp; // tell the controller the current temperature

    // getResult returns the value for the control device
    digitalWrite(3, regulator.compute(2)); // send to the relay. We transfer time manually, we have 2 seconds
  }
}
*/
```

<a id="versions"></a>
## Versions
- v2.1 - fixed getResultTimer
- v2.2 - improved and simplified algorithm

<a id="feedback"></a>
## Bugs and feedback
When you find bugs, create an **Issue**, or better, immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for revision and your **Pull Request**'s!