---

title: Wiring the MCU 8266 ESP-12
date: 2016-07-31 21:20 +02:00

---

Below a process to update the 8266 firmware and get it running.

# Wire

My module is the [ESP-12](https://www.amazon.de/gp/product/B00WJNHOGY/ref=oh_aui_detailpage_o01_s00?ie=UTF8&amp;psc=1){:target="_blank"} version of the 8266; every version and board has its own wiring.

You can find a [discussion about the white board](http://www.esp8266.com/viewtopic.php?f=13&amp;t=6505){:target="_blank"} I am using at the esp8266.com forum.  It contains the necessary pullups for pins `RST` and `CH_PD`, while `GPI015` is GND.

In order to prepare the board for Arduino, I am using a non-inverting Hex buffer/converter, the  [CD4050B](http://www.ee.washington.edu/stores/DataSheets/cd4000/cd4049.pdf){:target="_blank"}; it converts 5v from Arduino output to 3.3v required from the 8266 input. For the tutorial below you can use a direct connection between the 8266 and the serial port.

Use a external 3.3v power supply; be sure it can provide at least 500mA; I combined a [12V power supply](https://www.amazon.de/gp/product/B0056SV18Q/ref=oh_aui_detailpage_o00_s00?ie=UTF8&amp;psc=1){:target="_blank"} with a [5v/3.3v converter](https://www.amazon.de/Breadboard-Module-Adapter-Schild-Arduino-Board/dp/B01HN0HVY2/ref=sr_1_23?s=ce-de&amp;ie=UTF8&amp;qid=1469214045&amp;sr=1-23&amp;keywords=versorgungsmodul){:target="_blank"}.

Use a serial-to-ttl USB port, like this [CH340G](https://www.amazon.de/EasyWordMall-CH340G-Serial-Converter-Upgrade/dp/B00YHP8LP0/ref=sr_1_5?ie=UTF8&amp;qid=1468529490&amp;sr=8-5&amp;keywords=USB+to+3.3V+TTL+converter){:target="_blank"};

* connect `Vcc` to 3.3v, and `GND` to GND from power supply and to GND of the serial converter;

* connect `Rx` from serial to `Tx` on the 8266, and `TX` from serial to `RX` of the 8266; to use with Arduino, I connected the `Tx` from Arduino to `pin 3` of the 4050, and `pin 2` to `Rx` of 8266; powering the 4050 with 3.3v, I convert the 5v from Arduino to 3.3v supported by the 8266.

* `RST` should be pulled up: connect to 3.3v via a 1K resistor. I connected a button to connect the pin to ground when need to reset.

* `GPI00` should stay floating, but I also connected to a button to pull it to GND to flash the firmware.

![]({{ site.url }}/assets/images/2016-07-31-wiring-the-esp8266/esp8266.png)


# Update firmware

* download firmware from [electrodragon](http://www.electrodragon.com/w/ESP8266_AT_Commands){:target="_blank"}
* install the python tool [esptool](https://github.com/themadinventor/esptool){:target="_blank"}, a ROM flash utility for the 8266; you can install via [Pypi](https://pypi.python.org/pypi/esptool){:target="_blank"}
* connect the serial converter to a USB, and detect which is the port (something like COM4 in Windows, and /dev/ttyUSB0 in Linux)
* launch esptool

```
esptool.py --port /dev/ttyUSB0 write_flash 0x00000 v1.3.0.2\ AT\ Firmware.bin
```

for other version of the firmware refer to dedicate documentation (e.g. the expressif firmware is made of 4 files, and the 4 memory addresses are available at the same github repository, see links below)

You will receive an output like this

![]({{ site.url }}/assets/images/2016-07-31-wiring-the-esp8266/flashing-esp8266.png)

# Test

Open a serial interface: I used [cutecom](http://freecode.com/projects/cutecom){:target="_blank"}, an alternative to Putty for Windows, but also the Arduino IDE with the serial port communicator will do the task. Be sure to set the baud rate at 115,200, as this is the default with the new firmware.

Try some basic commands

* just return an "OK"

```
AT

OK
```

* Firmware version

```
AT+GMR
AT version:0.40.0.0(Aug  8 2015 14:45:58)
SDK version:1.3.0
Ai-Thinker Technology Co.,Ltd.
Build:1.3.0.2 Sep 11 2015 11:48:04
```

* CWmode: to operate wifi it should be setup to 3

```
AT+CWMODE?

+CWMODE:2

OK
AT+CWMODE=3

OK
```

* List available access point

```
AT+CWLAP

+CWLAP:(4,"FRITZ!Box 30 SL",-68,"9c:c7:a6:47:13",1,-37)
+CWLAP:(3,"dvolo-bcf2af753afb",-87,"bc:f2:af:75:fb",1,30)
+CWLAP:(4,"FRITZ!Box",-62,"34:81:c4:b3:58",1,-14)
+CWLAP:(4,"UPC573552",-92,"b0:c2:87:82:e9:",1,-24)
+CWLAP:(4,"Funklch",-76,"5c:dc:96:1:31:c8",4,-22)
+CWLAP:(3,"WLAN-60352",-70,":21:22:2a:8a:13",11,-54)
+CWLAP:(4,"Chrisilan",-85,"24:65:1:0a:83:fe",11,-29)
+CWLAP:(4,"FRITZ!Box Fon WLAN 790",-89,"9c:c7:a6:f4:22:8c",11,-27)
```

* Connect to an access point giving the UUID and the password

```
AT+CWJAP="FRITZ!Box","992815669545102078"

WIFI CONNECTED
WIFI GOT IP

OK
```

# Resources

* firmware update tutorial from [esp8266.com](http://www.esp8266.com/wiki/doku.php?id=loading_firmware){:target="_blank"}
* firmware update tutorial from [medium.com](https://medium.com/@nowir3s/getting-started-with-esp8266-875fb54441d6#.cfy9gegin){:target="_blank}
* AI Thinker AT [firmware](http://www.electrodragon.com/w/ESP8266_AT_Commands){:target="_blank"}
* espressif forum: [latest version of SDKs](http://www.espressif.com/en/support/download/sdks-demos){:target="_blank"} and [documentations](http://www.espressif.com/en/support/download/documents){:target="_blank"}
* espressif github page: [firmware](https://github.com/espressif/ESP8266_AT){:target="_blank"}, and [memory address for each bin](https://github.com/espressif/ESP8266_AT/blob/master/bin/readme.txt){:target="_blank"}
* AT Commands from [espressif documentation](http://espressif.com/sites/default/files/documentation/4a-esp8266_at_instruction_set_en.pdf){:target="_blank"}
* other info collection on the 8266 on [nurdspace](https://nurdspace.nl/ESP8266){:target="_blank"}
* espressif [source code](https://github.com/espressif/esp8266_at/blob/master/at/user/at_cmd.h#L34){:target="_blank"}, you can see the list of AT commands
