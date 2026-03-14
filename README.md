# 📡 DIY Meshtastic Node: ESP32 + SX126x (Ebyte E22)

This repository contains the custom firmware build and hardware guide for creating a DIY [Meshtastic](https://meshtastic.org/) node using a standard **ESP32 DevKit** and an **Ebyte E22-900M22S** (SX1262/SX1261) SPI LoRa module. 

Building your own node is a great way to learn about LoRa mesh networks and customize your hardware exactly how you want it!

## 🛠️ Components List

### 🛑 Required Components
* **ESP32 Development Board** (e.g., ESP32 WROOM-32 DevKit V1)
* **LoRa Module:** Ebyte E22-900M22S (or similar bare SPI SX1262/SX1261 module)
* **LoRa Antenna:** 868MHz or 915MHz depending on your region (⚠️ **NEVER transmit without the antenna attached!**)

### 🟢 Optional Components
* **Power Supply:** 18650 Battery + 18650 Battery Shield (with built-in TP4056 charger and 3.3V/5V regulator)
* **OLED Display:** 0.96" SSD1306 I2C OLED screen (SDA: 21, SCL: 22).

---

## 🔌 Circuit & Wiring Tutorial

### 📡 LoRa Module (Ebyte E22)
The Ebyte E22 module requires specific pins to handle SPI communication, the TCXO oscillator, and RF switching (RXEN/TXEN). 

*Note: The pin numbers on the Ebyte module are counted from the top (near the antenna connector) going downwards.*

| Ebyte E22 Pin | Pin Name | ESP32 GPIO | Notes |
| :--- | :--- | :--- | :--- |
| 1 | GND | **GND** | Connect to ESP32 Ground |
| 2 | VCC | **3.3V** | ⚠️ MUST be 3.3V (Do not use 5V!) |
| 3 | MISO | **GPIO 19** | SPI MISO |
| 4 | MOSI | **GPIO 23** | SPI MOSI |
| 5 | SCK | **GPIO 18** | SPI Clock |
| 6 | NSS | **GPIO 5** | SPI Chip Select (CS) |
| 7 | NRST | **GPIO 26** | Reset pin |
| 8 | DIO1 | **GPIO 33** | IRQ pin |
| 9 | BUSY | **GPIO 32** | Busy state indicator |
| 10 | DIO2 | *Not Connected* | |
| 13 | RXEN | **GPIO 13** | RF Switch Receive Enable |
| 14 | TXEN | **GPIO 25** | RF Switch Transmit Enable |

### 📺 OLED Display (Optional)
If you are adding a 0.96" I2C OLED display to see messages and node status without the app:

| OLED Pin | ESP32 GPIO | Notes |
| :--- | :--- | :--- |
| GND | **GND** | Connect to ESP32 Ground |
| VCC | **3.3V** | Power for the display |
| SCL | **GPIO 22** | I2C Clock |
| SDA | **GPIO 21** | I2C Data |

---

### 🔋 Battery Note (Optional)
If you decide to make it portable with an 18650 battery, **do not connect the battery directly to the ESP32 3.3V pin**. A fully charged 18650 outputs 4.2V, which will damage both the ESP32 and the LoRa module. 
Instead, use a dedicated **18650 Battery Shield** that outputs a safe 3.3V/5V, or connect the battery to the `VIN` / `5V` pin of the ESP32 (only if your specific ESP32 board has a built-in voltage regulator that safely steps down 3.7V-4.2V to 3.3V).

---

## 💻 Building and Flashing the Firmware

To build the firmware for this custom wiring, we will use [PlatformIO](https://platformio.org/) and create a custom variant inside the official Meshtastic firmware repository.

### Step 1: Clone the Meshtastic Repository
First, clone the official Meshtastic firmware and enter the directory:
```bash
git clone https://github.com/meshtastic/firmware.git
cd firmware
```

### Step 2: Create the Custom Variant
Create a new directory for our custom hardware variant:
```bash
mkdir -p variants/esp32/diy_ebyte_e22
```

Create and open the `variant.h` file:
```bash
nano variants/esp32/diy_ebyte_e22/variant.h
```

Paste the following configuration into `variant.h`:
```c
#pragma once
#define USE_SX1262  // Works for both SX1261 and SX1262

// ─── SPI / LoRa ───────────────────────────────────────────
#define LORA_SCK   18
#define LORA_MISO  19
#define LORA_MOSI  23
#define LORA_CS    5

// ─── SX126x control ───────────────────────────────────────
#define SX126X_CS       LORA_CS
#define SX126X_RESET    26   // ← CHANGED FROM 14 TO 26!
#define SX126X_BUSY     32
#define SX126X_DIO1     33

// ─── RF Switch (Ebyte E22) ────────────────────────────────
#define SX126X_RXEN     13
#define SX126X_TXEN     25

// ─── TCXO ─────────────────────────────────────────────────
#define SX126X_DIO3_TCXO_VOLTAGE 1.8

// ─── OLED I2C ─────────────────────────────────────────────
#define I2C_SDA   21
#define I2C_SCL   22
#define USE_SSD1306

// ─── Boot Button ──────────────────────────────────────────
#define BUTTON_PIN 0

// ─── Hardware model DIY ───────────────────────────────────
#define HW_VENDOR meshtastic_HardwareModel_PRIVATE_HW
```

### Step 3: Update `platformio.ini`
Open `platformio.ini` in your root folder:
```bash
nano platformio.ini
```

Scroll to the very bottom and append our new build environment:
```ini
[env:diy_ebyte_e22]
extends = esp32_base
board = esp32dev
monitor_filters = esp32_exception_decoder
build_flags =
    ${esp32_base.build_flags}
    -D PRIVATE_HW
    -I variants/esp32/diy_ebyte_e22
lib_deps = ${esp32_base.lib_deps}
```

### Step 4: Build and Flash
Connect your ESP32 to your computer via USB and run the following PlatformIO command to compile and upload the firmware:

```bash
pio run -e diy_ebyte_e22 --target upload
```
*(If your USB port isn't automatically detected, you can specify it by adding `--upload-port /dev/ttyUSB0` or your specific COM port).*

### Step 5: Verify the Boot
Once the flashing is complete, open the serial monitor to verify that the radio initializes properly:
```bash
pio device monitor -e diy_ebyte_e22 -b 115200
```
Look for the `[INFO] Radio initialized` message. If you see it, everything is working perfectly! 🎉
