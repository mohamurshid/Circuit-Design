# ICS 4111: Embedded Systems & IoT
## Semester Project — Deliverable 1
### Flora Farms Greenhouse Monitoring System — Tulip Unit

**Group Members:**
* 169004  Mohammed Ilhan Hamud.
* 169962	Abdalla Muhammad Murshid.
* 154210	Elvis Wafuke.
* 159540  Wanjohi Joy Wambui.
* 150851	Ndeti Melissa Mwende.
* 156740	Gitonga Benson Kamau.

## Project Background

**Client:** Flora Farms, Naivasha, Kenya

**Stakeholder:** Sheila (Farm Owner), based in Nairobi

**Objective:** Design and develop a Wi-Fi-enabled embedded monitoring device for remote greenhouse oversight, ensuring optimal tulip growth conditions are maintained at all times.

Flora Farms operates solar-powered greenhouses in Naivasha, each dedicated to a specific flower type. This group has been assigned the **tulip greenhouse**. The monitoring device developed by this team will:

- Continuously measure environmental parameters critical to tulip growth
- Detect LPG gas levels from the greenhouse heating system (leak monitoring)
- Transmit data over the greenhouse Wi-Fi network to a cloud platform
- Enable Sheila and farm staff to remotely monitor conditions from the Nairobi office

**Power Supply Context:** Each greenhouse runs on a 200W solar panel with a 12V, 100Ah battery bank. All embedded devices must operate within this power budget. The ESP32 will be powered from the 12V battery via a buck converter (stepped down to 5V for the ESP32 VIN pin), making low-power design a consideration for future firmware.

**Communication:** Greenhouses are fitted with Wi-Fi routers. The ESP32S's built-in Wi-Fi (802.11 b/g/n) will connect to this network to push sensor data to a cloud platform (to be implemented in a later deliverable).

---

## 1. Flower Growth Environmental Requirements

**Assigned Flower: Tulip (*Tulipa* spp.)**

Tulips are cool-season bulb flowers native to Central Asia and are among the most widely grown ornamental flowers in the world. They have precise temperature and drainage requirements, making them an excellent subject for embedded environmental monitoring.

The table below summarises the optimal environmental parameters required for healthy tulip growth. These measurements will serve as reference thresholds for the embedded monitoring system.

| # | Parameter | Optimal Range / Value | Notes |
|---|-----------|----------------------|-------|
| 1 | **Temperature (Day)** | 15°C – 18°C |Night temperature: 5°C – 9°C. Above 20°C causes bud blast, weak stems and tulip topple disorder. The LPG heater covers cold nights; vents and fans manage hot afternoons.|
| 2 | **Relative Humidity (Air)** | 65% – 75% RH | Below 60% RH causes petal desiccation; above 75% RH triggers Botrytis tulipae (tulip fire). Tulips prefer a dry environment, active dehumidification is recommended over ventilation.|
| 3 | **Soil Type** | Well-draining light sandy loam with organic matter | Heavy clay causes bulb rot. Raised beds strongly recommended. Amend with compost or peat moss for structure. Avoid waterlogged conditions entirely |
| 4 | **Soil Moisture Content** | 21% – 40% volumetric water content. | Soil must never be soggy, bulbs rot quickly in excess moisture. Allow surface to slightly dry between watering. Normal rainfall usually sufficient after planting |
| 5 | **Soil pH Range** | 6.0 – 7.0 (slightly acidic to neutral) | Optimum is 6.0–6.5. Acidic or overly alkaline soil prevents bulb root development and nutrient uptake |
| 6 | **Sunlight Exposure** | 6 – 8 hours/day of bright direct sunlight | Full sun is ideal. Partial afternoon shade (noon–4 PM) is beneficial in warmer climates to extend bloom longevity and prevent petal scorching |

