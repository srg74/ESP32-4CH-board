# ESP32 4 channel development board
ESP32 4 channel development board
[![release](https://img.shields.io/github/v/release/srg74/ESP32-4CH-board)](https://img.shields.io/github/v/release/srg74/ESP32-4CH-board)
[![License: CC0](https://img.shields.io/badge/License-CC0-blue.svg?style=flat-square)](https://github.com/srg74/ESP32-4CH-board/blob/main/LICENSE)
[![custom firmware](https://img.shields.io/static/v1?label=Custom&message=firmware&color=blue&style=flat-square)](https://github.com/srg74/ESP32-4CH-board/resources/firmware)
[![Official releases](https://img.shields.io/static/v1?label=WLED&message=firmware&color=green&style=flat-square)](https://github.com/Aircoookie/WLED/releases)
[![ESPhome](https://img.shields.io/static/v1?label=ESPhome&message=firmware&color=green&style=flat-square)](https://esphome.io)

I appreciate your support for my project! [![donate](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/donate/?hosted_button_id=VU7L89Z2RR7S4)

![Board](https://github.com/srg74/ESP32-4CH-board/blob/main/Resources/images/4CH_board.jpg)

## ESP32 4 channel development board with following features

- Very easy to configure for your project;
- Can be used with WLED or ESPhome firmware;
- MicroSD card socket;
- Rotary encoder;
- [Ethernet shield](https://github.com/srg74/ESP32_ethernet) compatible;
- [Serial connection header for programming](https://github.com/srg74/ESP-uploader-CH340C);
- Level shifter for reliable data signal;
- Solder jumpers for flexible configuration for display;
- Board power selector (e.g 5VDC or 12/24VDC);
- Indepent power input for all 4 relay channels. Meaning LEDs power can be mixed (5V or 12V independent for each channel);
- Can be configured for 1 (data) wire and 2 (data, clock) wire addressable LED strips;
- Headers for shield expantion;
- Relays for power saving feature (without disconnecting power from LED strip it still consumes current even if none of the LEDs are lit);
- I2C connector for OLED display or other devices that might be integrated in a future;
- Built in SHTx temperature and humidity sensor;
- 4 pin PWM fan circuit header.

## Firmware

- [WLED firmware](https://github.com/srg74/WLED-wemos-shield/blob/master/resources/Firmware/%40Aircoookie/Latest/4ch_board) file naming WLED_vXX.X_esp32_4CH_16mb.bin

### Ready to use bin files

- [@Aircookie WLED repo firmware builds](https://github.com/srg74/WLED-wemos-shield/tree/master/resources/Firmware/%40Aircoookie) - Original WLED repository bin files.

### Resources used for bin files compiling

- [WLED repository](https://github.com/Aircoookie/WLED) - Aircoookie original WLED repository
- [ESPhome](https://esphome.io) - ESPhome project
