This is an automatic translation, may be incorrect in some places. See sources and examples!

# Gyverrelay
Gyverrelay - a library of a classic relay regulator for Arduino
- Feedback on the speed of change in size
- Setting a hysteresis, reinforcement coefficient of OS, direction of regulation
- returns the result by built -in timer or manually

## compatibility
Compatible with all arduino platforms (used arduino functions)

### Documentation
There is [extended documentation] to the library (https://alexgyver.ru/gyverrelay/)

## Content
- [installation] (# Install)
- [initialization] (#init)
- [use] (#usage)
- [Example] (# Example)
- [versions] (#varsions)
- [bugs and feedback] (#fedback)

<a id="install"> </a>
## Installation
- The library can be found by the name ** gyverrelay ** and installed through the library manager in:
    - Arduino ide
    - Arduino ide v2
    - Platformio
- [download the library] (https://github.com/gyverlibs/gyverrelay/archive/refs/heads/main.zip) .Zip archive for manual installation:
    - unpack and put in * C: \ Program Files (X86) \ Arduino \ Libraries * (Windows X64)
    - unpack and put in * C: \ Program Files \ Arduino \ Libraries * (Windows X32)
    - unpack and put in *documents/arduino/libraries/ *
    - (Arduino id) Automatic installation from. Zip: * sketch/connect the library/add .Zip library ... * and specify downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%BD%D0%BE%BE%BE%BED0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: errors and bugs are corrected in the new versions, as well as optimization and new features are added
- through the IDE library manager: find the library how to install and click "update"
- Manually: ** remove the folder with the old version **, and then put a new one in its place.“Replacement” cannot be done: sometimes in new versions, files that remain when replacing are deleted and can lead to errors!


<a id="init"> </a>
## initialization
`` `CPP
Gyverrelay Regulator;
Gyverrelay Regulator (Mode);// Mode: Normal / Reverse
`` `

<a id="usage"> </a>
## Usage
`` `CPP
// Calculation returns the condition for the control device (relay, transistor) (1 vkl, 0 off)
Boolean Compute (Float DT = 0);// instant calculation.Accepts DT in seconds for the regime with OS
Boolean Getressult ();// instant calculation.Built -in timer for the OS mode
Boolean Getressulttimer ();// Calculation by built -in timer

VOID Setdirection (Boolean Dir);// Direction of regulation (Normal, Reverse)

Float Input = 0;// signal from the sensor (for example, the temperature that we adjust)
Float setpoint = 0;// a given value that the regulator should support (temperature)
Bool output = 0;// regulator output (0 or 1)

Float Hysteresis = 0;// Hysteresis window width
Float k = 0;// Rinse of speed reinforcement (in silence 0)
int16_t dt = 1000;// Iteration time, MS (by the silence of seconds)
`` `

<a id="EXAMPLE"> </a>
## Example
The rest of the examples look at ** Examples **!
`` `CPP
/*
   For exampleCranberries R operation of the relay regulator in automatic mode according to the built -in timer
   Let's imagine that on 3 pin we have a heating spiral connected via relay
   And there is some kind of abstract temperature sensor on which the spiral affects
*/
#incLude "gyverrelay.h"

// Installation, hysteresis, direction of regulation
Gyverrelay Regulator (Reverse);
// or gyverrelay regulator ();Without indicating the direction (will be reverse)

VOID setup () {
  Pinmode (3, output);// Pin Relay
  regulator.setpoint = 40;// Installation (set at 40 degrees)
  regulator.hysteresis = 5;// The width of the hysteresis
  regulator.k = 0.5;// feedback coefficient (selected in fact)
  //regulator.dt = 500;// set the time of iteration for Getressulttimer
}

// Option with DELAY
VOID loop () {
  Int TEMP;// For example, read the temperature from the sensor
  regulator.input = TEMP;// inform the regulator the current temperature

  // Getressult returns the value for the control device
  digitalWrite (3, regulator.getresult ());// Send by relay (OS works according to your timer)
  DELAY (100);
}

/*
  // option with a built -in timer
  VOID loop () {
  Int TEMP;// For example, read the temperature from the sensor
  regulator.input = TEMP;// inform the regulator the current temperature

  // Getressult returns the value for the control device
  DigitalWrite (3, regulator.getresulttimer ());// Send by relay
  
  // You can also get a value from the regulator output
  // Regulator
  }
*/

/*
// Option with your timer
VOID loop () {
  static uint32_t mytimer = 0;
  if (millis () - mytimer> 2000) {// your timer by millis, 2 seconds
    Mytimer = Millis ();
    Int TEMP;// For example, read the temperature from the sensor
    regulator.input = TEMP;// inform the regulator the current temperature

    // Getressult returns the value for the control device
    digitalWrite (3, regulator.compute (2));// Send by relay.We pass the time manually, we have 2 seconds
  }
}
*/
`` `

<a id="versions"> </a>
## versions
- V2.1 - Fixed Getressulttimer
- V2.2 - the algorithm is improved and simplified

<a id="feedback"> </a>
## bugs and feedback
Create ** Issue ** when you find the bugs, and better immediately write to the mail [alex@alexgyver.ru] (mailto: alex@alexgyver.ru)
The library is open for refinement and your ** pull Request ** 'ow!


When reporting about bugs or incorrect work of the library, it is necessary to indicate:
- The version of the library
- What is MK used
- SDK version (for ESP)
- version of Arduino ide
- whether the built -in examples work correctly, in which the functions and designs are used, leading to a bug in your code
- what code has been loaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed.Not a canvas of a thousand lines, but a minimum code