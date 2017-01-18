# BigClown development on Windows

<!-- toc -->


## Introduction


Our software stack and SDK can be easily run also on Windows.
With few simple steps you can develop and test firmware, MQTT messages and more on Windows.
This tutorial was tested on Windows 7 Professional x64.


## How to download SDK


All our code is located on public GitHub account. You have to use GIT to clone the repository.

Download [command line GIT utility](https://git-scm.com/download/win) or you can use [GitHub Desktop](https://desktop.github.com/) with easy graphical interface.

Clone repository `https://github.com/bigclownlabs/bc-core-module` to your machine.
You have to call clone with `--recursive` to clone also the submodules.

`git clone --recursive https://github.com/bigclownlabs/bc-core-module.git`


*GIT tools are not extra necessary but it helps you update to the latest versions of our SDK. You can also download SDK in ZIP file from the GitHub. In that case you have to download submodules separately and unzip them in their respective folders (bcl, doc, stm, sys).*


## How to install compiler


It's not necessary to be able compile firmwares for microcontroller.
You can use our pre-compiled binaries if the fits your needs.
In that case you would just need the DFU firmware flashing utility and drivers which is explained further.

Otherwise download latest [GNU ARM Embedded Toolchain](https://launchpad.net/gcc-arm-embedded/+download) for Windows.
We would suggest the installer because it is easiest solution. Install the GCC compiler and in the last wizard screen after the instalation **select that you would like to add the tools to the PATH environment variable.**


## How to install buildsystem


You will need `make` tool to compile our SDK. There is not simple installer yet so you have to download and copy it manually.

You have to download compiled make utility from [this page](http://www.equation.com/servlet/equation.cmd?fa=make) and then copy it to to some directory which is already in the PATH variable. So you can call `make` from any folder. You can copy it to your newly created GCC ARM Toolchain folder (`C:\Program Files (x86)\GNU Tools ARM Embedded\5.4 2016q3\bin\`).


## How to compile SDK


Now you can compile the SDK by typing `make` in a project directory.

The downloaded GitHub firmware has empty user function. Please see the tutorials and add you application logic in `app/application.c`.

Congratulation for your first compiled firmware.


## How to upload firmware over USB


This Device Firmware Update utility (`dfu-util`) will allow you to upload compiled binary firmware just with USB.
Please follow this help [how to setup USB driver and use DFU Util on Windows](https://doc.bigclown.com/core-module.html#on-windows-10-64-bit-desktop).
If you have the dfu-util in your PATH variable or you copy it to the GCC bin directory, you can then [set the device to the boot mode](https://doc.bigclown.com/core-module.html#programming-using-usb-dfu-bootloader) and flash the firmware.

Set the device to DFU mode and flash it by typing `make dfu`.


## How to debug core module


To debug the running code on Core Module you can use Ozone debugger with J-Link debug probe. It is also possible to use GDB/OpenOCD with other debug probes but this is not documented yet.

Download the [Ozone debugger here](https://www.segger.com/downloads/jlink#Ozone).
Ozone folder also needs to be set in PATH environment variable or you can simply edit Makefile and set absolute path to the Ozone.exe file.

You start debugging by typing `make ozone`.


## How to generate API documentation


Our SDK is using Doxygen to automaticaly generate API documentation from C header files.
This tool is not necessary to install.
The generated API documentation is also on http://api.bigclown.com.
Download the [latest Doxygen here](http://www.stack.nl/~dimitri/doxygen/download.html).

You generate the docs by `make doc`.


## How to install virtual COM port driver


Download and install the [STM32 Virtual COM Port driver](https://drive.google.com/open?id=0B5pXL_JAACMvczQ0MVM1eUZILXc). You can also download latest driver [directly from ST](http://www.st.com/en/development-tools/stsw-stm32102.html) but you will need to register.


## How to install Mosquitto MQTT broker


[Mosquitto MQTT Broker](https://mosquitto.org/download/) is a MQTT server which is a central message hub in our system. Please download, install and run this broker. This broker needs to be running in the background all the time you use our Python Gateway.

To run the MQTT broker go to install directory and type `mosquitto.exe -v`.
Parameter `-v` is for verbose, so you can see the messages.


## How to start the gateway


Download and install [Python 3](https://www.python.org/downloads/) which is needed to run the Gateway. Gateway is a small python program which connects virtual USB serial port of Core Module and MQTT broker.

Connected core module appears as a USB serial device in your computer.
The python gateway script interconnects the core module with MQTT broker.
This section explains how to set up these pieces.

Clone bc-workroom-hub

`git clone https://github.com/bigclownlabs/bc-workroom-hub.git`

**Current bc-workroom-gateway.py needs that you comment out two lines of code where we use fcntl. Will be fixed in next revision**

Then run the script with the correct COM port set.

`python gateway/bc-workroom-gateway.py -d COM2`


## Congratulations!


You have sucessfully installed SDK with gateway. Now you can explore and make new interesting projects.
