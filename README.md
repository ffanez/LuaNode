ESP-LUA-RTOS-SDK
======================================

This project is a `lua` `rtos` sdk, based on esp-open-rtos-sdk, for esp8266/esp32.
The hardware is provided by www.doit.am.

![github](http://bbs.doit.am/data/attachment/common/19/common_36_banner.jpg "esp8266")

PROJECT STRUCTRUE:
--------------------------------------
```
esp-lua-sdk-master
    -->core  
    -->examples
        -->simple
        -->lua_test
        -->terminal
    -->extras
        -->bmp180
        -->cpp_support
        -->dhcpserver
        -->driver
        -->ds18b20
        -->i2c
        -->lua
        -->mbedtls
        -->modules
        -->mylibc
        -->onewire
        -->platform
        -->pwm
        -->rboot-ota
        -->spiffs
        -->stdin_uart_interrupt
        -->ws2812
    -->FreeRTOS
        -->Source
        -->component.mk
    -->include
    -->ld
    -->lib
    -->libc  
    -->lwip
    -->utils
    common.mk
```


HOW TO BUILD:
--------------------------------------

#### Requirements:

The following tools are required, use `apt-get` to install them.

* GNU autoconf, libtool, libtool-bin
* GNU flex, bison, gawk,
* libexpat1-dev


#### Windows:

We utilize VirtualBox (VB) with Ubuntu installed as build environment. If your OS is Linux, you can also setup the build environment according the following steps:

* 1. Download and install [VirtualBox-5.0.16-105871-Win](https://www.virtualbox.org/ "VirtualBox") (You'd better install the latest version, since I found that the older version such as 4.x cannot install the VB Guest Additions)
* 2. Download [ubuntu-15.10-desktop-amd64.iso](http://www.ubuntu.com/download/ "Ubuntu") and install it on VB. Make sure that asign more than 1G RAM and 12G Hard Disk for this virtual machine. The tool chain build process require much memory!
* 3. Setup share file folder: Settings->Share Folders->Add new shared folder, input your share folder path and the select `Make Permanent`, Note that `do not` select Auto Mount. Launch Ubuntu and then create a folder to mount the share folder, I create a folder, named `share`, in the path /mnt. Type the commond in the terminal to mount share folder:

```sh
sudo mount -t vboxsf share /mnt/share 
```

Note: if you mount failed, check whether the module `vboxsf` is exist by executing commond `lsmod | grep vboxsf`. If nothing found, maybe the `Guest Additions` are not installed correctly. Try to install that again or upgrade your VirtualBox to the latest version.

* 4. Download tool chain: esp-open-sdk; Before downloading, make sure that the `git` has been installed, if not, install it by `sudo apt-get install git`. I create path `/opt/esp-open-sdk` and then install to sdk to it; Execute the following commond to download sdk:
  
```sh
git clone --recursive https://github.com/pfalcon/esp-open-sdk.git /opt/esp-open-sdk
```
  
* 5. Install esp-open-sdk: Before installing, make sure that all the required tools list in `Requirements` are installed on your virtual machine. Type `make STANDALONE=y` to build the tool chain.
  
Note: if your machine is not enough memory, the error such as `Build failed in step: 'installing pass-2 core C compiler'` may occur. The following screenshot shows the error:

![github](http://ww2.sinaimg.cn/mw690/999babe3gw1f2iq57ya69j20hq09ajw3.jpg "github")
  
* 6. After installing the esp-open-sdk, export the bin path by the following commond, then you can build your source on your machine!
  
```sh
export PATH=/opt/esp-open-sdk/xtensa-lx106-elf/bin:$PATH
```

Now that you can place your source in the share folder, build it with your virtual machine.


--------------------------------------

If you prefer IDE, such as `Eclipse`, you can also build the firmware with Eclipse. To setup Eclipse, follow the steps list below:

* 1. Download and install the [Unofficial Development Kit for Espressif ESP8266](http://programs74.ru/udkew-en.html "Unofficial Development Kit for Espressif ESP8266")
* 2. Download and install the [Java Runtime x86 (jre-7uXX-windows-i586.exe)](http://www.oracle.com/technetwork/java/javase/downloads/index.html "Java Runtime x86")
* 3. Download and install [Eclipse Mars x86 to develop](http://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/mars/R/eclipse-cpp-mars-R-win32.zip "Eclipse") in C++ (eclipse-cpp-mars-R-win32.zip). Unpack the archive to the root of drive C.

```
Note: You should place the Eclipse under the path C:, since the install-mingw-package.bat   
run in step 5 will search the path. If install-mingw-package.bat run failed, it may   
probably you didn't place Eclipse in C:
```

* 4. Download and install [MinGW](https://sourceforge.net/projects/mingw/files/Installer/ "MinGW"). Run the downloader mingw-get-setup.exe, `the installation process to select without GUI, ie uncheck "... also install support for the graphical user interface"`.
* 5. Run from the file `install-mingw-package.bat`. He will establish the basic modules for MinGW, installation should proceed without error.
* 6. Start the Eclipse Luna from the directory c:\eclipse\eclipse.exe
* 7. In Eclipse, select File -> Import -> General -> Existing Project into Workspace, in the line Select root directory, select the directory C:\Espressif\examples and import work projects.
Further, the right to select the Make Target project, such as hello-world and run the target All the assembly, while in the console window should display the progress of the build. To select the target firmware flash.

Now that you can build firmware using Eclipse, if you have any questions, go to `ESP8266 Community Forum`, read this [post](http://www.esp8266.com/viewtopic.php?f=9&t=820) for details.


#### Linux:

* System requiement: RAM>1G; Hard Disk>10G
* Setup the build environment as that on Windows:)

#### Mac OS:

* Download and install VirtualBox for Mac
* Assign memory > 1G and hard disk > 12G for the virtual machine
* Setup the build environment as that on Windows:)


HOW TO FLASH THE FIRMWARE:
--------------------------------------

#### Flash tool for Windows:

You can download the windows flash tool [HERE](http://www.baidu.com). This tool is official Espressif flash tool.

![github](http://ww3.sinaimg.cn/mw690/999babe3gw1f2je4e2012j20u80hs7b2.jpg "Flash Tool")

You can also use `esptool` to flash the bin files on Linux, instead of Espressif flash tool.


HOW TO DEBUG:
--------------------------------------
I use SecureCRT to monitor the output from UART0. When you try to utilize this tool, you should setup serial as bellow:

![github](http://ww4.sinaimg.cn/mw690/999babe3gw1f2jvwv34spj20y80j444u.jpg "SecureCRT")

```
Note: In the serial settings, you must uncheck XON/XOFF, otherwise, you won't be able to input via serial!
```

To set break points and debug code, maybe you can use GDBSTUB.


EXAMPLES:
--------------------------------------

* `simple`: This is a sample to show how to create an os task. Build the example by typing `make` or `make rebuild` in the example directory.

How to create a task:

```c
void task1(void *pvParameters) {
    // do something
}

void user_init(void) {
    xTaskCreate(task1, (signed char *)"tsk1", 256, &mainqueue, 2, NULL);
}
```

* `lua_test`: A lua sample. 

* `terminal`: An uart sample. Set baud rate to 115200pbs, using UART0. Note that the extras/stdin_uart_interrupt must be included in the sample Makefile, when you want to enable uart device.



CONTACT ME:
--------------------------------------

  - `Email`: nicholas3388@gmail.com
  - `QQ`: 535873968
  - `Homepage`: www.apparduino.com

REFERENCE:
--------------------------------------

* esp-open-sdk: https://github.com/pfalcon/esp-open-sdk

* NodeMCU: https://github.com/nodemcu/nodemcu-firmware

* esp-open-rtos: https://github.com/SuperHouse/esp-open-rtos

* ESP8266-RTOS-SDK: https://github.com/espressif/ESP8266_RTOS_SDK

* FreeRTOS: http://www.freertos.org/

* esptool: https://github.com/themadinventor/esptool

* GDBSTUB: https://github.com/espressif/esp-gdbstub

* Unofficial Development Kit for Espressif ESP8266: http://programs74.ru/udkew-en.html

* lua: www.lua.org

* elua: www.eluaproject.net

* WifiMCU: www.wifimcu.com/en.html
