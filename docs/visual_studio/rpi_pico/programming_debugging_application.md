---
layout: default
parent: Raspberry Pi Pico
title: Programming/Debugging your RPi Pico application via VS2026
nav_order: 5
---

# Programming/Debugging your RPi Pico application via VS2026
In a normal desktop application, after you successfully build your application, you can run it directly from VS2026.  This is because the desktop application can run on the same machine that it was built on.
However, the RPi Pico is a completely seperate piece of hardware.  So how do you get your application onto the Pico and run it?

The Pico already supports a "drag-and-drop" programming model.  When you plug the Pico into your PC, it shows up as a USB mass storage device.  You can copy your application binary (a .uf2 file) onto the Pico, and it will automatically reprogram itself with your application.
But this is not very convenient if you are doing a lot of development/debugging, as you have to keep unplugging the Pico, copying the file, and plugging it back in.

Luckily there is another solution...

## The SWD Debug Pins
If you take a look at your Pico, apart from all the pins along the edge, there are also some special pins that are used for debugging.  These are the SWD (Serial Wire Debug) pins, and they allow you to connect a debugger to the Pico and control it directly.

![](/assets/images/rpi_pico_2w_debug_port.png)

{: .note }
> The location of these debug pins varies slightly between the Pico 1 and Pico 2 and their "W" (wireless) variants.


Raspberry Pi have an official debug adapter called the "Raspberry Pi Pico Debug Probe".  You can also use other SWD debuggers, such as the Segger J-Link.
I bought a cheap clone of a CMSIS/DAP debugger off eBay for about £5, and it works perfectly well.

![](/assets/images/rpi_pico_debug_box.png)

The following is a diagram of the pinouts on my debug adapter:

![](/assets/images/debug_adapter_pinouts.svg)

At a minimum, you will need to connect the following pins:

| Pico SWD | Debug Adapter | 
|:---------|:--------------|
| SWDIO    | TMS/IO        |
| SWCLK    | TCK/CLK       |
| GND      | GND           |

The debug adapter then connects to your PC via USB.  It will show up as a COM port.


## OpenOCD
VS2026 doesn't know how to directly talk to the debug adapter and/or the RPi Pico.  This is where another piece of software comes in - **OpenOCD**.  
OpenOCD is an open-source tool that can talk to a variety of debug adapters and microcontrollers, including the RPi Pico.

The official OpenOCD website is here: [http://openocd.org/](http://openocd.org/)
Its github repo is here: [https://github.com/openocd-org/openocd](https://github.com/openocd-org/openocd)

OpenOCD is also available as part of the RPi Pico tools https://github.com/raspberrypi/pico-sdk-tools/releases

When it is run, OpenOCD presents a GDB-like interface on port 3333 (by default).  You can then connect to this interface using a GDB client.
We will be using the version of GDB that came with the ARM GCC toolchain that we installed earlier.  This is called `arm-none-eabi-gdb.exe` and it is located in the `bin` folder of your ARM GCC installation.

VS2026 know how to talk to GDB, so we can use it to control the Pico via OpenOCD.

Here is a digram showing how all the pieces fit together:

![](/assets/images/debugging_block_diagram.svg)


