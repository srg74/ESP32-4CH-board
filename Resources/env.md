# Build env for WLED project

```text
[env:esp32_4CH_16mb]
extends = env:esp32dev
upload_speed = 460800
monitor_speed = 115200
upload_protocol = espota
# exchange for your WLED IP
upload_port = "10.0.x.x"
build_flags = ${common.build_flags_esp32}
  -D WLED_WATCHDOG_TIMEOUT=0
  -D WLED_DISABLE_BLYNK
  -D WLED_DISABLE_CRONIXIE
  -D WLED_DISABLE_HUESYNC
  -D WLED_DISABLE_LOXONE
  -D WLED_RELEASE_NAME=esp32_4CH_16mb
  -D SERVERNAME='"esp32_4CH_16mb"'
# Pins assigment and pixel count
  -D DATA_PINS=2,4,12,3
  -D PIXEL_COUNTS=100,100,100,100
  -D RLYPIN=-1
  -D IRPIN=-1
  -D BTNPIN=-1
  -D ABL_MILLIAMPS_DEFAULT=9500 ; PS 10A
# User mods
  # 4 line display usermod
  -D USERMOD_FOUR_LINE_DISPLAY
  -D USE_ALT_DISPlAY
  -D I2CSCLPIN=13 ;Global I2C pins settings
  -D I2CSDAPIN=32 ;Global I2C pins settings
  -D FLD_TYPE=SSD1306_64
# PWM Fan mod
  -D USERMOD_PWM_FAN
  -D PWM_PIN=33
  -D TACHO_PIN=34
# SD card
  -D WLED_USE_SD_SPI
  -D HW_PIN_CSSPI=16
  -D SD_PRINT_HOME_DIR
# Temperature usermod
  -D USERMOD_SHT
  -D USERMOD_SHT_TYPE_SHT31=1
# Ethernet card usermod
  -D WLED_USE_ETHERNET
# Ethernet card usermod
  -D USERMOD_ROTARY_ENCODER_UI
  -D ENCODER_DT_PIN=35
  -D ENCODER_CLK_PIN=39
  -D ENCODER_SW_PIN=0
# Multirelay usermod
  -D USERMOD_MULTI_RELAY  # 5, 18, 32 & 36
  -D USERMOD_USE_PCF8574
# End user mods
;  -D WLED_DEBUG
  -UWLED_USE_MY_CONFIG
lib_deps = ${esp32.lib_deps}
  https://github.com/RobTillaart/SHT85
  olikraus/U8g2 @ ^2.34.22 # OLED sisplay lib
board_build.partitions = tools/WLED_ESP32_16MB.csv
```
