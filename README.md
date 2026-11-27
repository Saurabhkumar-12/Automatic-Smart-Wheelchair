# ♿ Automatic and Health Detecting Wheelchair

The **Automatic and Health Detecting Wheelchair** is a low-cost, IoT-enabled assistive mobility prototype designed to provide flexible drive control alongside real-time vital-sign monitoring for elderly individuals, patients, and people with physical disabilities. Built around a **split-controller architecture** using an **Arduino Nano** and a **NodeMCU ESP8266**, the system separates motor control from wireless networking and health sensing so that network or sensor delays never interfere with manual driving.

Users and caregivers can operate the wheelchair through **three independent control interfaces**—hard-wired **push buttons** (highest priority), a **Bluetooth smartphone app (HC-05)**, and a self-hosted **Wi-Fi web dashboard**—while continuously monitoring **heart rate**, **blood-oxygen saturation (SpO₂)** via a **MAX30102** sensor, and **body temperature** via an **LM35** sensor without requiring a router or internet connection.

---

# ✨ Features

- 🦽 **Triple Control System:** Hard-wired Push Buttons (Highest Priority) + HC-05 Bluetooth App + Self-Hosted Wi-Fi Web Dashboard
- ❤️ **Heart Rate & SpO₂ Monitoring:** Optical pulse oximetry using the **MAX30102** sensor with clinical-style 5-sample rolling averaging
- 🌡️ **Body Temperature Monitoring:** Precision analog sensing using the **LM35** sensor
- 🌐 **Offline Self-Hosted Web Dashboard:** NodeMCU operates in Soft-AP mode (`Wheelchair_Health` at `192.168.4.1`)—no external router or internet required
- 🛡️ **Layered Failsafe & Priority Logic:** Absolute physical button override, latched remote command clearing, 300 ms dashboard heartbeat, and a 700 ms lost-link auto-stop failsafe
- ⚡ **Split-Controller Isolation:** Dedicated **Arduino Nano** for deterministic motor control and **NodeMCU ESP8266** for health sensing and HTTP server hosting
- 🚦 **Color-Coded Vitals Status Badges:** Live dashboard indicators (*Normal*, *Check/Low*, *Critical*, *No Data*) updated every 2 seconds
- 🔋 **Dual-Supply Power Architecture:** Isolated motor battery pack and regulated 5V/3.3V logic power with a shared common ground

---

# 🏗️ System Architecture

```text
          DRIVE SUBSYSTEM                                 HEALTH + WEB SUBSYSTEM

  ┌───────────────┐   ┌───────────────┐             ┌───────────────┐   ┌───────────────┐
  │   Phone App   │   │ Push Buttons  │             │   MAX30102    │   │     LM35      │
  │  (Bluetooth)  │   │  (A1 – A4)    │             │ SpO2 + HR     │   │  Body Temp    │
  └───────┬───────┘   └───────┬───────┘             └───────┬───────┘   └───────┬───────┘
          │ Bluetooth         │ Hard-wired                  │ I2C (D1/D2)       │ Analog (A0)
          ▼                   │                             ▼                   ▼
  ┌───────────────┐           │                     ┌───────────────────────────────────┐
  │     HC-05     │           │                     │         NodeMCU ESP8266           │
  │   Bluetooth   │           │                     │ Wi-Fi Access Point + Web Server   │
  └───────┬───────┘           │                     └─────────┬─────────────────┬───────┘
          │ D2 / D3           │                               │                 │
          ▼                   ▼                               │                 │ Wi-Fi / HTTP
  ┌───────────────────────────────────┐     TX > D0 (RX)      │                 ▼
  │           Arduino Nano            │◄──────────────────────┘         ┌───────────────┐
  │    Drive Controller (ATmega328P)  │       9600 baud                 │ Phone / PC    │
  └─────────────────┬─────────────────┘        (1-way)                  │ Web Dashboard │
                    │ D5 – D10 (PWM & Dir)                              └───────────────┘
                    ▼
  ┌───────────────────────────────────┐
  │        L298N Motor Driver         │
  └─────────────────┬─────────────────┘
                    │
                    ▼
  ┌───────────────────────────────────┐
  │       DC Geared Motors (x2)       │
  └───────────────────────────────────┘
```

