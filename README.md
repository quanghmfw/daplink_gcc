# OpenOCD Tutorial
 
## install deps

sudo apt-get install build-essential

sudo apt-get install autotools-dev autoconf automake libtool

sudo apt-get install texinfo libudev-dev libusb-1.0-0-dev libfox-1.6-dev


## Download FTDI driver

```
http://www.intra2net.com/en/developer/libftdi/download.php
tar xf libftdi1-1.2.tar.bz2
```

## HIDAPI 

```
git clone git://github.com/signal11/hidapi.git
./bootstrap
./configure
make
sudo make install
```

## Build and Install OpenOCD

```
git clone git://git.code.sf.net/p/openocd/code
cd code
./bootstrap
cd ../
mkdir ./openocd-build
cd openocd-build
../code/configure --with-ftd2xx-linux-tardir=../libftdi1-1.2 --prefix=/opt/openocd --enable-jlink --enable-ftdi --enable-stlink --enable-ti-icdi --enable-ulink --enable-usb-blaster-2 --enable-cmsis-dap --enable-bcm2835gpio
make
make install
```
## Usage

### Prepare extra OpenOCD cfg commands

create file openocd.cfg with following contents:

```
$_TARGETNAME configure -event gdb-attach {
   echo "Halting target"
   halt
}
```

### Start OpenOCD server to Debug LPC11U35

```
sudo openocd -f interface/jlink.cfg -c "transport select swd" -f target/lpc11xx.cfg -f openocd.cfg 

```

### Flash and Debug

- First, flash debug firmware on board

```
telnet localhost 4444
reset halt
flash probe 0
flash write_image erase ~/Firmware/DAPLINK/Debug/DAPLINK.bin 0x0
reset
exit
```

- Second, debug using the commands:

```
target remote localhost:3333
break DAPLINK.c:103
monitor reset halt
monitor reset init
continue
```
http://wiki.paparazziuav.org/wiki/DevGuide/GDB_OpenOCD_Debug

### Breakpoint limits

```
set remote hardware-breakpoint-limit 4
set remote hardware-watchpoint-limit 2
```

### gdbtui GUI debugging

http://fun-tech.se/stm32/OpenOCD/gdb.php


## OpenOCD nRF51822 using CMSIS-DAP

Launch OpenOCD

```
$ sudo openocd -f interface/cmsis-dap.cfg -c "transport select swd" -f target/nrf51.cfg
```

nrf OpenOCD log

```
users@pc:~$ 
users@pc:~$ sudo openocd -f interface/cmsis-dap.cfg -c "transport select swd" -f target/nrf51.cfg
[sudo] password for yliu: 
Open On-Chip Debugger 0.10.0-dev-00001-g70a14db (2015-06-23-16:35)
Licensed under GNU GPL v2
For bug reports, read
	http://openocd.org/doc/doxygen/bugs.html
Info : only one transport option; autoselect 'swd'
Warn : Transport "swd" was already selected
swd
cortex_m reset_config sysresetreq
adapter speed: 1000 kHz
Info : CMSIS-DAP: SWD  Supported
Info : CMSIS-DAP: JTAG Supported
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : CMSIS-DAP: FW Version = 1.0
Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 1 TDO = 1 nTRST = 0 nRESET = 1
Info : CMSIS-DAP: Interface ready
Info : clock speed 1000 kHz
Info : SWD IDCODE 0x0bb11477
Info : nrf51.cpu: hardware has 4 breakpoints, 2 watchpoints
Info : accepting 'telnet' connection on tcp/4444
```


```
users@pc:~$ telnet localhost 4444
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Open On-Chip Debugger
> reset halt
target state: halted
target halted due to debug-request, current mode: Thread 
xPSR: 0xc1000000 pc: 0x000006d0 msp: 0x000007c0
> flash probe 0
Unknown device (HWID 0x00000057)
flash 'nrf51' found at 0x00000000
```
 
