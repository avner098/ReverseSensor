# ReverseSensor
This project develops a reverse sensor for automobiles with an ultrasonic sensor, Arduino board, and LED display screen. It provides a cost-effective and easy-to-install solution for enhancing parking and reverse driving safety with the added feature of a proximity buzzer for obstacle warning. The open-source project files are available on Github.

You can try out the project in the [Arduino simulator](https://wokwi.com/projects/364343439885255681).


<img src="https://github.com/avner098/ReverseSensor/blob/main/distance%20equal%20to%20390.png" alt="Example Image" width="500" height="300">

<img src="https://github.com/avner098/ReverseSensor/blob/main/distance%20equal%20to%20190.png" alt="Example Image" width="500" height="300">

<img src="https://github.com/avner098/ReverseSensor/blob/main/distance%20equal%20to%2030.png" alt="Example Image" width="500" height="300">



```cpp
// Author: Avner Ben Shlomo



#include <TM1637.h>
#include <HCSR04.h>


const int CLK = 12; // CLK pin TM1637
const int DIO = 13; // DIO pin TM1637
const int trig = 9; // trig pin HCSR04
const int echo = 10;// echo pin HCSR04
const int buzz = 11;// buzzer pin
const int ledCount = 10; //amount LED in the LED bar
const int maxlength = 400; //Maximum distance that the sensor detects

int ledPins[] = {0,1,2,3,4,5,6,7,8,11}; //ledBars pins

TM1637 tm(CLK, DIO); // set the TM1637 led bar
HCSR04 hc(trig, echo); // set the HCSR04 ultrasonic sensor



void setup() {
  
  tm.init();
  tm.set(BRIGHT_TYPICAL);

  for (int i = 0; i < ledCount; i++) {
    pinMode(ledPins[i], OUTPUT); 
  }
}

unsigned int dist = 0;

void loop() {
  // Displays each digit on the display
  tm.display(0, (dist / 1000) % 10);
  tm.display(1, (dist / 100) % 10);
  tm.display(2, (dist / 10) % 10);
  tm.display(3, dist % 10);
  
  //Reads distance from the sensor
  dist=hc.dist();

  //Maps the distance we got from 0 to 400 to 0 to 10 as the amount of leds
  int ledLevel = map(dist, 0, maxlength, 0, ledCount);

  //Determines whether the corresponding LED should light up or not according to the distance
  for(int i=0;i<ledCount;i++){
    if(i<ledLevel){
      digitalWrite(ledPins[i], HIGH);
    }else{
      digitalWrite(ledPins[i], LOW);
    }
  }
 

  
  //If the distance is less than 2 meters the buzzer starts beeping
  if(dist<200){
    tone(buzz, 262, 250);
    delay(dist*10); 
  }
  

  delay(60);
}



