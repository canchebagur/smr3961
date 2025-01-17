# SMR3961 Hands-On with the Nicla Vision

This repository contains the material used for the hands-on Arduino sessions of [SMR3961 workshop](https://indico.ictp.it/event/10499). This material lets you connect an [Arduino® Nicla Vision board](https://store.arduino.cc/products/nicla-vision) to the Arduino IDE and Edge Impulse®. [You can find all the workshop materials here](https://tinyml.seas.harvard.edu/SustainableDev-24/).

## Contents

1. The Workshop Board: The Nicla Vision
2. Arduino Ecosystem Installation
3. Onboard Sensors Test
4. Edge Impulse Environment Configuration

## The Workshop Board: The Nicla Vision

The Nicla Vision is a powerful development board with a 2MP color camera in a tiny form factor (23 x 23 mm). With onboard Wi-Fi® and Bluetooth® Low Energy connectivity, the Nicla Vision maximizes compatibility with professional and consumer equipment. 

![The Nicla Vision board](/assets/nicla_vision_01.png)

Some key features of the Nicla Vision are the following:

- **Microcontroller**: Dual 32-bit Arm® Cortex®-M7 running at 480 MHz and Cortex®-M4 running at 240 MHz microcontroller (STM32H747AII6, same as the Portenta family boards), 2 MB of Flash, and 1 MB of RAM.
- **Connectivity**: Wi-Fi® and Bluetooth® Low Energy connectivity.
- **Onboard sensors**: 6-axis IMU (3-axis accelerometer and 3-axis magnetometer), digital microphone (MEMS), and distance sensor (Time-of-Flight sensor).
- **Onboard camera**: 2MP CMOS color camera. 
- **Security**: Onboard crypto chip. 
- **Other**: Onboard battery charger and user-programmable RGB LED. 

![Key features of the Nicla Vision. Image credits: Machine Learning Systems book from Harvard University](/assets/nicla_vision_02.png)

## Arduino Ecosystem Installation

To use the Nicla Vision board with the Arduino ecosystem tools, we need to install the following:

- **IDE**: [Arduino IDE 2.0+](https://www.arduino.cc/en/software) and [OpenMV v4.0+](https://openmv.io/pages/download).
- **Core**: `Arduino Mbed OS Nicla Boards` (installation via the Boards Manager of the Arduino IDE). 

Install the Arduino and OpenMV IDE first, and then use the **Boards Manager** and **Library Manager** of the Arduino IDE to install the board core and libraries. 

***Note: Before using your board, ensure its bootloader is updated to the latest release. This can be done by running the `STM32H747_manageBootloader` example. The example can be found by navigating into **File > Examples > STM32H747_System > STM32H747_manageBootloader** in the Arduino IDE.***

## Onboard Sensors Test 

To make sure the Nicla Vision is working correctly, we need to test the following:

- **Microphone**: The Arduino IDE includes a simple example of visualizing raw data from the onboard microphone. To test it, navigate to **File > Examples > PDM > PDMSerialPlotter**. Upload the example code to the Nicla Vision and open the Serial Plotter to see the sound wave output.

![Microphone test sketch](/assets/microphone_test.gif)

- **IMU**: To use the onboard IMU of the Nicla Vision with the Arduino IDE, we must first install the `Arduino_LSM6DSOX` library, which can be found in the Arduino IDE Library Manager. To install the library, select the Library Manage from the left side menu, and then search for `LSM6DSOX`. **Install the library from Arduino**. Then, go to **Examples > Arduino_LSM6DSOX > SimpleAccelerometer** with the library installed and run the accelerometer test. Upload the example code to the Nicla Vision and open the Serial Plotter to see the accelerometer output.

![Accelerometer test sketch](/assets/accelerometer_test.gif)

- **Distance sensor**: To use the onboard distance sensor of the Nicla Vision with the Arduino IDE, we must first install the `VL53L1X` library from Pololu, which can be found in the Arduino IDE Library Manager. To install the library, select the Library Manage from the left side menu, and then search for `VL53L1X`. **Install the library from Pololu**. Then, compile and upload the example code below to your Nicla Vision.

```arduino
#include "VL53L1X.h"
VL53L1X proximity;

bool blinkState = false;
int reading = 0;
int timeStart = 0;
int blinkTime = 2000;

void setup() {
  Serial.begin(115200);
  Wire1.begin();
  Wire1.setClock(400000); // use 400 kHz I2C
  proximity.setBus(&Wire1);


  pinMode(LEDB, OUTPUT);
  digitalWrite(LEDB, blinkState);

  if (!proximity.init()) {
    Serial.println("Failed to detect and initialize sensor!");
    while (1);
  }

  proximity.setDistanceMode(VL53L1X::Long);
  proximity.setMeasurementTimingBudget(50000);
  proximity.startContinuous(50);
}

void loop() {
  reading = proximity.read();
  Serial.print(reading);
  Serial.println(" mm");

  if (millis() - timeStart >= reading) {
    digitalWrite(LEDB, blinkState);
    timeStart = millis();

    blinkState = !blinkState;
  }
}
```
The distance measured by the distance sensor will be printed on the Arduino IDE's Serial Monitor, and the built-in LED will blink proportionally to that distance.

![Distance sensor test sketch](/assets/distance_test.gif)

- **Onboard camera**: To use the onboard camera of the Nicla Vision we will use the OpenMV IDE, which is designed to work specifically with machine/computer vision hardware.

***The Nicla Vision uses a 2MP camera sensor, meaning its maximum resolution is 1920x1080 pixels. However, the effective resolution is 1616×1232 pixels.*** 

With OpenMV running, connect your Nicla Vision to your computer. Select the **Connect** icon in the bottom left side of the OpenMV IDE; you should see your Nicla Vision onboard green LED start flashing; this indicates that your board is on *bootloader mode*.

![OpenMV IDE](/assets/OpenMV_IDE_01.png)

The following connection dialogue will open; select the "**Install the latest release firmware**" option. 

![OpenMV IDE](/assets/OpenMV_IDE_02.png)

***Note: **DO NOT ERASE THE INTERNAL FILE SYSTEM of your Nicla Vision board.*****

Now, navigate to select **File > New File** and add the following code into the OpenMV code editor window and save it as `nicla_vision_test.py`. The following code will start the Nicla Vision camera and display the feed in the OpenMV IDE's frame buffer. You will also use this code to capture frames for the ML model:

```python
import sensor, image, time

sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.QVGA)
sensor.skip_frames(time = 2000)

clock = time.clock()

while(True):
    clock.tick()
    img = sensor.snapshot()
    print(clock.fps())
```

Select the **Start** icon (green triangle) in the bottom left of the OpenMV IDE; the camera feed will display in the OpenMV IDE's frame buffer (notice that the dynamic color balance is also shown). 

![OpenMV IDE](/assets/OpenMV_IDE_03.png)

## Edge Impulse Environment Configuration

Let's set up the Edge Impulse® environment on your computer. The first step for Edge Impulse configuration is to install its CLI. [Follow these instructions to install the Edge Impulse CLI](https://docs.edgeimpulse.com/docs/tools/edge-impulse-cli/cli-installation). 

***Windows users: Install [Visual Studio Community](https://visualstudio.microsoft.com/downloads/) and [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022) before you install Edge Impulse CLI.***

After correctly installing Edge Impulse CLI, [install the latest Arduino CLI version](https://arduino.github.io/arduino-cli/0.35/installation/). For guidance in Windows, [watch the steps shown in this video](https://www.youtube.com/watch?v=1jMWsFER-Bc).

Now, let's test the Edge Impulse CLI and the Arduino CLI installation:

- Download the [latest Edge Impulse firmware for the Nicla Vision](https://cdn.edgeimpulse.com/firmware/arduino-nicla-vision.zip) and unzip it.
- Open the .zip file on your computer and select the uploader corresponding to your operating system (Linux, Mac, or Windows).
- Put the Nicla Vision on `BOOT` mode by pressing the reset button twice.
- Execute the specific uploader for your operating system to upload the `arduino-nicla-vision.bin` file to your board.

Go to your project on Edge Impulse Studio. On the `Data Acquisition` tab, select WebUSB (1). A window will pop up; choose the option that shows that the Nicla is paired (2) and press `Connect` (3).6r