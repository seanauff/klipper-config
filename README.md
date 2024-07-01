# klipper-config

This is my klipper config for a [Voron Trident](https://vorondesign.com/voron_trident) (300 mm) with a FYSETC Spider v2.2. I use a Raspberry Pi 4B running Moonraker and Klipper via [KIAUH](https://github.com/dw-0/kiauh).

I do not include the firmware; please compile your own.

# Hardware List

[VT.1376](https://www.reddit.com/r/voroncorexy/comments/1801otq/voron_trident_serial_request/)

All printed parts done on my old Ender 3:

Primary: Priline CF-PC

Accent: PC-PETG Blend

Non chamber parts: GF-PETG

- [FYSETC Voron Trident Kit](https://github.com/FYSETC/FYSETC-Voron-Trident/tree/main) - [BOM v1.0](https://github.com/FYSETC/FYSETC-Voron-Trident/blob/main/BOM.md#revision-10)
- Stealthburner toolhead
- Raspberry Pi 4B 4GB
- FYSETC Spider v2.2
- [FYSETC SB CAN TH](https://wiki.fysetc.com/SB%20CAN%20ToolHead/) v1.3
- [FYSETC UCAN](https://wiki.fysetc.com/UCAN/)
- Steel backers
- Nozzle brush
- Bento filters system
- HULA Vibration damping feet

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

## Accessing Electronics

1. Ensure printer is cooled down.
2. Unload filament.
3. Turn off power to printer.
4. Remove power cord and ethernet cable.
5. Remove any loose objects from top of printer.
6. Remove top handles (2x M3 screws, 2.5 mm hex drive each).
7. Remove Bento filter unit.
8. Turn entire printer upside down, with the front facing away.
9. Remove HULA feet.
10. Remove electronics enclosure latches (4x, M3 screws, 3.5 mm hex drive).
11. Swing open cover.

## Updating Main controller (FYSETC Spider v2.2) firmware

Also see [here](https://wiki.fysetc.com/Spider/#44-firmware-upload).

1. Access the electronics enclosure, followng procedure above.
2. Ensure all power is off.
3. Ensure 5V select jumper is in lower position.
4. Place jumper on BT0 and 3.3V pins. Could also use tweezers.
5. Apply power.
6. Verify board is in DFU mode by checking the output of `lsusb`.
7. Upload new firmware with `dfu-util -R -a 0 -s 0x08008000:leave -D ~/klipper/out/klipper.bin`
8. Remove BT0 jumper.
9. Power cycle the board.

## Updating Toolhead controller (FYSETC SB CAN TH) firmware

Also see [here](https://wiki.fysetc.com/SB%20CAN%20ToolHead/#32-firmware-upload).

1. Turn off power to printer.
2. ???

## Updating USB-to-CAN board (FYSETC UCAN) firmware

1. Access the electronics enclosure, followng procedure above.
2. Ensure all power is off (unplug USB cable).
3. Place jumper on BOOT0 and 3V3 pins. Could also use tweezers.
4. Apply power (plug in USB cable).
5. Verify board is in DFU mode by checking the output of `lsusb`.
6. Upload new firmware with `dfu-util -R -a 0 -s 0x08000000:leave -D ~/klipper/out/klipper.bin`
7. Remove BOOT0 jumper.
8. Power cycle the board (unplug/replug USB cable).
