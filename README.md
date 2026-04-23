# Mochi on ESP32-C3 + ST7789 76x284

Open-source Arduino sketches for running a Dasai-style Mochi character on an `ESP32-C3 Super Mini` with a narrow `ST7789P3 SPI` display module marked `76x284`.

This repo is built around one exact hardware setup and does not assume a different board, different controller, or different backlight logic.

## Hardware

Target hardware:

- `ESP32-C3 Super Mini`
- `ST7789P3` SPI TFT
- visible/module marking: `76x284`
- orientation used by these sketches: `landscape`
  - long side = left to right
  - short side = top to bottom

### Pinout

| Display pin | ESP32-C3 pin | Notes |
|---|---:|---|
| `BL`  | `GPIO 21` | active LOW backlight |
| `CS`  | `GPIO 10` | SPI chip select |
| `DC`  | `GPIO 8`  | data/command |
| `RST` | `GPIO 7`  | reset |
| `SDA` | `GPIO 6`  | SPI MOSI |
| `SCL` | `GPIO 4`  | SPI CLK |
| `VCC` | `3.3V`    | do not use 5V |
| `GND` | `GND`     | ground |

### Important electrical note

The display backlight is **active LOW**.

- `digitalWrite(21, LOW)` -> backlight ON
- `digitalWrite(21, HIGH)` -> backlight OFF

That is already hardware-tested and all sketches in this repo use that logic.

## Software

Target environment:

- `Arduino IDE`
- Espressif `ESP32` boards package
- `Adafruit GFX Library`
- `Adafruit ST7735 and ST7789 Library`

The sketches use custom SPI pin mapping:

```cpp
SPI.begin(4, -1, 6, 10);
```

That means:

- `SCLK = GPIO 4`
- `MISO = -1`
- `MOSI = GPIO 6`
- `SS   = GPIO 10`

## Sketches

### `watcher_mochi_st7789_esp32c3.ino`

Main sketch. This is the most advanced version in the repo right now.

Features:

- watcher-style Mochi animation set adapted for `ESP32-C3 + ST7789`
- centered face sized correctly for this narrow display
- white on black rendering
- procedural `PONG` mode
- side margin effects to use the empty space left and right of the face
- lightweight behavior engine instead of a simple repeating loop
  - boredom
  - curiosity
  - social need
  - mischief
  - sleep pressure
  - cooldowns
  - recent-action memory
  - serial interaction influence

Recommended if you want the most complete build.

### `autonomous_mochi_st7789_esp32c3.ino`

Procedural face engine version.

Features:

- smooth face rendering
- eye movement
- blinking
- expressions
- info/status panels

Recommended if you want a generated face style rather than preconverted watcher frames.

### `dasai_mochi_st7789_esp32c3.ino`

Dasai-style bitmap animation port adapted to this display.

Features:

- original-style Mochi frame animation
- centered and scaled for the `76x284` display
- simple hardware test flow

Recommended if you want the simpler original Dasai vibe.

## Arduino IDE setup

1. Install the Espressif `ESP32` board package.
2. Install:
   - `Adafruit GFX Library`
   - `Adafruit ST7735 and ST7789 Library`
3. Open one of the `.ino` files.
4. Select board:
   - `ESP32C3 Dev Module`
5. Select the correct serial port.
6. Use a conservative upload speed first:
   - `115200`

## Upload notes for ESP32-C3

This build uses `GPIO8` for `DC`. On `ESP32-C3`, `GPIO8` is a strapping pin, so flashing can sometimes be more sensitive than normal.

If upload fails:

1. Close `Serial Monitor`.
2. Press `Upload`.
3. When the IDE shows `Connecting...`, hold `BOOT`.
4. Tap `RST/EN`.
5. Release `BOOT` after a second or two.

If it is still unreliable:

- try a different USB cable
- try another USB port
- keep upload speed at `115200`
- temporarily disconnect the display `DC` line from `GPIO8` during flashing if needed

## Serial commands

The main watcher sketch supports commands from `Serial Monitor` at `115200`.

Commands:

```text
help
list
next
idle
brain
smile
happy
left
right
wink
sleepy
laughing
surprised
angry
squint
hello
dancing
pong
```

`brain` prints the current internal behavior state so you can tune the character.

## Behavior model

The latest watcher sketch is not driven only by random animation selection.

It uses a lightweight behavior system with:

- internal drives
- cooldown timers
- recent-history penalties
- idle-time reactions
- serial-driven reactions

That gives the character more believable behavior on limited embedded hardware without trying to run a large AI model on the ESP32-C3.

## Display assumptions

These sketches are tuned for:

- `TFT_ROTATION = 1`
- `TFT_INIT_WIDTH = 76`
- `TFT_INIT_HEIGHT = 284`
- `landscape 284x76 visible layout`

If your module is electrically similar but offset differently, the init size, rotation, or offsets may need small changes.

## Credits

This repo builds on ideas and assets adapted from:

- `upiir/esp32s3_oled_dasai_mochi`
  - https://github.com/upiir/esp32s3_oled_dasai_mochi
- `pham-tuan-binh/watcher-mochi`
  - https://github.com/pham-tuan-binh/watcher-mochi

Original character/art ownership belongs to its respective creators. This repo is intended as a personal/open hardware style adaptation for embedded display projects.

## Status

What is already solved in this repo:

- exact ESP32-C3 pin mapping
- active-LOW backlight handling
- correct centered layout for this narrow display
- stable landscape orientation
- multiple Mochi variants for the same hardware

What may still be tuned further:

- expression timing
- behavior weights
- time-aware behavior via Wi-Fi/NTP
- extra sensor-based reactions