---

# 🔄 System Workflow

```text
Power ON (Motors & Latched State Initialized to STOP)
    │
    ├──────────────────────────────────────────┐
    ▼                                          ▼
[Arduino Nano: Drive Loop]              [NodeMCU ESP8266: Health & Web Loop]
    │                                          │
    ▼                                          ▼
Read Physical Push Buttons (A1–A4)      Sample MAX30102 (100 samples @ 25 Hz) & LM35
    │                                          │
    ├── Button Held? ──► YES                   ├── Filter Plausibility (HR 30–220, SpO2 70–100%)
    │                     │                    ├── Update 5-Sample Rolling Average
    │                     ▼                    └── Serve Web Dashboard & /data JSON (192.168.4.1)
    │            Drive per Button                      │
    │            Clear Latched Command                 │ Dashboard Hold (300 ms heartbeat)
    │            Ignore Remote Inputs                  ▼
    │                                          Forward Command ('F','B','L','R','S') via UART
    └── NO ──► Read HC-05 & NodeMCU UART ◄─────────────┘
                    │
                    ▼
          NodeMCU/USB Silent > 700 ms?
            ├── YES ──► Failsafe STOP
            └── NO  ──► Drive Motors per Latched Command (PWM 120/255)
```

---

# 🧩 Hardware Components

| Component | Qty | Purpose | Key Specifications |
| :--- | :---: | :--- | :--- |
| **Arduino Nano** | 1 | Dedicated Drive Controller | ATmega328P, 5V logic, 16 MHz, Hardware UART + `SoftwareSerial` |
| **NodeMCU ESP8266 (ESP-12E)** | 1 | Health Sensing, Wi-Fi AP & Web Server | 3.3V logic, 80 MHz, 802.11 b/g/n, 10-bit ADC (`A0`) |
| **HC-05 Bluetooth Module** | 1 | Wireless Smartphone App Control | Bluetooth 2.0 + EDR, SPP, 9600 baud |
| **MAX30102 Breakout** | 1 | Heart Rate & SpO₂ Sensing | Red (~660 nm) + IR (~880 nm) LEDs, 18-bit ADC, I2C (`0x57`) |
| **LM35 Temperature Sensor** | 1 | Body Temperature Sensing | Analog output (10 mV/°C), 4–30V supply |
| **L298N Motor Driver** | 1 | Dual H-Bridge Motor Control | Up to 2A/channel, PWM speed control (`ENA`/`ENB`) |
| **DC Geared Motors** | 2 | Left & Right Wheel Drive | Geared traction motors matched to chassis & battery |
| **Momentary Push Buttons** | 4 | Hard-Wired Manual Override | Wired to `A1`–`A4` (`INPUT_PULLUP`, active `LOW`) |
| **Resistors (1 kΩ & 2 kΩ)** | 1 each | Logic Level Voltage Divider | Steps down Nano 5V TX to ~3.3V for HC-05 RXD |
| **Dual Battery / Power Supply** | 1 + 1 | Isolated Motor & Logic Power | Dedicated motor battery pack + 5V USB/regulated logic supply |

---

# 🛠️ Technology Stack

## Embedded Firmware & Microcontrollers
- **Controllers:** Arduino Nano (ATmega328P) & NodeMCU ESP8266 (ESP-12E)
- **IDE & Language:** Arduino IDE, Embedded C/C++
- **Libraries:** `SoftwareSerial`, `ESP8266WiFi`, `ESP8266WebServer`, `Wire`, `SparkFun MAX3010x` (`MAX30105.h`, `spo2_algorithm.h`)

