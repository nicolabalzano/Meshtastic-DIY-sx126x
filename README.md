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
