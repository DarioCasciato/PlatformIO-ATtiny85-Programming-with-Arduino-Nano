##  The Pain of Undocumented Programming

I think finding out how to program a new microcontroller for the first time without proper documentation can be one of the most painful tasks in embedded systems. This task mostly involves finding YouTube videos that explain almost your case but not exactly, scrolling through websites and not finding any relevant information, and finally thinking you found the solution, but then an error pops up that has never been recorded in human history, so you don't find anything about it on the web.

That's why I want to close a gap with this article about **how to program an ATtiny85 with an Arduino via PlatformIO.**

### Disclaimer

This article focuses only on how to program an ATtiny85. So it is not a complete guide on also how to set up and program an Arduino. In this article, I will do all examples with an Arduino Nano.

## Overview

These are the general steps on how to program the ATtiny85:

- Set up the Arduino as ISP programmer
- Wiring up the ATtiny85
- Setting up the PlatformIO Project
- Program it!

## 1. Set up the Arduino as ISP programmer

The first step is to modify the Arduino's behavior, transforming it into a capable ISP (In-System Programmer). This step is relatively simple: you just need to upload a code to the Arduino. It doesn't matter if you upload the code with the Arduino IDE or via PlatformIO.

The Code can be found here: [Arduino as ISP Code](https://github.com/DarioCasciato/PlatformIO-ATtiny85-Programming-with-Arduino-Nano/blob/main/Step%201%20%7C%20Set%20up%20the%20Arduino%20as%20ISP%20programmer/main.cpp%20(Arduino%20as%20ISP%20Code)).

After this is done, you are ready to wire up the ATtiny85 with the Arduino!

## 2. Wiring up the ATtiny85

The wiring requires one additional part: a capacitor with a minimum capacity of 1µF is needed between the Reset and Ground Pin of the Arduino. The capacitor prevents the Arduino from resetting while programming the ATtiny85.

These are the connections:

| Arduino        | ATtiny85 |
| -------------- | -------- |
| 5V             | VCC      |
| Ground         | Ground   |
| Digital pin 13 | GPIO 2   |
| Digital pin 12 | GPIO 1   |
| Digital pin 11 | GPIO 0   |
| Digital pin 10 | Reset    |

Here's an illustration of all connections:

![Wiring](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yxuh5jwchpb8fbry2m25.png)
[Image Sources](https://github.com/DarioCasciato/PlatformIO-ATtiny85-Programming-with-Arduino-Nano/blob/main/Step%202%20%7C%20Wiring%20up/Sources.txt)

### Note

Even if there is an Arduino Nano in the illustration, you can use another Arduino (e.g., Uno, Mega, etc.) as long as you use the same digital Pins mentioned above.

## 3. Setting up the PlatformIO Project

Now comes the important step: setting up the project. Open VSCode (in my case) and access the PlatformIO Home. In the "quick access" tab, select "New Project." There, you'll find three input fields:

- Name
- Board
- Framework

Give your project a suitable name.

In the "Board" input field, type "ATtiny85". You should see one result called "Generic ATtiny85 (Atmel)," and select it. Ensure that the Framework is "Arduino."
Optionally, you can change the Location of the Project on your computer by unticking the checkbox and selecting a location.
If everything is done, click "Finish". PlatformIO will set up the Project, which usually takes a maximum of 20 seconds.

After the Project is set up, the "platform.ini" file should open automatically. If not, you can find it in the main directory of the Project.
In this file, you'll find all configurations for the project, such as how it should be uploaded, which framework it uses, which microcontroller it's for, etc.

This is the critical file that needs to be changed to program the ATtiny with the Arduino. Delete the content of this file, and paste in the following snippet:

```
[env:program_via_ArduinoISP]
platform = atmelavr
framework = arduino
board = attiny85
upload_protocol = stk500v1
; each flag in a new line
upload_flags =
    -P$UPLOAD_PORT
    -b$UPLOAD_SPEED

upload_speed = 19200
upload_port = COMX ; Set the port to the Arduino COM Port
```
(You can find this snippet in the [GitHub repository](https://github.com/DarioCasciato/PlatformIO-ATtiny85-Programming-with-Arduino-Nano/blob/main/Step%203%20%7C%20Setting%20up%20the%20PlatformIO%20Project/platform.ini%20(for%20ATtiny85)))

Here you have to make one customization. In the last line, you need to change "COMX" to the right COM Port of your Arduino. E.g., if your Arduino is connected to the COM Port 3, you have to change it to "COM3."

Now the Project setup is finished, and the ATtiny can be programmed!

## 4. Program it!

In the `src` directory, you can find the `main.cpp` with the basic example code of the Arduino. Write your code into this file, and then you should be able to upload it.

You can upload it in the PlatformIO tab on the left and click "Upload".

The console output should look something like this:

```
Building in release mode
Checking size .pio\build\program_via_ArduinoISP\firmware.elf
Advanced Memory Usage is available via "PlatformIO Home > Project Inspect"
RAM:   [==        ]  16.4% (used 84 bytes from 512 bytes)
Flash: [==        ]  15.0% (used 1230 bytes from 8192 bytes)
Configuring upload protocol...
AVAILABLE: stk500v1
CURRENT: upload_protocol = stk500v1
Looking for upload port...
Uploading .pio\build\program_via_ArduinoISP\firmware.hex

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.02s

avrdude: Device signature = 0x1e930b (probably t85)
avrdude: erasing chip
avrdude: reading input file ".pio\build\program_via_ArduinoISP\firmware.hex"
avrdude: writing flash (1230 bytes):

Writing | ################################################## | 100% 1.81s

avrdude: 1230 bytes of flash written
avrdude: verifying flash memory against .pio\build\program_via_ArduinoISP\firmware.hex:
avrdude: load data flash data from input file .pio\build\program_via_ArduinoISP\firmware.hex:
avrdude: input file .pio\build\program_via_ArduinoISP\firmware.hex contains 1230 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 0.88s

avrdude: verifying ...
avrdude: 1230 bytes of flash verified

avrdude: safemode: Fuses OK (E:FF, H:DF, L:62)

avrdude done.  Thank you.
```

You have successfully programmed your ATtiny85!