## Sensors & Actuators
- **Biometric Sensors:** MAX30102 Pulse Oximeter & Heart-Rate Sensor, LM35 Precision Analog Temperature Sensor
- **Motor Drive:** L298N Dual H-Bridge Driver, Dual DC Geared Motors (fixed safe PWM duty `120/255`)

## Communication & Interface
- **Wi-Fi Access Point & Web UI:** Self-hosted responsive HTML/CSS/JS dashboard (`SSID: Wheelchair_Health`, IP: `192.168.4.1`) with REST endpoints (`/`, `/data`, `/cmd?dir=X`)
- **Bluetooth & Inter-MCU Link:** HC-05 Serial Port Profile (9600 baud) and one-way 9600-baud UART link (NodeMCU `TX` → Nano `D0/RX`)

---

# 📂 Project Structure

```text
Automatic-Health-Detecting-Wheelchair/
│
├── Firmware/
│   ├── Drive_Controller_Nano/        # Arduino Nano motor, button priority & failsafe code
│   └── Health_Web_NodeMCU/           # ESP8266 MAX30102/LM35 sampling & self-hosted web server
├── Hardware/
│   ├── Pinout_and_Wiring.md          # Nano, NodeMCU, L298N, HC-05, MAX30102 & LM35 pin mappings
│   └── BOM.csv                       # Complete Bill of Materials
├── Documentation/
│   └── Project_Final_Report.pdf      # B.Tech ECE Final Report (PSIT Kanpur, Group G-12)
├── Images/
│   ├── Prototype_Assembly.jpg        # Assembled wheelchair with armrest controls & sensor pod
│   └── Web_Dashboard_UI.png          # Mobile dashboard interface screenshot
└── README.md
```

---

# 🔐 Safety Features

- **Absolute Physical Button Priority:** Holding any armrest push button (`A1`–`A4`) immediately overrides and rejects all Bluetooth and Wi-Fi commands (precedence: *Forward > Backward > Left > Right*).
- **Latched Command Clearing:** Pressing any physical button clears the active remote command to `Stop`, preventing unexpected movement when the button is released.
- **Press-and-Hold Web Control + 700 ms Failsafe:** The dashboard sends a direction heartbeat every **300 ms** while held and `Stop` on release; if Wi-Fi drops or the browser closes, the Nano automatically halts the motors within **700 ms**.
- **Safe Power-Up State & Speed Limiting:** Motor pins and command states initialize to `Stop` on boot, with PWM capped at `120/255` (~47% duty) for smooth, controllable indoor/prototype speeds.
- **Hardware Isolation & Command Validation:** One-way serial wiring protects the 3.3V NodeMCU from 5V Nano signals, and only valid single-character commands (`F`, `B`, `L`, `R`, `S`) are executed.

> **Note:** This system is an academic proof-of-concept prototype and is not a certified medical or clinical mobility device.

---

# 📡 Functional Modules

| Control Source / Endpoint | Interface | Behavior & Protocol | Failsafe / Refresh |
| :--- | :--- | :--- | :--- |
| **Push Buttons (`A1`–`A4`)** | Hard-wired (`INPUT_PULLUP`) | Highest priority; momentary movement only while held | Physical (instant stop on release) |
| **Web Dashboard (`/cmd?dir=X`)** | Wi-Fi Soft-AP (`192.168.4.1`) | Press-and-hold directional pad (`F`, `B`, `L`, `R`, `S`) | **300 ms** heartbeat / **700 ms** auto-stop |
| **Bluetooth App (`HC-05`)** | SoftwareSerial (`D2`/`D3`, 9600 baud) | Single-character commands (`F`, `B`, `L`, `R`, `S`); latched until changed | Manual `S` or physical button override |
| **Health Telemetry (`/data`)** | HTTP `GET` (JSON) | Returns `hr`, `spo2`, `temp`, and `finger` presence flag | Polled every **2 s**; 5-sample rolling average |

