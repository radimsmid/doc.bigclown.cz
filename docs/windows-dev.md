# BigClown development on Windows

<!-- toc -->


## Introduction


Our software stack and SDK can be easily run also on Windows.
With few simple steps you can develop and test firmware, MQTT messages and more on Windows.
This tutorial was tested on Windows 7 Professional x64.


## Firmware development


### ARM GCC


It's not necessary to be able compile firmwares for microcontroller.
You can use our pre-compiled binaries.
In that case you would just need the DFU firmware flashing utility and drivers which is explained further.

Otherwise download latest [GNU ARM Embedded Toolchain](https://launchpad.net/gcc-arm-embedded/+download) for Windows.
We would suggest the installer because it is easiest solution. Install the GCC compiler and in the last wizard screen after the instalation **select that you would like to add the tools to the PATH environment variable.**


### MAKE


You will need `make` tool to compile SDK. There is not simple installer yet so you have to download and copy it manually.

You have to download compiled make utility from [this page](http://www.equation.com/servlet/equation.cmd?fa=make) and then copy it to to some directory which is already in the PATH variable. So you can call `make` from any folder. You can copy it to your newly created GCC ARM Toolchain folder (`C:\Program Files (x86)\GNU Tools ARM Embedded\5.4 2016q3\bin\`).


### J-LINK Ozone debugger


In case you will upload and debug firmware on your microcontroller with J-LINK, download this great debugger.

Download the [Ozone debugger here](https://www.segger.com/downloads/jlink#Ozone).
The firmware building makefile will call ozone binary when you type `make ozone`. Ozone folder also needs to be set in PATH environment variable or you can simply edit Makefile and select absolute path to the Ozone.exe file.


### Doxygen


Doxygen is not necessary.
This utility automaticaly generates documentation from source header files. You generate the docs by `make doc`.
The generated API documentation is also on http://api.bigclown.com.

Download the [latest Doxygen here](http://www.stack.nl/~dimitri/doxygen/download.html).


## Bigclown computer gateway and software


Connected core module appears as a USB serial device in your computer.
The python gateway script interconnects the core module with MQTT broker.
This section explains how to set up these pieces.


### STM32 Virtual COM Port driver


Download and install the [STM32 Virtual COM Port driver](http://www.st.com/en/development-tools/stsw-stm32102.html).

*TODO: Needs registration on ST site, can we mirror the installer?*


### USB upload tool - DFU


This utility will allow you to upload compiled binary firmware just with USB.
Please follow this help [how to setup USB driver and use DFU Util on Windows](https://doc.bigclown.com/core-module.html#on-windows-10-64-bit-desktop).
If you have the dfu-util in your PATH variable or you copy it to the GCC bin directory, you can then [set the device to the boot mode](https://doc.bigclown.com/core-module.html#programming-using-usb-dfu-bootloader) and flash it with command `make dfu`.


### GIT Tools


GIT tools are not necessary but it helps you update to the latest versions of our SDK. You can also download SDK in ZIP file from the GitHub. In that case you have to download submodules separately and unzip them in their respective folders (bcl, doc, stm, sys).

Download [command line GIT utility](https://git-scm.com/download/win) or [GitHub Desktop](https://desktop.github.com/) with easy graphical interface.

*TODO: do the submodules download ?*



### Python 3


Download and install [Python 3](https://www.python.org/downloads/) which is neede to run the Gateway. Gateway is a small python program which connects virtual USB serial port of Core Module and MQTT broker.


### Mosquitto MQTT broker


[Mosquitto MQTT Broker](https://mosquitto.org/download/) is a MQTT server whic is a central message hub in our system. Please download, install and run this broker. This broker needs to be running in the background all the time you use our Python Gateway.
