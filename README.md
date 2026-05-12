# HomeAssistant + Infrared

## ESPHome sample code used in this video

## Basic configuration

```yaml
esphome:
  name: your-device-name        # e.g. ir-hub-lounge (lowercase, hyphens only)
  friendly_name: Your Device Name  # e.g. IR Hub Lounge (displays in Home Assistant)

esp32:
  board: esp32dev      # change if using a different ESP32 board - full list: https://registry.platformio.org/platforms/platformio/espressif32/boards
  framework:
    type: esp-idf      # recommended - change to arduino if you experience compatibility issues
    # Framework guide: https://esphome.io/components/esp32/#framework

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "YOUR_ENCRYPTION_KEY"

ota:
  - platform: esphome
    password: "YOUR_OTA_PASSWORD"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

# Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Your-Device Fallback Hotspot"
    password: "YOUR_AP_PASSWORD"

captive_portal:

##################################
# REMOTE RECEIVER GOES HERE
# https://esphome.io/components/remote_receiver/
##################################


##################################
# REMOTE RECEIVER SETTINGS GO HERE
##################################


##################################
# REMOTE TRANSMITTER GOES HERE
# https://esphome.io/components/remote_transmitter/
##################################


##################################
# BINARY SENSORS GO HERE
##################################

binary_sensor:

##################################
# BUTTONS GO HERE
##################################

button:


##################################
# AUTOMATIONS GO HERE
# https://esphome.io/components/remote_transmitter/#automations
##################################


```

## Remote receiver
```yaml
remote_receiver:
  pin:
    number: GPIO_PIN    # replace with your receiver pin e.g. GPIO27
    inverted: true      # default: false - set to true for most IR receiver modules (active-low signal)
    mode:               # required when setting pullup - defines pin behaviour
      input: true       # always true for a receiver pin
      pullup: true      # enables internal pull-up resistor - required for some modules e.g. TSOP38238
  dump: all  # comment this out and uncomment dump: below to filter specific protocols
# Full protocol list: https://esphome.io/components/remote_receiver/
```
## Remote receiver settings
```yaml
#  dump:
#    - aeha         # AEHA infrared codes
#    - beo4         # B&O Beo4 infrared codes
#    - canalsat     # CanalSat infrared codes (56kHz)
#    - canalsatld   # CanalSatLD infrared codes (56kHz)
#    - coolix       # Coolix infrared codes
#    - dish         # Dish infrared codes (57.6kHz - many receivers won't decode)
#    - dyson        # Dyson Cool AM7 fan codes
#    - jvc          # JVC infrared codes
#    - gobox        # Go-Box infrared codes
#    - haier        # Haier infrared codes
#    - lg           # LG infrared codes
#    - magiquest    # MagiQuest wand infrared codes
#    - midea        # Midea infrared codes
#    - nec          # NEC infrared codes (most common - cheap/generic remotes)
#    - panasonic    # Panasonic infrared codes
#    - pioneer      # Pioneer infrared codes
#    - rc5          # RC5 infrared codes
#    - rc6          # RC6 infrared codes
#    - roomba       # Roomba infrared codes
#    - samsung      # Samsung infrared codes
#    - samsung36    # Samsung36 infrared codes
#    - symphony     # Symphony infrared codes
#    - sony         # Sony infrared codes
#    - toshiba_ac   # Toshiba AC infrared codes
#    - mirage       # Mirage infrared codes
#    - toto         # Toto infrared codes
#    - pronto       # Universal raw format - use if no protocol matches
#    - raw          # Raw timing dump - last resort fallback


# ----------------------------------------------------------
# Tolerance - how much signal timing can deviate before failing to decode
# Default: 25% - increase if signals are inconsistent or noisy
  tolerance: 25%
# Or use time based tolerance instead (comment out line above and uncomment below)
#  tolerance:
#    type: time
#    value: 500us  # microseconds (us) - 1000us = 1ms


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
# ESP32 / ESP32-S2:                65536us (~65ms)
# Other ESP32 variants (RMT):      32767us (~32ms)
# All other platforms (incl C2/C61): 4294967295us
  idle: 10ms

# ----------------------------------------------------------
# ID - manually specify an ID for this receiver
# Only needed if you have multiple receivers on the same device
# Example:
#  id: ir_receiver_1

# ----------------------------------------------------------
# ESP32 RMT HARDWARE SETTINGS
# Only applies to ESP32 variants with RMT hardware
# NOT available on ESP32-C2 and ESP32-C61
# Leave commented unless experiencing memory conflicts
# More info: https://esphome.io/components/remote_receiver/

# rmt_symbols - RMT hardware memory allocation
#  rmt_symbols: 64  # [uncomment to use]

# receive_symbols - maximum receive length in symbols
#  receive_symbols: 64  # [uncomment to use]

# filter_symbols - ignore received data shorter than this (filters noise bursts)
#  filter_symbols: 4  # [uncomment to use]

# clock_resolution - RMT peripheral clock speed in Hz
# Default: 1000000
#  clock_resolution: 1000000  # [uncomment to use]

# use_dma - enable Direct Memory Access (only on supported variants)
# If enabled rmt_symbols controls the DMA buffer size
#  use_dma: false  # [uncomment to use]


# ----------------------------------------------------------
# SIGNAL DEMODULATION
# Only needed if connecting a raw IR photodiode directly to ESP32
# Standard IR receiver modules (VS1838B etc) already demodulate the signal
# Most users will never need this - leave commented
# More info: https://esphome.io/components/remote_receiver/

# carrier_duty_percent - carrier duty cycle for demodulation (%)
# Default: 100
#  carrier_duty_percent: 100  # [uncomment to use]

# carrier_frequency - carrier frequency for demodulation in Hz
# Default: 0 (disabled)
#  carrier_frequency: 38000  # [uncomment to use] 38kHz is standard for most IR
```

