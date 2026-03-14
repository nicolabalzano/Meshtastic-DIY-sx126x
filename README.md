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
* **User Button:** A tactile push button connected to `GPIO 0` to interact with the device screen.
* **OLED Display:** 0.96" SSD1306 I2C OLED screen (SDA: 21, SCL: 22).

---

## 🔌 Circuit & Wiring Tutorial

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
| 7 | NRST | **GPIO 26** | Reset (*Changed from GPIO14 to prevent boot loops*) |
| 8 | DIO1 | **GPIO 33** | IRQ pin |
| 9 | BUSY | **GPIO 32** | Busy state indicator |
| 10 | DIO2 | *Not Connected* | |
| 13 | RXEN | **GPIO 13** | RF Switch Receive Enable |
| 14 | TXEN | **GPIO 25** | RF Switch Transmit Enable |

### 🔋 Battery Note (Optional)
If you decide to make it portable with an 18650 battery
