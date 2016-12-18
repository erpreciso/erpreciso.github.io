---
title: Wiring the MPU6050
date: 2016-07-22 22:35:31 +02:00
type: post
tags: [semi-original, make]

---
# Abstract

I'll wire the MPU6050 following the excellent tutorial from [geekmomprojects](http://www.geekmomprojects.com/mpu-6050-dmp-data-from-i2cdevlib/){:target="_blank"}.

I won't use raw data from accellerometer and gyro, but processed data from the DMP (Digital Motion Processor) integrated in the MPU: I will interface with Arduino via the I2C bus, using the library from [Jeff Rowberg](https://github.com/jrowberg/i2cdevlib){:target="_blank"}. Data will be visualized in Processing, using a sketch available in the Jeff Rowberg repository.

# Software versions

* Windows 10 64-bit
* Processing 3.1.1
* Arduino IDE 1.6.9

# Components

* MPU6050; I purchased mine on [amazon.de](https://www.amazon.de/Aukru-MPU-6050-Modul-3-Achsen-analoge-Gyro-Sensor-Beschleunigungssensor-Modul/dp/B00PL70P7K/ref=sr_1_1?s=ce-de&amp;ie=UTF8&amp;qid=1469216147&amp;sr=1-1&amp;keywords=mpu+6050);
* [Arduino Uno](https://www.arduino.cc/en/Main/ArduinoBoardUno)
* breadboard and wiring hardware

![The MPU6050 on a breadboard]({{ site.url }}/assets/images/2016-07-22-wiring-the-mpu6050/mpu6050_breadboard.png)

# Build

* wire pins from MPU6050:

  + VCC to 5v;
  + GND to ground;
  + SCL (clock of the I2C) to pin A5;
  + SDA (serial data) to pin A4;
  + INT to pin 2.

![Breadboard wired]({{ site.url }}/assets/images/2016-07-22-wiring-the-mpu6050/breadboard.png)

* install Arduino library:

  + copy folder MPU6050 from Jeff's repo in your 'libraries' folder
  + open example MPU6050_DMP6;
  + read code comments carefully, then uncomment

`#define OUTPUT_TEAPOT`

and comment

`#define OUTPUT_READABLE_YAWPITCHROLL`

![Code commented]({{ site.url }}/assets/images/2016-07-22-wiring-the-mpu6050/arduino_code.png)

* verify that baud rate in the Arduino sketch is the same of the Processing one

![Baud rates]({{ site.url }}/assets/images/2016-07-22-wiring-the-mpu6050/same_baud_rate.png)

* upload Arduino sketch, and launch Processing

* give at least 10 seconds to the MPU to calibrate itself before moving

# Enjoy result in the processing window

<iframe width="560" height="315" src="https://www.youtube.com/embed/39PVdvu9n1k" frameborder="0" allowfullscreen></iframe>
