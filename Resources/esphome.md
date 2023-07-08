# ESPhome YAML code

```YAML
substitutions:
  device_name: esp32-4ch-16mb
  friendly_name: "ESP32 4CH 16mb"
  device_description: "Devboard-4CH-16mb"
  temperature_threshold_low: "30" # At what temperature, in celcius, should the fan turn on to its minimum speed
  temperature_threshold_high: "35" # At what temperature, in celcius, should the fan turn on to its maximum speed
  minimum_fan_speed: "0" # What is the minimum fan speed, as a percentage

esphome:
  name: '${device_name}'
  friendly_name: '${friendly_name}'

esp32:
  board: esp32dev
  framework:
    type: arduino

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "xxxxxxxxxxxxxxxxxxxxxxxxx"

ota:
  safe_mode: true
  reboot_timeout: 10min
  num_attempts: 5
  password: "xxxxxxxxxxxxxxxxxxxxxxxxx"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Esp32-4Ch-16Mb Fallback Hotspot"
    password: "xxxxxxxxxxxxxxxxxxxxxxxxx"

captive_portal:


# Defines the pins for the temperature sensor
i2c:
  sda: 32
  scl: 13
  scan: true
  id: bus_a

# Defines pcf8574 port extender
pcf8574:
  - id: 'pcf8574_hub'
    address: 0x20
    pcf8575: false

# Individual outputs
switch:
  - platform: gpio
    name: "PCF8574 Pin #1"
    restore_mode: RESTORE_DEFAULT_OFF
    id: relay1
    pin:
      pcf8574: pcf8574_hub
      # Use pin number 0
      number: 0
      # One of INPUT or OUTPUT
      mode:
        output: true
      inverted: false

  - platform: gpio
    name: "PCF8574 Pin #2"
    restore_mode: RESTORE_DEFAULT_OFF
    id: relay2
    pin:
      pcf8574: pcf8574_hub
      # Use pin number 1
      number: 1
      # One of INPUT or OUTPUT
      mode:
        output: true
      inverted: false
  - platform: gpio
    name: "PCF8574 Pin #3"
    restore_mode: RESTORE_DEFAULT_OFF
    id: relay3
    pin:
      pcf8574: pcf8574_hub
      # Use pin number 2
      number: 2
      # One of INPUT or OUTPUT
      mode:
        output: true
      inverted: false
  - platform: gpio
    name: "PCF8574 Pin #4"
    restore_mode: RESTORE_DEFAULT_OFF
    id: relay4
    pin:
      pcf8574: pcf8574_hub
      # Use pin number 3
      number: 3
      # One of INPUT or OUTPUT
      mode:
        output: true
      inverted: false

# PWM output for the fan speed control
output:
  - platform: ledc
    pin: 33
    id: pwm_output

# Hidden switch object to control the relay
# switch:
#   - platform: gpio
#     name: "fan_relay"
#     id: fan_relay
#     pin: D3
#     internal: true

text_sensor:

  # Send IP Address
  - platform: wifi_info
    ip_address:
      name: $friendly_name IP Address
      id: ip_addr

  # Send Uptime in raw seconds
  - platform: template
    name: $friendly_name Uptime
    id: uptime_human
    icon: mdi:clock-start

sensor:
  - platform: wifi_signal
    name: $friendly_name WiFi Strength
    update_interval: 60s
  - platform: rotary_encoder
    name: "Rotary Encoder"
    pin_a: 35
    pin_b: 39
  # This is a bit of overkill. It sends a human readable 
  # uptime string 1h 41m 32s instead of 6092 seconds
  - platform: uptime
    name: $friendly_name Uptime
    id: uptime_sensor
    update_interval: 60s
    on_raw_value:
      then:
        - text_sensor.template.publish:
            id: uptime_human
            # Custom C++ code to generate the result
            state: !lambda |-
              int seconds = round(id(uptime_sensor).raw_state);
              int days = seconds / (24 * 3600);
              seconds = seconds % (24 * 3600);
              int hours = seconds / 3600;
              seconds = seconds % 3600;
              int minutes = seconds /  60;
              seconds = seconds % 60;
              return (
                (days ? to_string(days) + "d " : "") +
                (hours ? to_string(hours) + "h " : "") +
                (minutes ? to_string(minutes) + "m " : "") +
                (to_string(seconds) + "s")
              ).c_str();

# Calls a script to set the fan state/speed whenever the temperature value changes
  - platform: sht3xd # SHT30 temperature sensor
    temperature:
      name: "${device_description} Temperature"
      id: temperature_sensor
      on_value:
        then:
          - script.execute: set_fan_state
    humidity:
      name: "${device_description} Humidity"
      id: humidity_sensor
    address: 0x44
    update_interval: 60s

  - platform: pulse_counter
    pin: 
      number: GPIO34   # Connect to any input PIN on the ESP
    unit_of_measurement: 'RPM'
    id: fan_speed
    name: $friendly_name Fan Speed
    accuracy_decimals: 0
    filters:
      - multiply: 0.5  # Depending on how many pulses the fan sends per round - should be 0.5 or 1 - try...

# The actual fan entity presented to Home Assistant
fan:
  - platform: speed
    output: pwm_output
    name: "${device_description}"
    id: "pwm_fan"

# Sets the speed of the fan based on a linear calculation
# between the high and low temperature thresholds and
# the minimum specified fan speed
script:
  - id: set_fan_state
    then:
      - if:
          condition:
            lambda: |-
              return id(temperature_sensor).state < id(${temperature_threshold_low});
          then:
            - fan.turn_off: pwm_fan
          else:
            - fan.turn_on:
                id: pwm_fan
                speed: !lambda |-
                  if (id(temperature_sensor).state >= id(${temperature_threshold_high})) {
                    // Over upper threshold, fan speed at maximum
                    ESP_LOGD("Fan speed calc", "Temperature is above or equal to upper threshold so setting to max");
                    return 100;
                  }
                  else {
                    float calc_speed = ((100-id(${minimum_fan_speed})) / (id(${temperature_threshold_high})-id(${temperature_threshold_low})))*(id(temperature_sensor).state-id(${temperature_threshold_low}))+id(${minimum_fan_speed});
                    ESP_LOGD("Fan speed calc", "calculated speed = %f", calc_speed);
                    return calc_speed;
                  }
time:
  - platform: homeassistant
    id: esptime
font:
  - file: "fonts/arial.ttf"
    id: font_arial_12
    size: 12
  - file: "fonts/arial.ttf"
    id: font_arial_10
    size: 10
  - file: "fonts/arial.ttf"
    id: font_arial_8
    size: 8
display:
  - platform: ssd1306_i2c
    model: "SH1106 128x64"
    address: 0x3C
    contrast: 40%
    lambda: |-
      // Print first line
      it.printf(0, 15, id(font_arial_12), TextAlign::BASELINE_LEFT, "TMP: ");
      it.printf(30, 15, id(font_arial_12), TextAlign::BASELINE_LEFT, "%.0f°", id(temperature_sensor).state);
      it.printf(60, 15, id(font_arial_12), TextAlign::BASELINE_LEFT, "HUM: ");
      it.printf(90, 15, id(font_arial_12), TextAlign::BASELINE_LEFT, "%.0f%%", id(humidity_sensor).state);
      // Print second line
      it.printf(0, 20, id(font_arial_10), "RLY1: %s", id(relay1).state ? "ON" : "OFF");
      it.printf(60, 20, id(font_arial_10), "RLY2: %s", id(relay2).state ? "ON" : "OFF");
      it.printf(0, 32, id(font_arial_10), "RLY3: %s", id(relay3).state ? "ON" : "OFF");
      it.printf(60, 32, id(font_arial_10), "RLY4: %s", id(relay4).state ? "ON" : "OFF");
      // Print time in HH:MM format
      it.printf(0, 48, id(font_arial_10), "IP: %s", id(ip_addr).state.c_str());
```
