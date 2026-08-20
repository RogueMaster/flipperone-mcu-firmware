<!-- See COPYING.txt for license details. -->
<h1 align="center"><a href='https://rogue-master.net'><img src="https://raw.githubusercontent.com/RogueMaster/flipperzero-firmware-wPlugins/420/.github/assets/rmlogo.png" width="40%"></a>
<br><a href='https://discord.gg/gF2bBUzAFe' target='_blank'><img src="https://raw.githubusercontent.com/RogueMaster/flipperzero-firmware-wPlugins/420/.github/assets/Discord.png" alt='Discord' title='Discord'></a>
&nbsp;<a href='https://github.com/RogueMaster/flipperzero-firmware-wPlugins/releases/latest' target='_blank'><img src="https://raw.githubusercontent.com/RogueMaster/flipperzero-firmware-wPlugins/420/.github/assets/Github.png"  alt='Firmware GitHub' title='Firmware GitHub'></a>
&nbsp;<a href='https://www.patreon.com/RogueMaster?filters[tag]=Latest%20Release' target='_blank'><img src="https://raw.githubusercontent.com/RogueMaster/flipperzero-firmware-wPlugins/420/.github/assets/Patreon.png"  alt='Latest PATREON Release' title='Latest PATREON Release'></a>
&nbsp;<a href='https://github.com/RogueMaster/awesome-flipperzero-withModules' target='_blank'><img src="https://raw.githubusercontent.com/RogueMaster/flipperzero-firmware-wPlugins/420/.github/assets/Resources.png"  alt='More Research / Assets' title='More Research / Assets'></a></h1>

## Thank you so much to our RM Pro/SUPER Patreon supporters! Because of people like you, we are able to offer the best and most up-to-date Flipper Zero Firmware!

<table><tr><th><div align="left">
This firmware is a fork of all Flipper One community projects! We are NOT paywalled. I will keep RM Custom Firmware the most cutting-edge with active development and updates from all projects that can be found to be useful to the community. Although there are features exclusive to this firmware, there are also many updates by the rest of the Flipper Zero / One community. With the help of the entire community, we get the best firmware! See the RM <a href='https://discord.gg/gF2bBUzAFe' target="_blank">Discord</a> - <a href='https://www.patreon.com/RogueMaster' target="_blank">Patreon</a>
</div></th><th style='width:50%;'>
This software is for experimental purposes only and is not meant for any illegal activity/purposes. We do not condone illegal activity and strongly encourage keeping transmissions to legal/valid uses allowed by law.
</th></tr></table>

> **This is a community project and is not affiliated with or endorsed by Flipper Devices.**

# Flipper One MCU Firmware

[![MemBrowse](https://membrowse.com/badge.svg)](https://membrowse.com/public/flipperdevices/flipperone-mcu-firmware)

This repository is part of [Flipper One MCU Firmware](https://github.com/orgs/flipperdevices/projects/8) sub-project and contains issue tracking and firmware sources for the Flipper One MCU — the low-power co-processor that controls the LCD, buttons, and battery.

<img width="1474" height="450" alt="Flipper One MCU and CPI interconnection" src="https://github.com/user-attachments/assets/67d4810f-38b0-49af-8321-11bbc84ed04d" />

### Flipper One uses a dual-processor architecture:

* **Low-Power MCU** (Raspberry Pi RP2350)  
  Buttons, LCD display, touchpad, and LEDs are physically connected to the MCU. It also manages battery and power control.  
  To render graphics on the LCD from Linux, the main CPU transfers display data to the MCU over SPI.  
  When the device is powered off, the MCU controls power-bank mode and system power states.  
  The MCU also participates in booting the main CPU.

* **High-Performance Linux CPU** (Rockchip RK3576)  
  This processor runs Linux, and all high-level peripherals are connected to it: USB, HDMI, M.2, Wi-Fi, Ethernet, and audio.

The MCU and CPU are interconnected via several interfaces: SPI, I²C, and UART. Additional GPIO lines are used for BOOT_0, BOOT_1, and IRQ signals.

## Automated builds
Builds run automatically on every push to the `dev` branch, on tag pushes, and on pull requests. PR builds are linked from a bot comment on the pull request.

### [`📥 Download latest dev firmware →`](https://update.flipperzero.one/builds/flipper-one-mcu/dev/)
**⚠️ TODO:** make a proper build server address and folder structure instead of using `flipperzero.one`

## Manual build 

**⚠️ TODO:** how to build manually? 

## Join development

* Check the public task tracker: [MCU Firmware Project](https://github.com/orgs/flipperdevices/projects/8)

* Read the documentation: [docs.flipper.net/one/tech-specs](https://docs.flipper.net/one/tech-specs)
  ⚠️ *Co-processor architecture documentation is coming soon (TODO).*

## How to build

Install [VSCode](https://code.visualstudio.com/) with the [Raspberry Pi Pico extension](https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico). The extension automatically downloads the ARM toolchain, CMake, Ninja, and Pico SDK. Open the project folder and use the extension's compile button.

<details>
<summary>Manual build (Linux / macOS)</summary>

Prerequisites: [ARM GCC toolchain](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases) (tested with 14.2.1), CMake 3.13+, [Pico SDK 2.2.0](https://github.com/raspberrypi/pico-sdk).

On macOS, the ARM toolchain can be installed via Homebrew:

```shell
brew install --cask gcc-arm-embedded
```

```shell
git clone --recursive https://github.com/flipperdevices/flipperone-mcu-firmware.git
cd flipperone-mcu-firmware

git clone -b 2.2.0 https://github.com/raspberrypi/pico-sdk.git ../pico-sdk
cd ../pico-sdk && git submodule update --init && cd ../flipperone-mcu-firmware

mkdir -p build && cd build
PICO_SDK_PATH=../../pico-sdk cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build . --config Release --parallel
```

The output firmware file will be at `build/flipperone-mcu-firmware.uf2`.
</details>

## How to update MCU firmware

The firmware uses the [UF2](https://github.com/microsoft/uf2) format for the RP2350 microcontroller.

### On a Flipper One device

The procedure for entering firmware update mode on the real device differs from a bare board. Follow the official guide: [docs.flipper.net/one/mcu-firmware/firmware-update](https://docs.flipper.net/one/mcu-firmware/firmware-update).

### On a plain RP2350 board

1. Enter bootloader mode: hold the **BOOTSEL** button on the RP2350 while connecting USB
2. A USB mass storage device will appear on your computer
3. Copy the `.uf2` firmware file to the mass storage device
4. The device will automatically reboot with the new firmware

Alternatively, you can flash the firmware with [`picotool`](https://github.com/raspberrypi/picotool) (it is downloaded automatically during the CMake configuration step):

```shell
picotool load -x build/flipperone-mcu-firmware.uf2
```
