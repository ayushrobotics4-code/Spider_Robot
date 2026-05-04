# 🕷️ Spider-Bot Tactical HUD (Spider-OS v6.0)

[![Hardware](https://img.shields.io/badge/Hardware-ESP32-E7352C?style=for-the-badge&logo=espressif)](https://www.espressif.com/)
[![Framework](https://img.shields.io/badge/Framework-Arduino_IDE-00979D?style=for-the-badge&logo=arduino)](https://www.arduino.cc/)
[![Interface](https://img.shields.io/badge/Interface-Asynchronous_Web_Server-blue?style=for-the-badge)](https://github.com/me-no-dev/ESPAsyncWebServer)

An advanced hexapod/quadruped spider robot controller featuring a custom-built, mobile-responsive **Tactical HUD**. This system integrates real-time obstacle avoidance using Ultrasonic sensors and a multi-servo gait engine powered by the **PCA9685** driver.

## 🧠 System Features

*   **Spider-OS v6.0 HUD:** A custom HTML/CSS interface with a Spider-Man aesthetic, featuring dual-eye overlays and glowing UI elements.
*   **Dual Control Modes:** Switchable interface between a **virtual joystick** and a **tactical D-Pad**.
*   **Autonomous Obstacle Avoidance:** Real-time distance monitoring via `HC-SR04`. If an obstacle is detected within 20cm, the robot automatically halts and executes a retreat maneuver.
*   **Asynchronous Communication:** Uses `ESPAsyncWebServer` for zero-latency control—the robot reacts instantly without blocking the main loop.
*   **Tactical Sequences:** Includes a "Final Attack" stance and automated movement patterns.

---

## 🛠️ Hardware Requirements

| Component | Description |
| :--- | :--- |
| **Microcontroller** | ESP32 (38-pin or 30-pin) |
| **Servo Driver** | Adafruit PCA9685 (16-Channel PWM) |
| **Servos** | 12x SG90 or MG90S (3 servos per leg) |
| **Distance Sensor** | HC-SR04 Ultrasonic Sensor |
| **Power Supply** | 5V 4A+ (External power for servos is critical) |

---

## 🔌 Pin Mapping

### Ultrasonic Sensor (HC-SR04)
- **TRIG:** GPIO 5
- **ECHO:** GPIO 18

### PCA9685 I2C Connection
- **SDA:** GPIO 21 (Default ESP32)
- **SCL:** GPIO 22 (Default ESP32)

---

## 🚀 Getting Started

### 1. Library Installation
Ensure you have the following libraries installed in your Arduino IDE:
*   `Wire.h` (Built-in)
*   `Adafruit_PWMServoDriver`
*   `WiFi.h` (Built-in)
*   `ESPAsyncWebServer`
*   `AsyncTCP`

### 2. Network Connection
1.  Flash the code to your ESP32.
2.  Open your smartphone's WiFi settings and connect to:
    *   **SSID:** `Spider Robot controller`
3.  Open your browser and navigate to `192.168.4.1`.

---

## 🕹️ HUD Controls

*   **Joystick/Buttons:** Toggle between fluid movement and precise stepping.
*   **Attack Mode:** Triggers a visual HUD "eye glow" and prepares the `attackStance()`.
*   **Sticky Command:** Holding a direction for more than 1 second engages "Auto-Drive" (Sticky Overlay).
*   **Action Tray:** Quick-access buttons for Wave, Dance, Patrol, and Fitness (Pushups).

---

## 📌 Project Vision
> "Building intelligent machines that operate independently, make decisions locally, and scale into real-world deployment."

