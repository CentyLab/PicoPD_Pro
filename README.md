# PicoPD Pro
Pico PD Pro is a development board for USB Power Delivery development with Raspberry Pi Pico footprint. The board can help you negotiate voltage from USB PD 3.1 with PPS (programable power supply) and AVS (Adjustable Voltage Supply) up to 5A of current at 30V max. The board feature input current/voltage/temperture reading as well as power switch to cut of the current to external devices. The 30V voltage limit is the max input voltage of the AP33772S IC.

# Quick links
[AP33772S Cpp Library](https://github.com/CentyLab/AP33772S-Cpp)

[AP33772S CircuitPython Library](https://github.com/hansendc/CircuitPython_AP33772s/) -> Big thanks to [@hansendc](https://github.com/hansendc)

[PicoPD Pro Pinout](#picopd-pro-pinout)

[Setup for AP33772S objects](#setup-for-AP33772S-objects)

[Basic AP33772S functions](#basic-ap33772S-functions)

[Important note](#important-note)

[Arduino IDE setup](#arduino-ide-setup)

[VSCode PlatformIO setup](#vscode-platformio-setup)

[Enable Serial print for debugging](#enable-serial-debug)

# PicoPD Pro Pinout
![pinout](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/PicoPD-pinout-diagram.jpg?raw=true)


# Setup for AP33772S objects
To use AP33772 IC with the library, first you need to create a `usbpd` object. Then set up your I2C pins and start collecting the power profiles from your charger. Ensure your delay after `Wire.begin()` is more than 500ms for stable PDOs request. Enable pin 23 to allow VBUS pass-through.
```
AP33772S usbpd; // Automatically wire to Wire0

void setup()
{
  Wire.setSDA(0); // Set the correct SDA0 pin
  Wire.setSCL(1); // Set the correct SCL0 pin
  Wire.begin();
  Serial.begin(115200);
  delay(1000); 		// Delay is needed for the IC to obtain PDOs
  usbpd.begin(); 	// Start pulling the PDOs from power supply

  pinMode(25, OUTPUT); // Built in LED

  usbpd.printPDO(); //Print out PDO to Serial port
  usbpd.setOutput(0); // Turn off output switch
}
```

# Basic AP33772S functions
Common functions are:
```
displayProfiles(); //Print out PDOs in Serial
mapPPSAVSInfo();
setFixPDO(int pdoIndex, int max_current);
setPPSPDO(int pdoIndex, int target_voltage, int max_current);
setAVSPDO(int pdoIndex, int target_voltage, int max_current);
setOutput(uint8_t flag);

readVoltage();      //Return voltage in mV
readCurrent();      //Return current in mA      
readTemp();         //Return temperature in C                          
```

# Important note

Calling `usbpd.displayProfiles()` without `Serial.begin()` can soft-brick your PicoPD. Causing the IC to be non-recognizable by your computer. You will have to use PicoProbe to reflash the chip. Check out @Patronics [PicoProbePCB](https://github.com/Patronics/PicoProbePCB) if you would like to make a stable fixture for firmware debugging.

If you want to use PicoPD Pro as an AP33772S evalulation board with your own micro-controller, flash the board with **AP33772S_Eval.uf2** using the drag and drop method. This firmware will config GP0 and GP1 as INPUT to prevent I2C interferance with your own add on micro-controller like ESP32 or STM32.

# Enable Serial Debug

To enable Serial debug, there are 2 main methods:

**Using USB-C Power/Data splitter (easiest):** Use a USB-C Splitter that separates the USB 2.0 signal into another USB-C port. You only need USB 2.0 FullSpeed (12Mbps) for the job, but never hurts to get extra speed if needed later. 

* [USB-C Power/Data Splitter](https://lectronz.com/products/usb-c-splitter) (USB-HighSpeed)
* [USB-C PD Power Splitter](https://www.tindie.com/products/essenceeng/usb-c-pd-power-splitter/) (USB-HighSpeed)


**Using UART communication**: Make sure you are not using GP0 and GP1 as they are reserved for I2C0 channel for AP33772 IC. Check out Youtube tutorial: [Talk to Your Pico Over Serial](https://www.youtube.com/watch?v=pbWhoJdYA1s)

# Arduino IDE setup
Open Arduino IDE, select File -> Preference
![Preference](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc1.png?raw=true)

Copy this line below and place in "Aditional Boards Manager URLs" -> OK. This will allow you to pull the Earlephilhower Pico core that work with the AP33772 library.

```
https://github.com/earlephilhower/arduino-pico/releases/download/global/package_rp2040_index.json
```
![boardmanagerURL](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc2.png?raw=true)

From Board Manager, search for "Pico" and install the "Raspberry Pi Pico/RP2040" core by Earlephilhower

![boardmanager](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc3.png?raw=true)

Now from Tool -> Board -> Raspberry Pi Pico/RP2040 -> Raspberry Pi Pico

![boardmanager](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc4.png?raw=true)

Now download the latest AP33772 library as a Zip file

```
https://github.com/CentyLab/AP33772S-Cpp
```
![ap33772lib](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc5.png?raw=true)

Import the AP33772S library into your Arduino IDE. Sketch -> Include library -> Add .ZIP Library ...

![importlib](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc6.png?raw=true)

Now you start using the Example code in File -> Examples -> AP33772-Cpp-main. The example is ready to compile and flash on to your PicoPD.
![importlib](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc8.png?raw=true)

Now you can plug in your USB-C cable with Power Delivery 2.0/3.0/3.1. If you also like to have serial print out, check out [Enable Serial print for debugging](#enable-serial-debug).

# VSCode PlatformIO setup
After [VSCode](https://code.visualstudio.com/) installation, you will need to install PlatformIO extension.

![installplatform](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc9.png?raw=true)

Now create a new PlatformIO project
+ Name:WhatEverYouWantToCallit
+ Board: Pico (RasberryPi)
+ Framework: Arduino (default)

Click "Finish"

![newproject](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc10.png?raw=true)

![boardandframework](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc11.png?raw=true)

In your new project, copy this and replace everything in platformio.ini. This is how you ask PlatformIO to compile using Earlephilhower Pico core, and enable upload via USB.


```
[env:pico]
platform = https://github.com/maxgerhardt/platform-raspberrypi.git
board = pico
framework = arduino
board_build.core = earlephilhower

;; If directly upload from PC
upload_protocol = picotool
```

Now download the latest AP33772 library as a Zip file. Unzip and place the folder in `lib`

```
https://github.com/CentyLab/AP33772S-Cpp
```

![ap33772lib](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc5.png?raw=true)

Your lib folder should look like this

![libstructur](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc12.png?raw=true)

To try out example code, you copy the content inside `lib/Compiled for PicoPD/PPSCycling/PPSCycling.ino`, And replace everything in your `src/main.cpp`

![maincpp](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc13.png?raw=true)

Now you can plug the PicoPD to your computer via USB-C and hit the upload button. After this, you will have to connect a charger to PicoPD to see the voltage cycling example. If you also like to have serial print out, check out [Enable Serial print for debugging](#enable-serial-debug).

![maincpp](https://github.com/CentyLab/PicoPD_Pro/blob/main/Document/doc14.png?raw=true)