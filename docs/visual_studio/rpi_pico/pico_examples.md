---
layout: default
parent: Raspberry Pi Pico
title: Raspberry Pi Pico Examples With VS2026
nav_order: 3
---

# Building the Raspberry Pi Pico Examples with VS2026

## 1. Install a toolchain
The Raspberry Pi Pico needs a target compiler for the RP2040/RP2350 processor.

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


## 2. Install the Pico SDK
See instructions [here](./pico_sdk.md)



## 3. Clone the examples repo

```
git clone https://github.com/raspberrypi/pico-examples.git
```


## 4. Add the CMakePresets.json

To tell VS2026 to use the Pico SDK and the Pico toolchain, you need to add a `CMakePresets.json` file to the root of the `pico-examples` repo.
You can use this file as a starting point: [CMakePresets.json](/assets/files/pico_examples_cmakepresets.json)



## 5. Open the pico-examples project in VS2026

If you have your CMakePresets.json in place, you should be able to open the `pico-examples` project in VS2026 and build it without any issues.
Choose the correct preset from the **Configuration** pull-down menu in the toolbar, and then click the **Build** button.


{: .warning }
> During the build, you make see some warnings pop out regarding line length.  These are due to a Windows limitation around maximum path length