### Remote transmitter
```yaml
remote_transmitter:
  pin: GPI_PIN                 # replace with your receiver pin e.g. GPIO26
  carrier_duty_percent: 50%   # 50% for IR LEDs, 100% for RF (433MHz)
```


## Button Examples by Brand/Protocol

### Samsung - Button Template (copy and edit)
```yaml
  - platform: template
    name: "NAME_OF_BUTTON"  # ← this can be anything you like
    on_press:
      - remote_transmitter.transmit_samsung:  # ← this is fixed
          data: DATA_ID
          nbits: 32
```

### Samsung - Example Button (working code)
```yaml
  - platform: template
    name: "TV Power Off"
    on_press:
      - remote_transmitter.transmit_samsung:
          data: 0xE0E019E6
          nbits: 32
```

### NEC - Button Template (copy and edit)
```yaml
  - platform: template
    name: "NAME_OF_BUTTON"  # ← this can be anything you like
    on_press:
      - remote_transmitter.transmit_nec:
          address: ADDRESS_ID
          command: COMMAND_ID
```

### NEC - Example Button (working code)
```yaml
  - platform: template
    name: "Light Power"
    on_press:
      - remote_transmitter.transmit_nec:
          address: 0xDEA8
          command: 0xFF00
```

### LG - Button Template (copy and edit)
```yaml
- platform: template
    name: "NAME_OF_BUTTON"  # ← this can be anything you like
    on_press:
      - remote_transmitter.transmit_lg:
          data: DATA_ID
          nbits: 32
```

### LG - Example Button (working code)
```yaml
- platform: template
    name: "Light Power"
    on_press:
      - remote_transmitter.transmit_lg:
          data: 0x157B00FF
          nbits: 32
```

### Pronto - Button Template (copy and edit)
```yaml
- platform: template
    "NAME_OF_BUTTON"  # ← this can be anything you like
    on_press:
      - remote_transmitter.transmit_pronto:
          data: DATA_ID
```

### LG - Example Button (working code)
```yaml
- platform: template
    name: "Light Power"
    on_press:
      - remote_transmitter.transmit_pronto:
          data: "0000 006D 0022 0000 015A 00AE 0015 0016 0015 0016 0015 0016 0015 0041 0015 0016 0015 0042 0015 0016 0015 0041 0015 0016 0015 0042 0015 0042 0015 0041 0015 0041 0015 0016 0015 0041 0015 0041 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 0016 0015 0041 0015 0041 0015 0041 0015 0041 0015 0042 0015 0042 0015 0042 0015 0041 0015 0181"  
```

