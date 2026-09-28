---
layout: default
parent: Raspberry Pi Pico
title: Building a custom Raspberry Pi Pico application with VS2026
nav_order: 4
---

# Building a custom Raspberry Pi Pico application with VS2026

To build a custom application for the Raspberry Pi Pico, you need to have the Pico SDK and the Pico toolchain installed.  See the instructions [here](./pico_sdk.md) for details.
Once these are in place, you simply need to set up a `CMakeLists.txt` file, and add a `CMakePresets.json` file to the root of the folder.  You can use this file as a starting point: [CMakePresets.json](/assets/files/vs2026_rpi_pico_cmakepresets.json)

The `CMakePresets.json` is needed so VS2026 knows what toolchain you want to use.


The top-level `CMakeLists.txt` file needs to contain a few lines to pull in the Pico SDK and set up the build environment.  

The first few lines look like this:

```cmake
# Pull in SDK (must be done BEFORE project)
include(cmake/pico_sdk_import.cmake)
include(cmake/pico_extras_import_optional.cmake)

project(test_app C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

if (PICO_SDK_VERSION_STRING VERSION_LESS "1.3.0")
    message(FATAL_ERROR "Raspberry Pi Pico SDK version 1.3.0 (or later) required. Your version is ${PICO_SDK_VERSION_STRING}")
endif()


# Initialize the SDK
pico_sdk_init()
```

After that you can add your own source files and include directories, and then create the executable.  For example, if you have a single source file called `main.cpp`, you would add the following lines to the `CMakeLists.txt` file:
```cmake
add_executable(wifi_test "main.cpp")


# pull in common dependencies
target_link_libraries(wifi_test pico_stdlib) # for core functionality

target_link_options(wifi_test PUBLIC -Wl,-gc-sections,--print-memory-usage)


pico_enable_stdio_usb(wifi_test 1)
pico_enable_stdio_uart(wifi_test 0)

# create map/bin/hex file etc.
pico_add_extra_outputs(wifi_test)
```


## Linker options
We are linking `pico_stdlib`, which is a library that provides core functionality for the Pico, including access to the GPIO pins, UART, and other peripherals.
Depending on your application, you may need to link other libraries from the SDK as well.  See the Pico SDK documentation for more details.

We are also adding some linker options to the executable, which will remove unused sections of code and print out a memory usage report after the build:

![](assets/images/vs2026_rpi_pico_app_build_output.png)

This is useful for showing just how much memory your application is using, and how much is left over for other things.




## STDIO
Like a normal desktop application, a Pico application has access to stdin/stdout (and presumably stderr), and all the functions that go with it, such as `printf()`, `scanf()`, etc.  
However, since the Pico has no screen or keyboard, STDIO typically goes via a UART.

The Pico has a few different options for controlling where STDIO is connected to.  
The most common is to use the USB port, which is what the `pico_enable_stdio_usb()` function does.  This will allow you to use a terminal program on your PC to communicate with the Pico via USB.

You can choose instead to use one of the onboard UART peripherals (conneted to pins on the edges of the Pico) by calling `pico_enable_stdio_uart()`.  
Since the pins output TTL levels, you will need a USB-to-TTL adapter to connect the Pico to your PC.  You can also use a logic analyzer or oscilloscope to monitor the UART signals.



## MAP file
To assist with debugging, I also call `pico_add_extra_outputs` which will create a `.map` file in the build output folder.  
This file contains a memory map of the application, and can be useful for debugging memory issues.