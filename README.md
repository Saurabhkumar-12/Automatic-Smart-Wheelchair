# ♿ Automatic Smart Wheelchair

The **Automatic Smart Wheelchair** is an IoT-enabled assistive mobility system designed to enhance the independence, safety, and quality of life of elderly individuals, patients, and people with physical disabilities. The wheelchair combines embedded systems, health monitoring, obstacle detection, and intelligent control mechanisms to provide a safer and more convenient mobility experience.

The system is powered by **ESP32 microcontrollers** and integrates multiple sensors, including **IR sensors** for obstacle detection and the **MAX30100 sensor** for real-time health monitoring. Users can control the wheelchair using either a **joystick** or a **Wi-Fi-based mobile application**, making it suitable for both manual and remote operation.

---

# ✨ Features

- 🦽 Dual Control System (Joystick + Wi-Fi Mobile App)
- 🚧 Real-Time Obstacle Detection using IR Sensors
- ❤️ Heart Rate & SpO₂ Monitoring (MAX30100)
- 🌡️ Temperature & Humidity Monitoring
- 💡 Automatic Ambient Light Detection
- 🔔 Buzzer & LED Safety Alerts
- 📱 ESP32-Based Wireless Control
- ⚡ Low Power Embedded System
- 🛡️ Safe Navigation with Automatic Motor Stop
- 🔋 Battery Powered Mobility Solution

---

# 🏗️ System Architecture

```text
                    ┌─────────────────────────┐
                    │      Mobile App         │
                    │    (Wi-Fi Control)      │
                    └───────────┬─────────────┘
                                │
                           Wi-Fi Communication
                                │
                                ▼
                 ┌─────────────────────────────┐
                 │      ESP32 Controller       │
                 └───────────┬─────────────────┘
                             │
      ┌──────────────┬────────┼──────────┬───────────────┐
      │              │        │          │               │
      ▼              ▼        ▼          ▼               ▼
  IR Sensors    MAX30100    DHT Sensor   LDR        Joystick
Obstacle Detect  Health     Temp/Humidity Light      Manual Input
      │              │          │          │               │
      └──────────────┴──────────┴──────────┴───────────────┘
                             │
                             ▼
                    Motor Driver (L298N)
                             │
                             ▼
                      DC Geared Motors
                             │
                             ▼
                  Smart Wheelchair Movement
```

---

# 🔄 System Workflow

```text
Power ON
    │
    ▼
ESP32 Initialization
    │
    ▼
Read Sensors
    │
    ├── IR Sensors
    ├── MAX30100
    ├── DHT Sensor
    ├── LDR
    └── Joystick / Mobile App
    │
    ▼
Process Sensor Data
    │
    ▼
Obstacle Detected?
    │
 ┌──Yes───────────────┐
 │                    │
 ▼                    ▼
Stop Motors      Continue Movement
 │                    │
 ▼                    ▼
Safety Alert     Navigate Normally
      │
      ▼
Display Health Data
```

---

# 🧩 Hardware Components

| Component | Purpose |
|-----------|----------|
| ESP32 | Main Microcontroller |
| IR Sensors | Obstacle Detection |
| MAX30100 | Heart Rate & SpO₂ Monitoring |
| DHT11/DHT22 | Temperature & Humidity |
| LDR | Ambient Light Detection |
| L298N Motor Driver | Motor Control |
| DC Geared Motors | Wheel Movement |
| Joystick Module | Manual Navigation |
| Buzzer | Audio Warning |
| LEDs | Visual Status Indicators |
| Rechargeable Battery | Power Supply |

---

# 🛠️ Technology Stack

## Embedded Systems

- ESP32
- Arduino IDE
- Embedded C/C++

## Sensors

- IR Sensor
- MAX30100
- DHT11 / DHT22
- LDR

## Communication

- Wi-Fi
- Mobile App Control

## Hardware

- L298N Motor Driver
- DC Geared Motors
- Joystick
- Battery Pack

---

# 📂 Project Structure

```text
Automatic-Smart-Wheelchair
│
├── Arduino_Code/
├── Circuit_Diagram/
├── Hardware/
├── Mobile_App/
├── Documentation/
├── Images/
├── Components_List/
├── README.md
└── LICENSE
```

---

# 🔐 Safety Features

- Automatic Obstacle Detection
- Instant Motor Shutdown
- Health Monitoring
- Emergency Alert System
- Stable Motor Control
- Low Power Consumption
- Real-Time Sensor Monitoring

---

# 📡 Functional Modules

```text
User Input
     │
     ▼
Joystick / Mobile App
     │
     ▼
ESP32 Controller
     │
     ├───────────────┐
     │               │
     ▼               ▼
Sensor Data      Motor Control
     │               │
     └──────┬────────┘
            ▼
Wheelchair Navigation
```

---

# 🚀 Future Enhancements

- GPS Navigation
- Voice Command Control
- AI-Based Path Planning
- Face Recognition
- Emergency SMS Notification
- Fall Detection
- Cloud-Based Health Monitoring
- Autonomous Navigation
- Camera Integration
- Mobile Application Dashboard

---

# 📸 Project Images

Include images of:

- Prototype
- Circuit Diagram
- ESP32 Wiring
- Sensor Connections
- Final Hardware Model
- Mobile App Interface

---

# ⚙️ Getting Started

```bash
# Clone Repository
git clone <repository-url>

# Open Arduino IDE
# Install ESP32 Board Package

# Install Required Libraries
MAX30100
DHT Sensor
WiFi
ESP32

# Upload Code to ESP32
```

---

# 🎯 Applications

- Elderly Care
- Hospitals
- Rehabilitation Centers
- Home Healthcare
- Disability Assistance
- Smart Healthcare Systems

---

# 👨‍💻 Developer

## Saurabh Kumar

**Embedded Systems Developer | IoT Enthusiast | Full Stack Developer | UI/UX Designer**

### Skills

- Embedded Systems
- ESP32
- Arduino
- IoT
- C/C++
- Electronics
- Sensor Integration
- React.js
- Node.js
- MongoDB
- UI/UX Design

---

# ⭐ Support

If you found this project useful, please consider giving it a **⭐ Star** on GitHub.

---