### ESPHome Full Setup Template
```yaml
```yaml
esphome:
  name: your-device-name        # e.g. ir-hub-lounge (lowercase, hyphens only)
  friendly_name: Your Device Name  # e.g. IR Hub Lounge (displays in Home Assistant)

esp32:
  board: esp32dev      # change if using a different ESP32 board - full list: https://registry.platformio.org/platforms/platformio/espressif32/boards
  framework:
    type: esp-idf      # recommended - change to arduino if you experience compatibility issues
    # Framework guide: https://esphome.io/components/esp32/#framework

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "YOUR_ENCRYPTION_KEY"

ota:
  - platform: esphome
    password: "YOUR_OTA_PASSWORD"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

# Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Your-Device Fallback Hotspot"
    password: "YOUR_AP_PASSWORD"

captive_portal:

##################################
# REMOTE RECEIVER GOES HERE
# https://esphome.io/components/remote_receiver/
##################################

remote_receiver:
  pin:
    number: GPIO_PIN    # replace with your receiver pin e.g. GPIO27
    inverted: true      # default: false - set to true for most IR receiver modules (active-low signal)
    mode:               # required when setting pullup - defines pin behaviour
      input: true       # always true for a receiver pin
      pullup: true      # enables internal pull-up resistor - required for some modules e.g. TSOP38238
  dump: all  # comment this out and uncomment dump: below to filter specific protocols
# Full protocol list: https://esphome.io/components/remote_receiver/

##################################
# REMOTE RECEIVER SETTINGS GO HERE
##################################

#  dump:
#    - aeha         # AEHA infrared codes
#    - beo4         # B&O Beo4 infrared codes
#    - canalsat     # CanalSat infrared codes (56kHz)
#    - canalsatld   # CanalSatLD infrared codes (56kHz)
#    - coolix       # Coolix infrared codes
#    - dish         # Dish infrared codes (57.6kHz - many receivers won't decode)
#    - dyson        # Dyson Cool AM7 fan codes
#    - jvc          # JVC infrared codes
#    - gobox        # Go-Box infrared codes
#    - haier        # Haier infrared codes
#    - lg           # LG infrared codes
#    - magiquest    # MagiQuest wand infrared codes
#    - midea        # Midea infrared codes
#    - nec          # NEC infrared codes (most common - cheap/generic remotes)
#    - panasonic    # Panasonic infrared codes
#    - pioneer      # Pioneer infrared codes
#    - rc5          # RC5 infrared codes
#    - rc6          # RC6 infrared codes
#    - roomba       # Roomba infrared codes
#    - samsung      # Samsung infrared codes
#    - samsung36    # Samsung36 infrared codes
#    - symphony     # Symphony infrared codes
#    - sony         # Sony infrared codes
#    - toshiba_ac   # Toshiba AC infrared codes
#    - mirage       # Mirage infrared codes
#    - toto         # Toto infrared codes
#    - pronto       # Universal raw format - use if no protocol matches
#    - raw          # Raw timing dump - last resort fallback


# ----------------------------------------------------------
# Tolerance - how much signal timing can deviate before failing to decode
# Default: 25% - increase if signals are inconsistent or noisy
  tolerance: 25%
# Or use time based tolerance instead (comment out line above and uncomment below)
#  tolerance:
#    type: time
#    value: 500us  # microseconds (us) - 1000us = 1ms


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
# ESP32 / ESP32-S2:                65536us (~65ms)
# Other ESP32 variants (RMT):      32767us (~32ms)
# All other platforms (incl C2/C61): 4294967295us
  idle: 10ms

# ----------------------------------------------------------
# ID - manually specify an ID for this receiver
# Only needed if you have multiple receivers on the same device
# Example:
#  id: ir_receiver_1

# ----------------------------------------------------------
# ESP32 RMT HARDWARE SETTINGS
# Only applies to ESP32 variants with RMT hardware
# NOT available on ESP32-C2 and ESP32-C61
# Leave commented unless experiencing memory conflicts
# More info: https://esphome.io/components/remote_receiver/

