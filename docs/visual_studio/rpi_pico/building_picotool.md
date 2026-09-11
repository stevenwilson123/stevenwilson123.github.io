---
layout: default
parent: Raspberry Pi Pico
title: Building Picotool
nav_order: 1
---

# Building Picotool

Picotool is one of the little executables that the Pico SDK makes use.  
It is used to do signing/hashing of binaries produced during builds.
Since it will be invoked as part of the SDK and/or application build, the executable has to available and ready to run.


{: .important }
> If you don't want to build your own, a prebuilt verion of the Picotool executable can be downloaded from https://github.com/raspberrypi/pico-sdk-tools


## 1. Clone the Picotool repo

```
git clone https://github.com/raspberrypi/picotool.git
```


## 2. Clone the Pico SDK repo
The Picotool utilises the **mbedtls** submodule of the Pico SDK for signing/hashing algorithms, so it needs to be available too

```
git clone --recursive https://github.com/raspberrypi/pico-sdk.git
```

{: .important }
> NOTE the `--recursive` flag in the above command to ensure submodules are also cloned

You also need to set up an environment variable (**PICO_SDK_PATH**) to point to the location of the Pico SDK.




## 2. Install dependency packages via VCPKG

According to the official build instructions (https://github.com/raspberrypi/picotool/blob/master/BUILDING.md),
Picotool depends on some 3rd-party packages.  These can be installed via VCPKG:

```
vcpkg install pkgconf
vcpkg install libusb
```



## 3. Open the picotool folder in VS2026
Use **File->Open->Folder** to open the picotool folder.

Shortly after it opens, VS2026 should detect the presence of a CMakeList.txt directory

Since there are no CMakePresets.json (or CMakeSettings.json), the defalt build configuration
is **x64 Debug**.
If you prefer **x64 Release**, go to the Configuration pull-down menu and select "**Manage Configurations**"

Now do a build - **Build Menu --> Rebuild All**



