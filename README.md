# smr3961

This repository contains the material used for the hands-on Arduino sessions of [SMR3961 workshop](https://indico.ictp.it/event/10499). This material lets you connect an [Arduino® Nicla Vision board](https://store.arduino.cc/products/nicla-vision) to the Arduino IDE and Edge Impulse®. [You can find all the workshop materials here](https://tinyml.seas.harvard.edu/SustainableDev-24/).

## Contents

1. The Workshop Board: The Nicla Vision
2. Arduino Ecosystem Installation
3. Onboard Sensors Test 

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

- **IMU**: To use the onboard IMU of the Nicla Vision with the Arduino IDE, we must first install the `Arduino_LSM6DSOX` library, which can be found in the Arduino IDE Library Manager. To install the library, select the Library Manage from the left side menu, and then search for `LSM6DSOX`. **Install the library from Arduino**. Go to **Examples > Arduino_LSM6DSOX > SimpleAccelerometer** with the library installed and run the accelerometer test. Upload the example code to the Nicla Vision and open the Serial Plotter to see the accelerometer output.

![Accelerometer test sketch](/assets/accelerometer_test.gif)