**Sources:**
- [Agriculture Institute – Growing Tulips: Climate Requirements](https://agriculture.institute/floriculture-and-landscaping/growing-tulips-varieties-propagation-climate/)
- [White Flower Farm – Planting & Growing Tulips](https://www.whiteflowerfarm.com/how-to-grow-tulips)
- [Ruigrok Flower Bulbs – Forcing Tulips Guide (PDF)](https://ruigrokflowerbulbs.com/wp-content/uploads/2020/04/Forcing-tulips.pdf)
- [Kellogg Garden – Gardener's Guide to Planting Tulips](https://kellogggarden.com/blog/gardening/gardeners-guide-to-planting-tulips/)
- [Greg App – Tulip Humidity Requirements](https://greg.app/garden-tulip-humidity/)
- [DryGair – Ideal Conditions for Greenhouse Tulips](https://drygair.com/blog/greenhouse-tulips/)

---

## 2. Hardware Components List

The following components are required to build an embedded IoT device that monitors all the environmental metrics listed in Section 1, plus LPG gas detection (methane/butane/propane). All prototyping tools are included.


### 2.1 Microcontrollers & Compute

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 1 | ESP32S DevKIT WIFI + BLE Module (30-Pin) | 2 | Main microcontroller; reads sensors, processes data, transmits wirelessly. Two units required for multi-node architectures (Designs b & c) |

### 2.2 Sensors

| # | Component | Qty | Purpose | Metric Measured |
|---|-----------|-----|---------|----------------|
| 2 | DHT22 (AM2302) Temperature & Humidity Sensor | 1 | Measures ambient air temperature and relative humidity | Parameters 1 & 2 |
| 3 | MQ-5 LPG/Natural Gas/Coal Gas Sensor | 1 | Detects and measures LPG (methane, butane, propane) concentration | LPG/Gas levels |
| 4 | Capacitive Soil Moisture Sensor (e.g., v1.2) | 1 | Measures volumetric soil moisture content | Parameter 4 |
| 5 | Soil pH Sensor Module | 1 | Measures soil pH level | Parameter 5 |
| 6 | BH1750 Light Intensity Sensor (Lux) or LDR module | 1 | Measures ambient light intensity/duration | Parameter 6 |

 
### 2.3 Power Supply

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 7 | LM2596 Step-Down Buck Converter (12V → 5V, 3A) | 1 | Steps down the greenhouse 12V battery to 5V for the ESP32 VIN pin. Required for field deployment. USB adapters are used only during lab prototyping |

### 2.4 Output / Display

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 8 | 1.3" White IIC 128×64 OLED LCD (SH1106) | 1 | Displays real-time sensor readings locally |

### 2.5 Control / Actuation

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 9 | 5V 1-Channel Low Level Trigger Relay Module | 1 | Acts as a signal switch/interface between ESP32 nodes; can also control an actuator (e.g., irrigation pump, fan) based on sensor thresholds |

### 2.6 Passive Electronic Components

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 10 | 10kΩ Resistor | 5 | Pull-up/pull-down resistors for DHT22 data line, voltage dividers |
| 11 | 4.7kΩ Resistor | 2 | Pull-up resistor for I2C bus (SDA/SCL lines for OLED, pH sensor) |
| 12 | 470Ω Resistor | 4 | Series protection resistors on UART TX lines in Design B |
| 13 | 100nF (0.1µF) Capacitor | 3 | Decoupling/bypass capacitor for DHT22 VDD, MQ-5 power rail, and OLED VCC |
| 14 | 10µF Electrolytic Capacitor | 2 | Power rail stabilisation for ESP32 and relay module |
| 15 | 1N4007 Flyback Diode | 1 | Protects ESP32 GPIO from inductive relay kickback |
| 16 | LED (Red/Green) | 2 | Visual status indicators |
| 17 | 220Ω Resistor | 2 | Current-limiting resistors for status LEDs |

### 2.7 Prototyping Tools

| # | Component | Qty | Purpose |
|---|-----------|-----|---------|
| 18 | Full-size Breadboard (830 tie-points) | 2 | Circuit prototyping without soldering |
| 19 | Mini Breadboard (400 tie-points) | 1 | Secondary sensor node prototyping |
| 20 | Male-to-Male Jumper Wires (set of 40) | 2 sets | Connecting components on breadboard |
| 21 | Male-to-Female Jumper Wires (set of 40) | 1 set | Connecting sensor modules to breadboard |
| 22 | Female-to-Female Jumper Wires (set of 20) | 1 set | Connecting sensor modules with female headers (DHT22, soil sensors) directly together |
| 22 | USB Micro-B Cable | 2 | Power supply and programming for each ESP32 |
| 23 | 5V USB Power Adapter (≥2A) | 2 | Power supply for each ESP32 node |
| 24 | Multimeter | 1 | Voltage and continuity testing during prototyping |

---

## 3. Component Datasheets

The following datasheets and technical reference documents apply to the five components listed in the assignment.

### 3a. 1.3" White IIC 128×64 OLED LCD (SH1106 Driver)

- **Driver IC:** SH1106 (SSD1306-compatible)
- **Interface:** I2C (IIC) — 4 pins: VCC, GND, SCL, SDA
- **Operating Voltage:** 3.3V – 5V DC
- **Resolution:** 128 × 64 pixels
- **Current Draw:** ~23mA at full brightness
- **Operating Temperature:** –40°C to 85°C

**Datasheet/Reference Links:**
- [SH1106 OLED 1.3" Datasheet — Waveshare](https://www.waveshare.com/w/upload/e/e3/1.3inch-SH1106-OLED.pdf)
- [Product Description & Pinout — Faranux Electronics](https://www.faranux.com/product/1-3-inch-oled-white-display-128x64-i2c/)
- [Display Module Specifications — displaymodule.com](https://www.displaymodule.com/products/1-3-inch-oled-graphic-display-128x64-with-spi-i2c)

---

### 3b. ESP32S DevKIT WIFI + BLE Module (30-Pin)

- **Chip:** Espressif ESP32-WROOM-32 (Dual-core Xtensa LX6)
- **Clock Speed:** Up to 240 MHz
- **Flash Memory:** 4 MB
- **GPIO Pins:** 25 exposed (30-pin board layout, 15 per side)
- **Wireless:** Wi-Fi 802.11 b/g/n + Bluetooth 4.2 (BR/EDR + BLE)
- **ADC:** 12-bit, 18 channels
- **Operating Voltage:** 3.3V (logic); 5V via VIN/USB
- **Interfaces:** UART, SPI, I2C, I2S, PWM
- **Programming:** Arduino IDE, ESP-IDF (USB via CH340 driver)

**Datasheet/Reference Links:**
- [ESP32 Official Datasheet — Espressif Systems](https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf)
- [DOIT ESP32 DevKit V1 Pinout & Specs — espboards.dev](https://www.espboards.dev/esp32/esp32doit-devkit-v1/)
- [GPIO Reference Guide — Last Minute Engineers](https://lastminuteengineers.com/esp32-pinout-reference/)
- [GitHub Pin Reference — TronixLab](https://github.com/TronixLab/DOIT_ESP32_DevKit-v1_30P)

---

### 3c. DHT22 AM2302 Temperature and Humidity Sensor

- **Measurement Range (Humidity):** 0% – 100% RH
- **Humidity Accuracy:** ±2% RH
- **Measurement Range (Temperature):** –40°C to +80°C
- **Temperature Accuracy:** ±0.5°C
- **Operating Voltage:** 3.3V – 5.5V DC
- **Interface:** Single-wire digital protocol (1-Wire-like, NOT Dallas 1-Wire)
- **Sampling Rate:** Once every 2 seconds (0.5 Hz)
- **Pull-up Resistor Required:** 10kΩ on the DATA line

**Datasheet/Reference Links:**
- [AM2302 Official Datasheet — Adafruit CDN (Aosong)](https://cdn-shop.adafruit.com/datasheets/Digital+humidity+and+temperature+sensor+AM2302.pdf)
- [DHT22 Datasheet PDF — Sparkfun CDN](https://cdn.sparkfun.com/assets/f/7/d/9/c/DHT22.pdf)
- [User Manual AM2302 (ST1173) — Conrad/Reichelt](https://cdn-reichelt.de/documents/datenblatt/B300/ST1173.pdf)

---

### 3d. MQ-5 LPG, Natural Gas, Coal Gas Sensor

- **Target Gases:** LPG (propane, butane), natural gas (methane), town/coal gas
- **Sensitive Material:** SnO2 (tin dioxide) — resistance decreases as gas concentration rises
- **Operating Voltage:** VCC = 5V DC; Heater Voltage VH = 5V
- **Detection Range:** 200 – 10,000 ppm (LPG/methane)
- **Output:** Analog voltage (0–5V, proportional to gas concentration) + Digital TTL
- **Warm-up Time:** ≥48 hours for first use; ~20 seconds for subsequent reads
- **Load Resistance (RL):** 20kΩ recommended (adjustable 10kΩ – 47kΩ)
- **Pins:** 6 pins (4 signal, 2 heater)



**Datasheet/Reference Links:**
- [MQ-5 Official Datasheet — Winsen Electronics](https://www.winsen-sensor.com/d/files/MQ-5.pdf)
- [MQ-5 Technical Data — Seeedstudio CDN](https://files.seeedstudio.com/wiki/Grove-Gas_Sensor-MQ5/res/MQ-5.pdf)
- [MQ-5 Waveshare User Manual](https://www.waveshare.com/w/upload/7/76/MQ-5-Gas-Sensor-UserManual.pdf)

---

### 3e. 5V 1-Channel Low Level Trigger Relay Module

- **Operating Voltage:** 5V DC (control side)
- **Trigger Type:** Active LOW (relay activates when IN pin is pulled to 0V/GND)
- **Trigger Current:** 15–20 mA
- **Idle Current:** ~4 mA
- **Maximum Load (Output):** AC 250V / 10A or DC 30V / 10A
- **Isolation:** Optocoupler isolation between control (MCU) side and load side
- **Protection:** Flyback diode included on-board (protects coil)
- **Indicator:** LED shows relay status
- **Contact Type:** SPDT — Common (COM), Normally Open (NO), Normally Closed (NC)

**Datasheet/Reference Links:**
- [1-Channel Relay Module Datasheet — Handsontec](https://handsontec.com/dataspecs/relay/1Ch-relay.pdf)
- [5V Low Level Trigger Relay Specifications — Scribd](https://www.scribd.com/document/427360534/5V-low-level-trigger-One-1-Channel-Relay-pdf)
- [HW-307B Relay Module — ThinkRobotics](https://thinkrobotics.com/products/1-channel-relay-module-shield-5v)

---

## 4. Schematic Diagrams

The schematics below describe three different circuit architectures using the components listed in Section 3. Each design uses proper passive components (resistors, capacitors, voltage dividers) to ensure circuit safety and signal integrity.

---

### 4a. Design A: 1 ESP32S + 1 MQ-5 + 1 DHT22 + 1 LCD


![Schematic A]("/Images/Design A.png")


### 4b. Design B: ESP32S-A ↔ MQ-5 ←→ ESP32S-B ↔ DHT22 (Direct Serial)


![Schematic A]("/Images/Design B.jpg")


### 4c. Design C: ESP32S-A ↔ DHT22 → Relay → ESP32S-B ↔ MQ-5


![Schematic A]("/Images/Design C.png")

