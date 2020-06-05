# Arduino - Press a button to turn on and turn off something using servo

Created this to turn on my AC for 10 minutes every one hour. Uses a Servo to press a soft button on the Thermostat on the wall. For now powering it with a powerbank and USB cable.
## Components
Arduino board
Servo
Push button
Jumper cables
Glue gun

## Circuit
<!-- [![Circuit Design](https://raw.githubusercontent.com/vixyad/arduino-onoff-servo/master/arduino-on-off-servo-circuit.png)](https://github.com/vixyad/arduino-onoff-servo)-->

## Installation
<!-- [![Installation](https://raw.githubusercontent.com/vixyad/arduino-onoff-servo/master/arduino-result.jpg)](https://github.com/vixyad/arduino-onoff-servo)-->

## Code
```c
// Include the Servo library
#include <Servo.h>

// Declare Constants
const int servoPin = 9;
const int onTime = 10; //minutes
const int offTime = 60; //minutes
const int pressTime = 350;  //milliseconds
const int buttonPin = 3;

const int ledPin =  LED_BUILTIN;
const int ledOnInterval =  300; //millis
const int ledOffInterval =  1000; //millis


const bool LED_BLINK_FAST_MODE = 13;
const bool LED_BLINK_SLOW_MODE = 0;


// Declare changing variables
unsigned long onTimeInMillis = 0;
unsigned long offTimeInMillis = 0;
unsigned long previousMillis = 0;
unsigned long previousMillisLED = 0;
unsigned long currentMillis = 0;
bool buttonPressed = false;

int buttonState = 0;
int ledState = LOW;



int switchState = LOW;

// Create a servo object
Servo Servo1;

void setup() {
  Serial.begin(9600);
  Serial.print("Setting Up Arduino");
  //Calculate On Time in Millis
  onTimeInMillis = onTime * 60L * 1000L;
  offTimeInMillis = offTime * 60L * 1000L;
  
  //Pin mode needs to be changed to Output else it hardly glows due to pwm pin mode or something, not sure
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT);
  
  // We need to attach the servo to the used pin number
  Servo1.attach(servoPin);
  
  //Bring To Original Position
  Servo1.write(0);
  Serial.print("Arduino Brought to 0");
  Serial.println();
  
  switchState = LOW;
  ledState = LOW;
  currentMillis = millis();
  
  //To press the buttton first Time
  //previousMillis = currentMillis-onTimeInMillis-1;
  
  Serial.print("Switch State=");
  Serial.print(switchState,DEC);
  Serial.println();
  Serial.print("Current Millis=");
  Serial.print(currentMillis,DEC);
  Serial.println();
  Serial.print("onTimeInMillis interval =");
  Serial.print(onTimeInMillis,DEC);
  Serial.println();
  Serial.print("offTimeInMillis interval =");
  Serial.print(offTimeInMillis,DEC);
  Serial.println();
  Serial.print("Setup complete Waiting 5 seconds");
  Serial.println();
  //Wait 5 seconds before doing anything
  delay(5000);

  
  //Switch it On First Time
  pressButton();
  switchState = HIGH;
  previousMillis = millis();
  delay(1000);

}


void loop() {

  //Calculate the Current Milliseconds of Arduino
  currentMillis = millis();


    //###################################################################
  //########################## Switch On Switch Off Logic  ############
  //###################################################################

  // If Switch is Off 
  if (switchState == LOW) {

    //Blinks LED in ON mode, blinks Fast 
  blinkLED(LED_BLINK_SLOW_MODE);
  
  //Turn Switch ON if it reached ON time Interval
    if (currentMillis - previousMillis >= offTimeInMillis) {
      Serial.println();
    Serial.print("Switch was off, Crossed OFF Interval time , Switching ON AC");
    Serial.println();
      pressButton();
      previousMillis = currentMillis;
      switchState = HIGH;
    }
  

  }

  // If Switch is on
  else {
    
  //Blinks LED in ON mode, blinks slowly 
  blinkLED(LED_BLINK_FAST_MODE);
  
  //Turn Switch ON if it reached OFF Time Interval
    if (currentMillis - previousMillis >= onTimeInMillis) {
      Serial.println();
    Serial.print("Switch was on , Crossed ON Interval time , Switching Off AC");
      Serial.println();
    pressButton();
      previousMillis = currentMillis;
      switchState = LOW;
    }
  
  }


    //###################################################################
  //########################## Check Button Presed  ###################
  //###################################################################
    
  // read the state of the pushbutton value:
  buttonState = digitalRead(buttonPin);

  // check if the pushbutton is pressed. If it is, the buttonState is HIGH:
  if (buttonState == HIGH) {
    buttonPressed = true;
    digitalWrite(ledPin, HIGH);
  } 
  else {
    // turn LED off:
    //digitalWrite(ledPin, LOW);
  
  //Button was pressed so execute below code on release of button
    if (buttonPressed){
    digitalWrite(ledPin, LOW);
      pressButton();
      previousMillis = millis();
    previousMillisLED = millis();
      if (switchState == LOW){
       Serial.println();
     Serial.println("Switch was OFF, when button released, changed state to ON");
        switchState = HIGH;
      }
      else { 
      Serial.println();
      Serial.println("Switch was ON, when button released, changed switch state to OFF");
        switchState = LOW;
      }
      buttonPressed = false;
    }
  }
  
  
}

void pressButton() {
  //Press Button To switch on
  Servo1.write(30);
  delay(pressTime);
  Servo1.write(0);
  delay(1000);
}

void blinkLED(bool switch_is_on) {
  if (switch_is_on == LED_BLINK_FAST_MODE) {
    if (currentMillis - previousMillisLED >= ledOnInterval) {
      // save the last time you blinked the LED
      previousMillisLED = currentMillis;

      // if the LED is off turn it on and vice-versa:
      if (ledState == LOW) {
      Serial.print("ON");
        ledState = HIGH;
      } else {
      Serial.print("OFF");
        ledState = LOW;
      }

      // set the LED with the ledState of the variable:
      digitalWrite(ledPin, ledState);
    }
  
  }
  else {
    if (currentMillis - previousMillisLED >= ledOffInterval) {
      // save the last time you blinked the LED
      previousMillisLED = currentMillis;

      // if the LED is off turn it on and vice-versa:
      if (ledState == LOW) {
          Serial.print("ON");
          ledState = HIGH;
      } else {
        Serial.print("OFF");
        ledState = LOW;
      }
      // set the LED with the ledState of the variable:
      digitalWrite(ledPin, ledState);
    }
  }
}

```

