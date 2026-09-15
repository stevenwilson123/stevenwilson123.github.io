---
layout: default
parent: Raspberry Pi Pico
title: Raspberry Pi Pico SDK with VS2026
nav_order: 2
---

# Building the Raspberry Pi Pico SDK with VS2026

## Download and/or build the dependency binaries
The Pico SDK requires a version of `picotool` to be available in your PATH.  
You can either:
* Download a pre-built binary from the [pico-sdk-tools repo](https://github.com/raspberrypi/pico-sdk-tools/releases)
* Build it yourself from source - instructions are available [here](/docs/visual_studio/rpi_pico/picotool_build.md)




## Install 3rd party dependencies

The Pico SDK also depends on some 3rd party libraries, which can be installed via **vcpkg**.  If you don't have vcpkg installed, you can follow the instructions [here](/docs/visual_studio/vcpkg.md)

```
vcpkg install python3
```



## Download the Pico SDK
Clone the github repo - https://github.com/raspberrypi/pico-sdk

`git clone https://github.com/raspberrypi/pico-sdk.git`

<br>


## Download the GCC toolchain
I'm using the latest ARM GCC toolchain, which can be downloaded from the official ARM [website](https://gitlab.arm.com/tooling/gnu-toolchains-for-arm)

{: .note }
> The actual download links are hidden behind a collapsible menu (which doesn't look clickable!)
>
> You will need to select the appropriate version for your operating system and architecture (see image below)

![](/assets/images/gitlab_arm_gcc_download.png)
 

{: .note }
> If you are using the Pico 2, the RP2350 processor has BOTH dual ARM cores and dual RISC-V cores
> so you can also use the RISC-V GCC toolchain.
> NOTE - you can only use either the ARM cores OR the RISC-V cores, not both at the same time. 
The latest RISC-V GCC toolchain can be downloaded from the official RISC-V website, or there is a pre-built binary available from the [pico-sdk-tools repo](https://github.com/raspberrypi/pico-sdk-tools/releases)

<br>

## Set up environment variables
Set up the following environment variables:

| Variable			        | Value             | Example                                                           |
|:--------------------------|:------------------|:------------------------------------------------------------------|
| PICO_SDK_PATH             | path/to/pico-sdk  | C:\Users\Steven\source\repos\pico-sdk                             |
| PICO_TOOLCHAIN_PATH       | path/to/toolchain | C:\tools\arm-gnu-toolchain-14.2.rel1-mingw-w64-i686-arm-none-eabi |

{: .note }
> You can choose one or both of the toolchains, depending on which processor cores you are targeting.
> Remember - Pico 1 only supports the ARM cores, while Pico 2 supports both ARM and RISC-V cores.
>
> If you want to build **universal** binaries, you will need **both** the ARM and RISC-V toolchains, and you need seperate environvent variables:
>
> | Variable			      | Value             | Example                                                           |
> |:--------------------------|:------------------|:------------------------------------------------------------------|
> | PICO_ARM_TOOLCHAIN_PATH   | path/to/toolchain | C:\tools\arm-gnu-toolchain-14.2.rel1-mingw-w64-i686-arm-none-eabi |
> | PICO_RISCV_TOOLCHAIN_PATH | path/to/toolchain | C:\tools\riscv_gcc_toolchain\12.3_rel1                            |

<br>

## Open the Pico SDK in VS2026
Open the `pico-sdk` folder in VS2026.  You should see the following in the Solution Explorer:

![](assets/images/vs2026_pico_sdk_folder_view.png)





## Add the CMakePresets.json file

Shortly after you open the `pico-sdk` folder in VS2026, it will try to configure the project.  
**THIS WILL FAIL** as it will try to use the default MSVC compiler, which is not compatible with the Pico SDK.

To get around this, we need to add a `CMakePresets.json` file to the root of the `pico-sdk` folder.  
This file will tell CMake to use the ARM GCC toolchain instead of the default MSVC compiler.

Here is the link to the [CMakePresets.json](/assets/files/vs2026_rpi_pico_cmakepresets.json) file that I created for this purpose: 

There are a few things to note about this file:
1. There are several hidden presets:
  * `platforms` define the processor type (RP2040 or RP2350), and the toolchain to use (GCC or Clang)
    * PICO_PLATFORM is set to either `rp2040` or `rp2350`.  These are reserved names in the Pico SDK, and are used to select the appropriate processor type.
    * `toolchainFile` points to the appropriate cmake toolchain file from the SDK
  * `boards` define the board type (Pico, Pico W, Pico 2, or Pico 2 W)
    * PICO_BOARD is set to the appropriate board name.  These are reserved names in the Pico SDK, and are used to select the appropriate board type.

2. The visible presets then inherit from the above hidden presets, to produce valid combinations of a `platform`, `board`, along with specifying CMAKE_BUILD_TYPE to choose either a Debug or Release build.

This should result in you having the following in the `Configuration` pulldown in VS2026:

![](/assets/images/vs2026_pico_sdk_cmake_config_pulldown.png)