# rmt_symbols - RMT hardware memory allocation
#  rmt_symbols: 64  # [uncomment to use]

# receive_symbols - maximum receive length in symbols
#  receive_symbols: 64  # [uncomment to use]

# filter_symbols - ignore received data shorter than this (filters noise bursts)
#  filter_symbols: 4  # [uncomment to use]

# clock_resolution - RMT peripheral clock speed in Hz
# Default: 1000000
#  clock_resolution: 1000000  # [uncomment to use]

# use_dma - enable Direct Memory Access (only on supported variants)
# If enabled rmt_symbols controls the DMA buffer size
#  use_dma: false  # [uncomment to use]


# ----------------------------------------------------------
# SIGNAL DEMODULATION
# Only needed if connecting a raw IR photodiode directly to ESP32
# Standard IR receiver modules (VS1838B etc) already demodulate the signal
# Most users will never need this - leave commented
# More info: https://esphome.io/components/remote_receiver/

# carrier_duty_percent - carrier duty cycle for demodulation (%)
# Default: 100
#  carrier_duty_percent: 100  # [uncomment to use]

# carrier_frequency - carrier frequency for demodulation in Hz
# Default: 0 (disabled)
#  carrier_frequency: 38000  # [uncomment to use] 38kHz is standard for most IR

##################################
# REMOTE TRANSMITTER GOES HERE
# https://esphome.io/components/remote_transmitter/
##################################

remote_transmitter:
  pin: GPIO26                 # replace with your receiver pin e.g. GPIO26
  carrier_duty_percent: 50%   # 50% for IR LEDs, 100% for RF (433MHz)

##################################
# BINARY SENSORS GO HERE
##################################

binary_sensor:

# DEVICE STATUS - confirms device is online in Home Assistant
binary_sensor:
  - platform: status
    name: "Status"

##################################
# BUTTONS GO HERE
##################################

button:

# BUILT IN BUTTONS
button:
  - platform: restart
    name: "Restart"

##################################
# AUTOMATIONS GO HERE
# https://esphome.io/components/remote_transmitter/#automations
##################################
```

### ESPHome Full Setup Example  DELETE DELETE
```yaml
esphome:
  name: your-device-name        # e.g. ir-hub-lounge (lowercase, hyphens only)
  friendly_name: Your Device Name  # e.g. IR Hub Lounge (displays in Home Assistant)

esp32:
  board: esp32dev      # change if using a different ESP32 board - full list: https://registry.platformio.org/platforms/platformio/espressif32/boards
  framework:
    type: esp-idf      # recommended - change to arduino if you experience compatibility issues
    # Framework guide: https://esphome.io/components/esp32/#framework

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: "YOUR_ENCRYPTION_KEY"

ota:
  - platform: esphome
    password: "YOUR_OTA_PASSWORD"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

# Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Your-Device Fallback Hotspot"
    password: "YOUR_AP_PASSWORD"

captive_portal:

##################################
# REMOTE RECEIVER GOES HERE
# https://esphome.io/components/remote_receiver/
##################################

remote_receiver:
  pin:
    number: GPIO_PIN    # replace with your receiver pin e.g. GPIO27
    inverted: true      # default: false - set to true for most IR receiver modules (active-low signal)
    mode:               # required when setting pullup - defines pin behaviour
      input: true       # always true for a receiver pin
      pullup: true      # enables internal pull-up resistor - required for some modules e.g. TSOP38238
  dump: all  # comment this out and uncomment dump: below to filter specific protocols
# Full protocol list: https://esphome.io/components/remote_receiver/

##################################
# REMOTE RECEIVER SETTINGS GO HERE
##################################

