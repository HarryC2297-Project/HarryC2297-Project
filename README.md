esphome:
  name: smart_wand
  platform: ESP32
  board: esp32dev

# Enable logging
logger:

# Enable Home Assistant API
api:

# Enable OTA updates
ota:

# Sensor for microphone input
sensor:
  - platform: adc
    pin: GPIO34
    name: "Wand Microphone"
    id: wand_mic
    update_interval: 1s

# Binary sensor for IR receiver
binary_sensor:
  - platform: gpio
    pin: GPIO15
    name: "IR Receiver"
    id: ir_receiver

# Output for IR LED
output:
  - platform: gpio
    pin: GPIO4
    id: ir_led

# Light for the tip of the wand
light:
  - platform: binary
    name: "Wand LED"
    output: ir_led

# Automations for voice commands
automation:
  - alias: Turn on light with Lumos
    trigger:
      platform: state
      entity_id: sensor.wand_mic
      to: "Lumos"
    action:
      - light.turn_on: ir_led
      - homeassistant.service:
          service: light.turn_on
          data:
            entity_id: light.your_light_entity

  - alias: Turn on all lights with Lumos Maxima
    trigger:
      platform: state
      entity_id: sensor.wand_mic
      to: "Lumos Maxima"
    action:
      - light.turn_on: ir_led
      - homeassistant.service:
          service: light.turn_on
          data:
            entity_id: group.all_lights
      - homeassistant.service:
          service: light.turn_on
          data:
            entity_id: light.your_room_lights

  - alias: Turn off lights with Nox
    trigger:
      platform: state
      entity_id: sensor.wand_mic
      to: "Nox"
    action:
      - light.turn_off: ir_led
      - homeassistant.service:
          service: light.turn_off
          data:
            entity_id: group.all_lights

  - alias: Handle IR signal
    trigger:
      platform: state
      entity_id: binary_sensor.ir_receiver
      to: "on"
    action:
      - homeassistant.service:
          service: light.toggle
          data:
            entity_id: light.your_light_entity
