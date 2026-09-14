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