---

# 🚀 Future Enhancements

- **Obstacle & Drop-Off Detection:** Integrate ultrasonic or Time-of-Flight (ToF) sensors to automatically halt the chair near obstacles or stairs.
- **Authentication & Bluetooth Heartbeat:** Add per-unit WPA2 credentials, dashboard PIN protection, and continuous Bluetooth keep-alive packets.
- **Higher-Resolution Temperature & Biometrics:** Upgrade to an external 16-bit ADC (e.g., ADS1115) or digital clinical-grade temperature sensor.
- **Emergency Caregiver Alerts & Cloud Logging:** Trigger automated SMS/push alerts for critical vitals and log historical health trends to an SD card or cloud dashboard.
- **Proportional Joystick, Fall Detection & GPS:** Add an analog proportional joystick, accelerometer-based tilt/fall detection, GPS tracking, and battery voltage telemetry.

---

# 📸 Project Images

Include images of:
- **Complete Prototype:** Dual-armrest wheelchair assembly with ergonomic button and sensor placement
- **Armrest Control Panel:** 4-way tactile push-button array (`Forward`, `Backward`, `Left`, `Right`)
- **Armrest Health Pod:** Integrated MAX30102 optical sensor and LM35 temperature sensor module
- **Mobile Web Dashboard:** Dark-themed live vitals card view (`Heart Rate`, `SpO2`, `Body Temp`) and touch drive pad

---

# ⚙️ Getting Started

```bash
# 1. Clone the repository
git clone <repository-url>

# 2. Configure Arduino IDE
# - Install "ESP8266 Boards" via Boards Manager (select: NodeMCU 1.0 (ESP-12E Module))
# - Install "SparkFun MAX3010x Pulse and Proximity Sensor Library" via Library Manager

# 3. Upload Firmware to Arduino Nano
# - Disconnect NodeMCU TX from Nano D0 and HC-05 wires before uploading
# - Select Board: "Arduino Nano" (use "ATmega328P (Old Bootloader)" if required) and upload

# 4. Upload Firmware to NodeMCU ESP8266
# - Select Board: "NodeMCU 1.0 (ESP-12E Module)" and upload
# - Reconnect NodeMCU TX -> Nano D0 and verify common GND across all modules

# 5. Connect & Operate
# - Power on the system (verify wheels are lifted for initial testing)
# - Connect phone/PC to Wi-Fi SSID: "Wheelchair_Health"
# - Open browser and navigate to: [http://192.168.4.1](http://192.168.4.1)
```

---

# 🎯 Applications

- **Elderly & Disability Mobility:** Independent indoor mobility with effortless multi-mode control for users with varying motor dexterity.
- **Hospitals & Rehabilitation Centers:** Patient transport combined with continuous bedside/chairside vital-sign visibility.
- **Home Healthcare & Caregiver Assistance:** Local Wi-Fi monitoring and remote caregiver maneuvering without internet dependency.
- **Embedded Systems & Telehealth Research:** Modular educational testbed for sensor fusion, safety-critical motor control, and offline IoT dashboards.

---

# 👨‍💻 Project Team & Developer

**Department of Electronics & Communication Engineering (Group G-12, 2026–27)**  
*Pranveer Singh Institute of Technology (PSIT), Kanpur — Affiliated to Dr. A.P.J. Abdul Kalam Technical University, Lucknow*

- **Saurabh Kumar** (`2301640310116`)
- **Milan Kushwaha** (`2301640310079`)
- **Nitin Agrahari** (`2301640310088`)
- **Ujjawal Gupta** (`2301640310137`)
- **Project Supervisor:** Ms. Sugandha Tejeswee *(Assistant Professor, Dept. of ECE)*

---

# ⭐ Support

If you found this project useful, please consider giving it a **⭐ Star** on GitHub!
