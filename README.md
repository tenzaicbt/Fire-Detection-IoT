# Fire & Smoke Detection IoT System

## 🚀 Project Overview
The **Fire & Smoke Detection IoT System** is an advanced, real time monitoring solution that uses IoT technology to detect fire and smoke hazards. It integrates **sensor networks, Blynk cloud monitoring, and ESP8266 communication** to ensure early detection and rapid response.

## 🔥 Features
- **Real-time Fire & Smoke Detection** using MQ2 & Flame Sensors
- **Temperature & Humidity Monitoring** via DHT11 Sensor
- **Visual Alerts** on 16x2 LCD Display
- **Buzzer & LED Alarm System** for immediate response
- **Blynk App Integration** for remote notifications
- **Wi-Fi Connectivity** using ESP8266

## 🛠️ Tech Stack
- **Microcontroller:** ESP8266 (NodeMCU)
- **Sensors:** MQ2 Smoke Sensor, Flame Sensor, DHT11
- **Actuators:** Buzzer, LED, 16x2 LCD Display
- **Software:** Arduino IDE, Blynk App, C++
- **Cloud Services:** Blynk IoT Platform

## 📁 Folder Structure
```bash
📦 Fire-Smoke-Detection-IoT
├── 📂 hardware               # Circuit diagrams & wiring guides
├── 📂 firmware               # Arduino sketches for ESP8266
├── 📂 cloud                  # Blynk dashboard setup & API integration
├── 📂 frontend               # Mobile app interface (Blynk setup)
├── 📜 README.md              # Project documentation
```

## 🚀 Getting Started
### 1️⃣ Prerequisites
Ensure you have the following installed:
- Arduino IDE
- ESP8266 Board Package
- Blynk Library
- DHT, LiquidCrystal_I2C, and MQ2 Libraries

### 2️⃣ Installation
```bash
git clone https://github.com/your-username/fire-smoke-detection-iot.git
cd fire-smoke-detection-iot
```

### 3️⃣ Circuit Connections
| Component | ESP8266 Pin |
|-----------|------------|
| MQ2 Sensor | A0 |
| Flame Sensor | D5 |
| DHT11 | D4 |
| Buzzer | D6 |
| LED | D7 |
| LCD Display | I2C Pins (SDA, SCL) |

### 4️⃣ Uploading the Code
1. **Connect ESP8266 to your PC**
2. **Open Arduino IDE**
3. **Load the firmware from `/firmware` folder**
4. **Select Board:** `NodeMCU 1.0 (ESP-12E Module)`
5. **Upload the code**

### 5️⃣ Configuring Blynk App
1. **Create a new Blynk project**
2. **Select ESP8266 as the device**
3. **Add Widgets:**
   - **Gauge:** Temperature & Humidity
   - **LED Indicator:** Fire & Smoke Detection
   - **Notifications:** Send alerts
4. **Copy the Blynk Auth Token and paste it in the firmware**

## 📊 Alerts & Notifications
- **Buzzer & LED Indicators** for on-device alerts
- **LCD Display** to show sensor readings
- **Blynk Mobile App** notifications

## 🤝 Contributing
We welcome contributions! Feel free to fork the repo and submit pull requests.

## 📜 License
This project is licensed under the MIT License.

## 📧 Contact
For inquiries or collaborations, reach out to **yohankoshala.info@gmail.com**.

