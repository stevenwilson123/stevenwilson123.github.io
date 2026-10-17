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

OpenOCD is also available as part of the RPi Pico tools [https://github.com/raspberrypi/pico-sdk-tools/releases](https://github.com/raspberrypi/pico-sdk-tools/releases)

When it is run, OpenOCD presents a GDB-like interface on port 3333 (by default).  You can then connect to this interface using a GDB client.
We will be using the version of GDB that came with the ARM GCC toolchain that we installed earlier.  This is called `arm-none-eabi-gdb.exe` and it is located in the `bin` folder of your ARM GCC installation.

VS2026 know how to talk to GDB, so we can use it to control the Pico via OpenOCD.

Here is a digram showing how all the pieces fit together:

![](/assets/images/debugging_block_diagram.svg)


## Launch Configuration (launch.vs.json)
The final piece of the puzzle is to tell VS2026 how to talk to GDB, and by extension OpenOCD and the Pico.  
This is done by creating a "launch configuration" in VS2026.  
This is done by adding a `launch.vs.json` file to the root of your project folder.

{: .note }
> Please note the difference between `launch.vs.json` and `launch.json`.  
> The former is used by VS2026, while the latter is used by VSCode.  
> Although they may have similar structures, they are NOT compatible with each other.

An example of a `launch.vs.json` file is shown below (the full file is available here: [launch.vs.json](/assets/files/vs2026_rpi_pico_launch.json))

```json
{
  "version": "0.2.1",
  "defaults": {},
  "configurations": [
    {
      "type": "cppgdb",
      "project": "CMakeLists.txt",
      "projectTarget": "wifi_test.elf (src\\main\\wifi_test.elf)",
      "name": "wifi_test.elf (OpenOCD)",
      "program": "${cmake.binaryDir}/src/main/wifi_test.elf",
      "isDefaultConfiguration": true,
      "MIMode": "gdb",
      "miDebuggerPath": "arm-none-eabi-gdb.exe",
      "miDebuggerServerAddress": "localhost:3333",
      "useExtendedRemote": true,
      "debugServerPath": "openocd.exe",
      "debugServerArgs": "-d2 -f interface/cmsis-dap.cfg -f target/${cmake.OPENOCD_TARGET}.cfg -c \"adapter speed 5000\" ",
      "serverLaunchTimeout": 5000,
      "filterStderr": true,
      "svdPath": "${env.PICO_SDK_PATH}/src/${cmake.OPENOCD_TARGET}/hardware_regs/${cmake.OPENOCD_TARGET}.svd",
      "showDevDebugOutput": "raw",
      "postRemoteConnectCommands": [
        {
          "text": "-target-download",
          "ignoreFailures": false
        },
        {
          "text": "-interpreter-exec console \"monitor reset init\"",
          "ignoreFailures": false
        },
        {
          "text": "-interpreter-exec console \"continue\"",
          "ignoreFailures": false
        }
      ]
    }
  ]
}
```

There is a lot going on in this file, so let's break it down into its component parts 

{: .note }
> A full description of the launch.vs.json file format can be found here: [https://learn.microsoft.com/en-us/cpp/build/launch-vs-schema-reference-cpp?view=msvc-170](https://learn.microsoft.com/en-us/cpp/build/launch-vs-schema-reference-cpp?view=msvc-170))


In order to load debug symbols, we need to tell VS2026 where the ELF file is located.  
This is done with the `program` property.  
In this case, the ELF file is located in the `src/main` folder of the build output directory, and is called `wifi_test.elf`.
I make use of the `${cmake.binaryDir}` variable to refer to the build output directory, which is set by CMake.

The `miDebuggerPath` property tells VS2026 where to find the GDB executable.  
In this case, it is located in the `bin` folder of the ARM GCC toolchain, which we have already added to our PATH environment variable.

The `miDebuggerServerAddress` property tells VS2026 where to find the OpenOCD server.

The `debugServerPath` property tells VS2026 where to find the OpenOCD executable (we also have this added to our PATH environment variable).

The `debugServerArgs` property tells VS2026 what arguments to pass to OpenOCD when it is launched.
The main thing to note here are the two `-f` arguments.  These contain the configuration files that tell OpenOCD how to talk to the debug adapter (the `interface/cmsis-dap.cfg` file), and how talk to the target (the `target/${cmake.OPENOCD_TARGET}.cfg` file).
The files that are passed to OpenOCD are located in its `share\openocd\scripts` folder.
The `${cmake.OPENOCD_TARGET}` variable is set in my [CMakePresets.json](/assets/files/vs2026_rpi_pico_cmakepresets.json) file, and is set to `rp2040` for the Pico 1, and `rp2350` for the Pico 2.

The `postRemoteConnectCommands` property contains a list of commands that are sent to GDB after it connects to OpenOCD.
These commands instructt GDB to download the program to the Pico, reset it, and then start running it.



## Start Debugging
When we start debugging in VS2026 (by pressing F5, or selecting "Start Debugging" from the menu), it will begin the following sequence of events:
1. Load the debug symbols from the ELF file into GDB:
![](/assets/images/vs2026_gdb_loading_symbols.png)

2. Launch OpenOCD, which will connect to the debug adapter and the Pico.  It does this as a background process, but you can see the output in the "Debug Console" window:
![](/assets/images/vs2026_openocd_output.png)

3. Connect GDB to OpenOCD:
![](/assets/images/vs2026_gdb_loading_symbols.png)

4. Download the program to the Pico, reset it, and start running it:
![](/assets/images/vs2026_target_download.png)

5. Reset the Pico and start running the program:
![](/assets/images/vs2026_gdb_monitor_reset.png)

{: .note }
> The whole process takes about a minute or so, depending on how fast your debug adapter is, how big your ELF file is, etc.



Once the program is running, you can pause execution, set breakpoints, step through the code, and inspect variables and memory just like you would in a normal desktop application:
![](/assets/images/vs2026_debugging_in_progress.png)







