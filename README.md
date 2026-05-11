# HomeAssistant-Infrared
HomeAssistant + Infrared


## ESPHome sample code used in this video

### ESPHome Basic Setup
```yaml



```

### ESPHome Full Setup
```yaml
esphome:
  name: ir-hub-lounge
  friendly_name: IR Hub Lounge

esp32:
  board: esp32dev
  framework:
    type: esp-idf

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "Rg8yxTktvgxLPF7Da5/MLyBhUH7tGAcK1s8YVNEWnMI="

ota:
  - platform: esphome
    password: "9bc4a92d9ca8f7f30c3a0ee04ff92e88"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Ir-Hub-Lounge Fallback Hotspot"
    password: "TUhHG5Q6tLyA"

remote_receiver:
  pin:
    number: GPIO27
    inverted: true
    mode:
      input: true
      pullup: true
  dump: all  # Use 'all' during setup to identify your protocol

  # Full dump options list: https://esphome.io/components/remote_receiver/
  # Once identified, replace 'all' with just your protocol(s) to keep logs clean
  # Example:
  # dump:
  #   - nec          # Most common - cheap/generic remotes & Arduino IR kits
  #   - samsung      # Samsung TVs
  #   - samsung36    # Samsung36 variant
  #   - sony         # Sony devices
  #   - lg           # LG devices
  #   - panasonic    # Panasonic devices
  #   - pioneer      # Pioneer devices
  #   - jvc          # JVC devices
  #   - rc5          # Older European devices
  #   - rc6          # Older European devices
  #   - coolix       # Some AC units
  #   - haier        # Haier AC units
  #   - midea        # Midea AC units
  #   - toshiba_ac   # Toshiba AC units
  #   - mirage       # Mirage AC units
  #   - dyson        # Dyson fans
  #   - dish         # Dish Network (57.6kHz - many receivers won't decode)
  #   - roomba       # iRobot Roomba
  #   - magiquest    # MagiQuest wands
  #   - byronsx      # Byron SX doorbells
  #   - nexa         # Nexa RF switches
  #   - rc_switch    # Generic RF switches
  #   - brennenstuhl # Brennenstuhl RF
  #   - dooya        # Dooya RF blinds
  #   - drayton      # Drayton Digistat RF
  #   - keeloq       # KeeLoq RF (garage doors etc)
  #   - symphony     # Symphony devices
  #   - gobox        # Go-Box devices
  #   - canalsat     # CanalSat (56kHz - France)
  #   - canalsatld   # CanalSatLD (56kHz - France)
  #   - beo4         # Bang & Olufsen
  #   - aeha         # AEHA (Japan)
  #   - abbwelcome   # ABB-Welcome (wired, not IR)
  #   - toto         # Toto devices
  #   - pronto       # Universal raw format - use if no protocol matches
  #   - raw          # Raw timing dump - last resort fallback

  # ----------------------------------------------------------
  # Tolerance - how much signal timing can deviate before failing to decode
  # Default: 25% - increase if signals are inconsistent or noisy
  tolerance: 25%
  # To use time based tolerance instead use expanded format:
  # tolerance:
  #   type: time
  #   value: 500us  # microseconds (us) - 1000us = 1ms

  # ----------------------------------------------------------
  # Buffer size - internal memory for storing received codes
  # Default: 10kb on ESP32, 1kb on ESP8266 - increase if missing signals
  buffer_size: 10kb

  # ----------------------------------------------------------
  # Filter - ignore pulses shorter than this (removes noise/glitches)
  # Default: 50us - increase if getting false triggers from interference
  # Range: 0 to 4294967295us (1000us = 1ms)
  filter: 50us

  # ----------------------------------------------------------
  # Idle - how long signal must be stable to be considered complete
  # Default: 10ms (10000us) - the signal is considered finished after this period of silence
  # Maximum values (1000us = 1ms):
  #   ESP32 / ESP32-S2:                65536us (~65ms)
  #   Other ESP32 variants (RMT):      32767us (~32ms)
  #   All other platforms (incl C2/C61): 4294967295us
  idle: 10ms

  # ----------------------------------------------------------
  # ID - manually specify an ID for this receiver
  # Only needed if you have multiple receivers on the same device
  # Example:
  # id: ir_receiver_1

  # ----------------------------------------------------------
  # ESP32 RMT HARDWARE SETTINGS
  # Only applies to ESP32 variants with RMT hardware
  # NOT available on ESP32-C2 and ESP32-C61
  # Leave commented unless experiencing memory conflicts
  # More info: https://esphome.io/components/remote_receiver/

  # rmt_symbols - RMT hardware memory allocation
  # rmt_symbols: 64

remote_transmitter:
  pin: GPIO26
  carrier_duty_percent: 50%


# These are just useful visible Home Assistant entities
# so we can confirm the device is online and restart it remotely.
binary_sensor:
  - platform: status
    name: "Status"

button:
  - platform: restart
    name: "Restart"

# Extra buttons go here

  - platform: template
    name: "OK Button"
    on_press:
      - remote_transmitter.transmit_nec:
          address: 0xFF00
          command: 0xE31C

  - platform: template
    name: "TV Volume up"  # ← this can be anything you like
    on_press:
      - remote_transmitter.transmit_samsung:  # ← this is fixed
          data: 0xE0E0E01F
          nbits: 32          



captive_portal:
```
