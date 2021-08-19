# Huawei LiteOS - WeAct BluePill Plus

This is a build of Huawei LiteOS 5.1.0 for GD32F303 on [WeAct BluePill Plus](https://github.com/weacttc/bluepill-plus) . The demo does the following:

- the led blinks
- the Huawei LiteOS console is on serial0 (PA9/PA10)
- the usb cdc serial port echoes back all characters 
- the usb hid device moves the mouse in small circles

## Installation

Download pre-built binaries from [releases](../../releases), boot in DFU mode, connect a usb-serial adapter to pin PA9/PA10 and install using

```
stm32flash -w Huawei_LiteOS.hex /dev/ttyUSB0
```

## Developer notes

Patches are relative to  [Huawei Liteos](https://gitee.com/LiteOS/LiteOS)

Do a ``make menuconfig``, set *target family* to *GD*, *target* to *GD32F303RGT6_BearPi*. 

```
(Top) → Targets
                          Huawei LiteOS Configuration
    Family (GD)  --->
    Target (GD32F303RGT6_BearPi)  --->
[ ] Qspi peripherals
[*] Enable Floating Pointer Unit
[*] Enable Access Permission Control
[ ] Enable Flash Patch
```

### Makefile
Create binaries in intel hex format.

[Makefile.patch](Makefile.patch)

### link script
The linker script is incomplete. This patch is needed to link.

[GD32F303RGT6_BearPi.patch](GD32F303RGT6_BearPi.patch)

### WeAct BluePill-Plus

GD32F303RGT6_BearPi has a LED on pin PB0, WeAct BluePill Plus has a LED on pin PB2. This patch puts the led on pin PB2. Apply this patch if compiling for WeAct BluePill Plus. Skip this patch if compiling for GD32F303RGT6_BearPi.

[WeActBluePillPlus.patch](WeActBluePillPlus.patch)

### usb device

The GD32F303 usb hardware seems register-compatible with STM32F103. The usb stack used is [libusb_stm32](https://github.com/dmitrystu/libusb_stm32), configuration is same as for STM32F103.

## Compiling

Huawei LiteOS 5.1.0 was compiled using gcc-9.4.0 and [gcc-arm-none-eabi-9-2020-q2-update](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads/9-2020-q2-update):

```
export PATH=/opt/xpack-arm-none-eabi-gcc-9.3.1-1.1/bin/:/opt/gcc-9.4.0/bin/:$PATH
make GCCLIB_PATH_32=/opt/xpack-arm-none-eabi-gcc-9.3.1-1.1/lib/gcc/arm-none-eabi/9.3.1/ CXXLIB_PATH_32=/opt/xpack-arm-none-eabi-gcc-9.3.1-1.1/arm-none-eabi/lib/
```

## Firmware
Download firmware with a debugger or using a usb-serial converter.

To download via the serial port, connect a usb serial to the WeAct BluePill Plus:

- RXD to pin A9
- TXD to pin A10

*Do not* provide power via the USB connector and the +5V pin at the same time, or you risk short-circuit.

Boot in DFU mode:

- press BOOT0 button
- press RESET
- release RESET
- release BOOT0 

Download the intel hex file via DFU. On linux:

``stm32flash -w out/GD32F303RGT6_BearPi/Huawei_LiteOS.hex /dev/ttyUSB0``

Reboot and you should have the LiteOS prompt on the serial port.

```
********Hello Huawei LiteOS********

LiteOS Kernel Version : 5.1.0
build data : Aug 17 2021 10:35:22

**********************************
OsAppInit
cpu 0 entering scheduler
app init!
Hello, welcome to liteos demo!

Huawei LiteOS # 
```

not truncated