#  dump:
#    - aeha         # AEHA infrared codes
#    - beo4         # B&O Beo4 infrared codes
#    - canalsat     # CanalSat infrared codes (56kHz)
#    - canalsatld   # CanalSatLD infrared codes (56kHz)
#    - coolix       # Coolix infrared codes
#    - dish         # Dish infrared codes (57.6kHz - many receivers won't decode)
#    - dyson        # Dyson Cool AM7 fan codes
#    - jvc          # JVC infrared codes
#    - gobox        # Go-Box infrared codes
#    - haier        # Haier infrared codes
#    - lg           # LG infrared codes
#    - magiquest    # MagiQuest wand infrared codes
#    - midea        # Midea infrared codes
#    - nec          # NEC infrared codes (most common - cheap/generic remotes)
#    - panasonic    # Panasonic infrared codes
#    - pioneer      # Pioneer infrared codes
#    - rc5          # RC5 infrared codes
#    - rc6          # RC6 infrared codes
#    - roomba       # Roomba infrared codes
#    - samsung      # Samsung infrared codes
#    - samsung36    # Samsung36 infrared codes
#    - symphony     # Symphony infrared codes
#    - sony         # Sony infrared codes
#    - toshiba_ac   # Toshiba AC infrared codes
#    - mirage       # Mirage infrared codes
#    - toto         # Toto infrared codes
#    - pronto       # Universal raw format - use if no protocol matches
#    - raw          # Raw timing dump - last resort fallback


# ----------------------------------------------------------
# Tolerance - how much signal timing can deviate before failing to decode
# Default: 25% - increase if signals are inconsistent or noisy
  tolerance: 25%
# Or use time based tolerance instead (comment out line above and uncomment below)
#  tolerance:
#    type: time
#    value: 500us  # microseconds (us) - 1000us = 1ms


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
# ESP32 / ESP32-S2:                65536us (~65ms)
# Other ESP32 variants (RMT):      32767us (~32ms)
# All other platforms (incl C2/C61): 4294967295us
  idle: 10ms

# ----------------------------------------------------------
# ID - manually specify an ID for this receiver
# Only needed if you have multiple receivers on the same device
# Example:
#  id: ir_receiver_1

# ----------------------------------------------------------
# ESP32 RMT HARDWARE SETTINGS
# Only applies to ESP32 variants with RMT hardware
# NOT available on ESP32-C2 and ESP32-C61
# Leave commented unless experiencing memory conflicts
# More info: https://esphome.io/components/remote_receiver/

# rmt_symbols - RMT hardware memory allocation
#  rmt_symbols: 64  # [uncomment to use]

# receive_symbols - maximum receive length in symbols
#  receive_symbols: 64  # [uncomment to use]

# filter_symbols - ignore received data shorter than this (filters noise bursts)
#  filter_symbols: 4  # [uncomment to use]

# clock_resolution - RMT peripheral clock speed in Hz
# Default: 1000000
#  clock_resolution: 1000000  # [uncomment to use]

# use_dma - enable Direct Memory Access (only on supported variants)
# If enabled rmt_symbols controls the DMA buffer size
#  use_dma: false  # [uncomment to use]


# ----------------------------------------------------------
# SIGNAL DEMODULATION
# Only needed if connecting a raw IR photodiode directly to ESP32
# Standard IR receiver modules (VS1838B etc) already demodulate the signal
# Most users will never need this - leave commented
# More info: https://esphome.io/components/remote_receiver/

# carrier_duty_percent - carrier duty cycle for demodulation (%)
# Default: 100
#  carrier_duty_percent: 100  # [uncomment to use]

# carrier_frequency - carrier frequency for demodulation in Hz
# Default: 0 (disabled)
#  carrier_frequency: 38000  # [uncomment to use] 38kHz is standard for most IR

##################################
# REMOTE TRANSMITTER GOES HERE
# https://esphome.io/components/remote_transmitter/
##################################

remote_transmitter:
  pin: GPIO26                 # replace with your receiver pin e.g. GPIO26
  carrier_duty_percent: 50%   # 50% for IR LEDs, 100% for RF (433MHz)

##################################
# BINARY SENSORS GO HERE
##################################

binary_sensor:

# DEVICE STATUS - confirms device is online in Home Assistant
binary_sensor:
  - platform: status
    name: "Status"

##################################
# BUTTONS GO HERE
##################################

button:

# BUILT IN BUTTONS
button:
  - platform: restart
    name: "Restart"

##################################
# AUTOMATIONS GO HERE
# https://esphome.io/components/remote_transmitter/#automations
##################################
```

captive_portal:
```
