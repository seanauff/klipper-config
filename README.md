# klipper-config

This is my klipper config for a [Voron Trident](https://vorondesign.com/voron_trident) (300 mm) with a FYSETC Spider v2.2. I use a Raspberry Pi 4B running Moonraker and Klipper via [KIAUH](https://github.com/dw-0/kiauh).

I do not include the firmware; please compile your own.

# Hardware List

[VT.1376](https://www.reddit.com/r/voroncorexy/comments/1801otq/voron_trident_serial_request/)

All printed parts done on my old Ender 3:

Primary: Priline CF-PC (some hotend parts started warping so I replaced them with ABS versions)

Accent: PC-PETG Blend

Non chamber parts: GF-PETG

- [FYSETC Voron Trident Kit](https://github.com/FYSETC/FYSETC-Voron-Trident/tree/main) - [BOM v1.0](https://github.com/FYSETC/FYSETC-Voron-Trident/blob/main/BOM.md#revision-10)
- Stealthburner toolhead
- [Cartographer CNC Mount](https://docs.cartographer3d.com/cartographer-cnc-mount/installation)
- Raspberry Pi 4B 4GB
- FYSETC Spider v2.2 [Github](https://github.com/FYSETC/FYSETC-SPIDER). Connected via USB to RPi.
- [FYSETC SB CAN TH](https://wiki.fysetc.com/SB%20CAN%20ToolHead/) v1.3B [Github](https://github.com/FYSETC/FYSETC_SB_CAN_TOOLHEAD)
- [FYSETC UCAN](https://wiki.fysetc.com/UCAN/) [Github](https://github.com/FYSETC/UCAN). Connected via USB to RPi.
- [FYSETC Hotkey](https://www.fysetc.com/products/fysetc-hot-key-board-voron-skirt-button-pcb-voron-skirt-klipper-pre-installed-pcb-board-with-neopixel-led-for-voron-v2-4-trident-switchwire-3d-printers) [Github](https://github.com/FYSETC/Hotkey) Connected via USB to RPi.
- Steel backers
- Nozzle brush
- Bento filters system
- HULA Vibration damping feet
- Neopixel caselights
- chamber thermistor

# Procedures

## Building Firmwares

Also see [here](https://www.klipper3d.org/Installation.html#building-and-flashing-the-micro-controller).

1. Ensure klipper is updated to the latest version.
2. Access the klipper directory, typically `~/klipper`
3. `make clean`
4. `make menuconfig`
5. Select the options according to the microcontroller.
6. `make`
7. Compiled firmware is located in `out/klipper.bin`

### Make menuconfig for UCAN

- Micro-controller Architechture (STMicroelectronics STM32)
- Processor model (STM32F072)
- Bootloader offset (No bootloader)
- Clock Reference (8 MHz crystal)
- Communication interface (USB to CAN bus bridge (USB on PA11/PA12))
- CAN bus interface (CAN bus (on PB8/PB9))
- (1000000) CAN bus speed

## Accessing Electronics

1. Ensure printer is cooled down.
2. Unload filament.
3. Turn off power via main AC inlet switch.
4. Remove power cord and ethernet cable.
5. Remove any loose objects from top of printer.
6. Remove top handles (2x M3 screws, 2.5 mm hex drive each).
7. Remove Bento filter unit.
8. Turn entire printer upside down, with the front facing away.
9. Remove HULA feet.
10. Remove electronics enclosure latches (4x, M3 screws, 3.5 mm hex drive).
11. Swing open cover.

## Updating Main controller (FYSETC Spider v2.2) firmware

Also see https://wiki.fysetc.com/docs/Spider#4.2%20Klipper

### Traditional method (BT0 pin not accessible)

Note: Power may be left on and physical reset button used to reboot microcontroller.

1. Access the electronics enclosure, following procedure above.
2. Ensure all power is off.
3. Ensure 5V select jumper is in lower position (DC5V).
4. Place jumper on BT0 and 3.3V pins. Could also use tweezers.
5. Turn on power via main AC inlet switch.
6. Verify board is in DFU mode by checking the output of `lsusb`.
7. Upload new firmware with `dfu-util -R -a 0 -s 0x08008000:leave -D ~/klipper/out/klipper.bin`
8. Turn off power via main AC inlet switch.
9. Remove BT0 jumper.
10. Firmware will be ready on next boot.

### Alternate method (using external DFU boot button)

1. Turn off power via main AC inlet switch.
2. Press and hold external DFU mode switch.
3. Turn on power via main AC inlet switch.
4. Verify board is in DFU mode by checking the output of `lsusb`.
5. Upload new firmware with `dfu-util -R -a 0 -s 0x08008000:leave -D ~/klipper/out/klipper.bin`
6. Turn off power via main AC inlet switch.
7. Firmware will be ready on next boot.

### Alternate method 2 (using external DFU boot button and external reset button)

1. Ensure power is on.
2. Press and hold external DFU mode switch.
3. Press reset button.
4. Verify board is in DFU mode by checking the output of `lsusb`.
5. Upload new firmware with `dfu-util -R -a 0 -s 0x08008000:leave -D ~/klipper/out/klipper.bin`
6. Press reset button.
7. Firmware will be ready on next boot.

## Updating Toolhead controller (FYSETC SB CAN TH) firmware

Also see https://wiki.fysetc.com/docs/SB-CAN-ToolHead#3.%20Firmware%20Guide

*Note:* When the micro USB port is plugged in, the 5V line pulls the BOOT0 pin high.

**WARNING: Never Connect or Disconnect the 2X2P power/canbus connector while power is on!**

### Traditional method (power cycling main PSU)

1. Turn off power via main AC inlet switch.
2. Open printhead cover (1x M3 screw, 2 mm hex drive)
3. Connect Micro USB cable from Pi (via external keystone USB port) to SB CAN PCB.
4. Turn on power via main AC inlet switch.
5. Verify board is in DFU mode by checking the output of `lsusb`.
6. Upload new firmware with `dfu-util -R -a 0 -s 0x08000000:leave -D ~/klipper/out/klipper.bin`
7. Turn off power via main AC inlet switch.
8. Disconnect Micro USB cable.
9. Close printhead cover.
10. Firmware will be ready on next boot.

### Alternate method (use reset switch)

1. Ensure power is on.
2. Open printhead cover (1x M3 screw, 2 mm hex drive)
3. Connect Micro USB cable from Pi (via external keystone USB port) to SB CAN PCB.
4. Hold reset button for 2 seconds, then release it.
5. Verify board is in DFU mode by checking the output of `lsusb`.
6. Upload new firmware with `dfu-util -R -a 0 -s 0x08000000:leave -D ~/klipper/out/klipper.bin`
7. Disconnect Micro USB cable.
8. Hold reset button for 2 seconds, then release it.
9. Close printhead cover.

## Updating USB-to-CAN board (FYSETC UCAN) firmware

1. Access the electronics enclosure, following procedure above.
2. Ensure all power is off.
3. Place jumper on BOOT0 and 3V3 pins. Could also use tweezers.
4. Turn on power via main AC inlet switch.
5. Verify board is in DFU mode by checking the output of `lsusb`.
6. Upload new firmware with `dfu-util -R -a 0 -s 0x08000000:leave -D ~/klipper/out/klipper.bin`
7. Turn off power via main AC inlet switch.
8. Remove BOOT0 jumper.
9. Firmware will be ready on next boot.

## Updating Hotkey board firmware

Also see [here](https://github.com/FYSETC/Hotkey?tab=readme-ov-file#klipper)
