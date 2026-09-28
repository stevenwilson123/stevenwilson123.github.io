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


Raspberry Pi have an official debugger called the "Raspberry Pi Pico Debug Probe".  You can also use other SWD debuggers, such as the Segger J-Link.
I bought a cheap clone of a CMSIS/DAP debugger off eBay for about $10, and it works perfectly well.

![](/assets/images/rpi_pico_debug_box.png)




