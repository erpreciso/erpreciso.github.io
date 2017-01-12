---

title: Arduino entry-level projects
date: 2014-12-13
excerpt: Started learning Arduino programming with some few pre-packed projects
header:
  teaser: /assets/images/arduino/arduino.jpg

---

## Buzzer


Read an analog input (in this case coming from a photoresistor) and output a buzz of variable frequency

{% highlight cpp %}
int sensorValue;
int sensorLow = 1023;
int sensorHigh = 0;
const int ledPin = 13;
void setup(){
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, HIGH);
  while (millis() < 5000){
    sensorValue = analogRead(A0);
    if (sensorValue > sensorHigh){
      sensorHigh = sensorValue;
      }
    if (sensorValue &amp;lt; sensorLow){
      sensorLow = sensorValue;
    }
  }
  digitalWrite(ledPin, LOW);
}

void loop(){
  sensorValue = analogRead(A0);
  int pitch = map(sensorValue, sensorLow, sensorHigh, 50, 4000);
  tone(8, pitch, 20);
  delay(10);
}
{% endhighlight %}

## Variable light

Read three analog inputs, and output a different colored led


{% highlight cpp %}
const int greenLEDpin = 10;
const int redLEDpin = 11;
const int blueLEDpin = 9;
const int redSENSORpin = A0;
const int greenSENSORpin = A1;
const int blueSENSORpin = A2;
int greenValue = 0;
int redValue = 0;
int blueValue = 0;
int redSENSORvalue = 0;
int greenSENSORvalue = 0;
int blueSENSORvalue = 0;

void setup(){
  Serial.begin(9600);
  pinMode(greenLEDpin, OUTPUT);
  pinMode(redLEDpin, OUTPUT);
  pinMode(blueLEDpin, OUTPUT);
}

void loop(){
  redSENSORvalue = analogRead(redSENSORpin);
  delay(5);
  greenSENSORvalue = analogRead(greenSENSORpin);
  delay(5);
  blueSENSORvalue = analogRead(blueSENSORpin);
  delay(5);
  Serial.print("Raw sensor values \t Red: ");
  Serial.print(redSENSORvalue);
  Serial.print("\t Green: ");
  Serial.print(greenSENSORvalue);
  Serial.print("\t Blue: ");
  Serial.println(blueSENSORvalue);
  redValue = redSENSORvalue/4;
  greenValue = greenSENSORvalue/4;
  blueValue = blueSENSORvalue/4;
  analogWrite(redLEDpin, redValue);
  analogWrite(greenLEDpin, greenValue);
  analogWrite(blueLEDpin, blueValue);
}
{% endhighlight %}

## Servo

Command a servo motor with a variable resistor


{% highlight cpp %}
#include <Servo.h>;
Servo myServo;
int const potPin = A0;
int potVal;
int angle;

void setup(){
  myServo.attach(9);
  Serial.begin(9600);
}

void loop(){
  potVal = analogRead(potPin);
  Serial.print("potVal: ");
  Serial.print(potVal);
  angle = map(potVal, 0, 1023, 0, 179);
  Serial.print(", angle: ");
  Serial.println(angle);
  myServo.write(angle);
  delay(15);
}
{% endhighlight %}


## Music Keyboard


{% highlight cpp %}
int notes[]={262,294,330,349};
void setup(){
  Serial.begin(9600);
}

void loop(){
  int keyval = analogRead(A0);
  Serial.println(keyval);
  if (keyval == 1023){
    tone(8, notes[0]);
  }
  else if (keyval >= 990 &amp;&amp; keyval < 1010){
    tone(8, notes[1]);
  }
  else if (keyval >= 505 &amp;&amp; keyval < 515){
    tone(8, notes[2]);
  }
  else if (keyval >= 5 &amp;&amp; keyval < 10){
    tone(8, notes[3]);
  }
  else {
    noTone(8);
  }
}
{% endhighlight %}


## Motor

Configured Arduino to power a motor through a mosfet


{% highlight cpp %}
const int switchPin = 1;
const int motorPin = 8;
int switchState = 0;

void setup(){
  pinMode(motorPin, OUTPUT);
  pinMode(switchPin, INPUT);
}

void loop(){
  switchState=digitalRead(switchPin);
  if (switchState==HIGH){
    digitalWrite(motorPin, HIGH);
  }
  else {
    digitalWrite(motorPin, LOW);
  }
}
{% endhighlight %}

