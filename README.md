# Nordic / u-blox / Avnet Workshop
Learning Zephyr RTOS, the Nordic nRF Connect SDK and Bluetooth LE on Nordic & u-blox hardware.
#### Based on nRF Connect SDK v3.3.0

| Date       | Location |
|------------|----------|
| 02.06.2026 | Berlin - Classic Remise |

## Preparations

* Installed IDE (VS Code) with Nordic Extension Pack
* Installed nRF Connect SDK + toolchain in stated version
* Download the devicetree files for the EVK-NORA B2: [Download EVK-NORA-B2 files](ubx_evknorab2_zephyr_main.zip)
* Setup the EVK-NORA-B2 out-of-tree (not part of NCS v3.3.0) devicetree files within VS Code (see next step)

#### Setup VS Code and the nRF Connect Extension for custom board directories:
1. Copy custom/third-party device tree files into a directory of your choice
2. The directory should contain the boards folder followed by /vendor/board_name/
3. Add the custom boards directory to VS Code 
    * Open VS code settings via the top command bar, enter: `> Preferences Open User Settings`
    * Search for "board roots" & find `Nrf-connect: Board Roots` as setting
    * `Add item`, use the absolute path to your custom board directory

## Code snippets for hands-on sessions
This repository contains code snippets useful for the hands on sessions. 

1) IDE Navigation and Zephyr's blinky sample
     - IDE navigation, see slide deck
     - Blinky sample code analysis, see slide deck
     - [Changing the LED](sessions/1_blinky.md)
     - [Adding a custom kconfig menu](sessions/1_blinky.md#task-3-adding-custom-kconfig-definitions)
     - [Using Zephyr's system work queue](sessions/1_blinky.md#task-4-using-the-zephyr-system-work-queue)

2) Bluetooth LE
     - [Adding Bluetooth LE Advertising](sessions/2_bluetooth_le.md)
     - [Working with Nordic's LED and Button Service](sessions/2_bluetooth_le.md#task-2-working-with-the-peripheral_lbs-sample)
          - SDK sample located at: <code>sdk/nrf/samples/bluetooth/peripheral_lbs</code>
     
3) Addon: Sensor integration
     - [Adding a simulated sensor](sessions/3_addon_sim_sensor.md)

