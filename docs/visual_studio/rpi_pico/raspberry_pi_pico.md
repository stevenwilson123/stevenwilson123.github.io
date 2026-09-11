---
layout: default
title: Raspberry Pi Pico SDK with VS2026
parent: Raspberry Pi Pico
nav_order: 3
---

# Building the Raspberry Pi Pico SDK with VS2026

## Download and/or build the dependency binaries

{: .important }
> The Pico SDK expects some executables to be available.
> These can either be built seperately, or pre-built binaries can
> be downloaded from https://github.com/raspberrypi/pico-sdk-tools


## Download and build PICOTOOL
TODO
TODO
TODO

## Download the Pico SDK
Clone the github repo - https://github.com/raspberrypi/pico-sdk

`git clone https://github.com/raspberrypi/pico-sdk.git`

<br>

## Download the GCC toolchain
The latest ARM GCC toolchain can be downloaded from the official ARM website: https://gitlab.arm.com/tooling/gnu-toolchains-for-arm

{: .note }
> The actual download links are hidden behind a collapsible menu (which doesn't look clickable!)
>
> You will need to select the appropriate version for your operating system and architecture (see image below)

![](../../assets/images/gitlab_arm_gcc_download.png)
 

<br>

## Set up environment variables
Set up the following environment variables:

| Variable			  | Value             | Example                                           |
|:--------------------|:------------------|:--------------------------------------------------|
| PICO_SDK_PATH       | path/to/pico-sdk  | C:\Users\Steven\source\repos\pico-sdk             |
| PICO_TOOLCHAIN_PATH | path/to/toolchain | C:\tools\gnu_arm_embedded_toolchain\12.3_rel1\bin |
| PICO_COMPILER       | pico_arm_gcc      | pico_arm_gcc                                      |

<br>

## Open the Pico SDK in VS2026
Open the `pico-sdk` folder in VS2026.  You should see the following in the Solution Explorer:

![](../../assets/images/vs2026_pico_sdk_folder_view.png)





## Add the CMakePresets.json file

Shortly after you open the `pico-sdk` folder in VS2026, it will try to configure the project.  
**THIS WILL FAIL** as it will try to use the default MSVC compiler, which is not compatible with the Pico SDK.

To get around this, we need to add a `CMakePresets.json` file to the root of the `pico-sdk` folder.  
This file will tell CMake to use the ARM GCC toolchain instead of the default MSVC compiler.

Here is the file:
<details markdown="1">
<summary class="summary-highlight">PICO SDK CMakePresets.json</summary>

```json
{
    "version": 3,
    "configurePresets": [
        {
            "name": "pico1_gcc_platform",
            "hidden": true,
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "toolchainFile": "$env{PICO_SDK_PATH}/cmake/preload/toolchains/pico_arm_cortex_m0plus_gcc.cmake",
            "cacheVariables": {
                "PICO_PLATFORM": "rp2040"
            }
        },
        {
            "name": "pico1_clang_platform",
            "hidden": true,
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "toolchainFile": "$env{PICO_SDK_PATH}/cmake/preload/toolchains/pico_arm_cortex_m0plus_clang.cmake",
            "cacheVariables": {
                "PICO_PLATFORM": "rp2040"
            }
        },
        {
            "name": "pico2_gcc_platform",
            "hidden": true,
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "toolchainFile": "$env{PICO_SDK_PATH}/cmake/preload/toolchains/pico_arm_cortex_m33_gcc.cmake",
            "cacheVariables": {
                "PICO_PLATFORM": "rp2350"
            }
        },
        {
            "name": "pico2_clang_platform",
            "hidden": true,
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "toolchainFile": "$env{PICO_SDK_PATH}/cmake/preload/toolchains/pico_arm_cortex_m33_clang.cmake",
            "cacheVariables": {
                "PICO_PLATFORM": "rp2350"
            }
        },
        {
            "name": "pico1_board",
            "hidden": true,
            "cacheVariables": {
                "PICO_BOARD": "pico"
            }
        },
        {
            "name": "pico1w_board",
            "hidden": true,
            "cacheVariables": {
                "PICO_BOARD": "pico_w"
            }
        },
        {
            "name": "pico2_board",
            "hidden": true,
            "cacheVariables": {
                "PICO_BOARD": "pico2"
            }
        },
        {
            "name": "pico2w_board",
            "hidden": true,
            "cacheVariables": {
                "PICO_BOARD": "pico2_w"
            }
        },

        {
            "name": "pico1_gcc_debug",
            "displayName": "Pico1 GCC Debug",
            "inherits": [
                "pico1_gcc_platform",
                "pico1_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "pico1_gcc_release",
            "displayName": "Pico1 GCC Release",
            "inherits": [
                "pico1_gcc_platform",
                "pico1_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "RelWithDebInfo"
            }
        },
        {
            "name": "pico1w_gcc_debug",
            "displayName": "Pico1W GCC Debug",
            "inherits": [
                "pico1_gcc_platform",
                "pico1w_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "pico1w_gcc_release",
            "displayName": "Pico1W GCC Release",
            "inherits": [
                "pico1_gcc_platform",
                "pico1w_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "RelWithDebInfo"
            }
        },

        {
            "name": "pico2_gcc_debug",
            "displayName": "Pico2 GCC Debug",
            "inherits": [
                "pico2_gcc_platform",
                "pico2_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "pico2_gcc_release",
            "displayName": "Pico2 GCC Release",
            "inherits": [
                "pico2_gcc_platform",
                "pico2_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "RelWithDebInfo"
            }
        },
        {
            "name": "pico2w_gcc_debug",
            "displayName": "Pico2W GCC Debug",
            "inherits": [
                "pico2_gcc_platform",
                "pico2w_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "pico2w_gcc_release",
            "displayName": "Pico2W GCC Release",
            "inherits": [
                "pico2_gcc_platform",
                "pico2w_board"
            ],
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "RelWithDebInfo"
            }
        }
    ]
}
```

</details>


There are a few things to note about this file:
1. There are several hidden presets:
  * `platforms` define the processor type (RP2040 or RP2350), and the toolchain to use (GCC or Clang)
    * PICO_PLATFORM is set to either `rp2040` or `rp2350`.  These are reserved names in the Pico SDK, and are used to select the appropriate processor type.
    * `toolchainFile` points to the appropriate cmake toolchain file from the SDK
  * `boards` define the board type (Pico, Pico W, Pico 2, or Pico 2 W)
    * PICO_BOARD is set to the appropriate board name.  These are reserved names in the Pico SDK, and are used to select the appropriate board type.

2. The visible presets then inherit from the above hidden presets, to produce valid combinations of a `platform`, `board`, along with specifying CMAKE_BUILD_TYPE to choose either a Debug or Release build.

This should result in you having the following in the `Configuration` pulldown in VS2026:

![](../../assets/images/vs2026_pico_sdk_cmake_config_pulldown.png)