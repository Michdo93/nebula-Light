# Galaxy Projector LED Nebula Light

Watch the full video tutorial here: https://youtu.be/YwHWbcuztuY

Forked for using MQTT.



ESPHome Code:
```yaml
esphome:
  name: ${name}
  platform: ESP8266
  board: d1_mini

## Edit these for either your Wi-Fi SSID & Password - Or your 'secrets' entries ##
wifi:
  ssid: !secret wifi_ssid 
  password: !secret wifi_password

## Change this for your light's name ##
substitutions:
  name: nebula_light

logger:
  logs:
    light: none
mqtt:
  broker: !ip_to_broker
  port: 1883
  username: !secret broker username
  password: !secret broker password
  client_id: nebula_light
  discovery: true
  birth_message:
    topic: nebula_light/mqtt_status
    payload: online
  will_message:
    topic: nebula_light/mqtt_status
    payload: offline
ota:

light:
  - platform: rgb
    name: ${name} Light
    id: rgb_light
    red: red
    green: green
    blue: blue
    effects:
      - flicker:
          name: Flicker
          alpha: 95%
          intensity: 2.5%
      - random:
          name: Random
          transition_length: 2.5s
          update_interval: 3s
      - random:
          name: Random Slow
          transition_length: 10s
          update_interval: 5s
    on_value:
      - mqtt.publish:
          topic: "nebula_light/rgb/effects/state"
          payload: !lambda |-
          return to_string(id(rgb_light[effects]).state);

  - platform: monochromatic
    name: ${name} Laser
    id: laser
    output: laser_pwm
  - platform: monochromatic
    name: ${name} Motor
    id: motor
    output: motor_pwm

output:
  - platform: esp8266_pwm
    id: red
    pin: D1
    on_value:
      - mqtt.publish:
          topic: "nebula_light/rgb/red/state"
          payload: !lambda |-
          return to_string(id(red).state);
  - platform: esp8266_pwm
    id: green
    pin: D5
    on_value:
      - mqtt.publish:
          topic: "nebula_light/rgb/green/state"
          payload: !lambda |-
          return to_string(id(green).state);
  - platform: esp8266_pwm
    id: blue
    pin: D6
    on_value:
      - mqtt.publish:
          topic: "nebula_light/rgb/blue/state"
          payload: !lambda |-
          return to_string(id(blue).state);

  - platform: esp8266_pwm
    id: laser_pwm
    pin: D7
    frequency: 2000 Hz
    on_value:
      - mqtt.publish:
          topic: "nebula_light/pwm/laser/state"
          payload: !lambda |-
          return to_string(id(laser_pwm).state);
  - platform: esp8266_pwm
    id: motor_pwm
    frequency: 8 Hz
    pin: D8
    on_value:
      - mqtt.publish:
          topic: "nebula_light/pwm/motor/state"
          payload: !lambda |-
          return to_string(id(motor_pwm).state);
```

