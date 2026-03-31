# IoT Fundamentals with ESP32 and MQTT

This project follows the IoT Fundamentals workflow: ESP32 reads sensor data, publishes with MQTT, and a backend can subscribe for processing or visualization.

## Overview

In this implementation:

- ESP32 reads distance data from an HC-SR04 sensor
- ESP32 publishes measurements to an MQTT broker
- ESP32 listens for threshold updates on a config topic
- Onboard LED turns on when distance is below threshold

## Components of the System

1. Device layer: ESP32 plus ultrasonic sensor
2. Communication layer: MQTT publish and subscribe
3. Broker layer: Mosquitto or any MQTT broker
4. Backend layer: optional Python subscriber/dashboard

## Hardware Required

- ESP32 development board
- HC-SR04 ultrasonic sensor
- Breadboard (optional)
- Jumper wires
- Wi-Fi network and a machine running an MQTT broker

## Wiring

| Sensor/Signal | ESP32 Pin |
| --- | --- |
| Trigger | GPIO 19 |
| Echo | GPIO 18 |
| LED | GPIO 2 |
| VCC | 3.3V |
| GND | GND |

## Software Setup

### 1. PlatformIO

Install VS Code and PlatformIO extension:

https://platformio.org/install/ide?install=vscode

### 2. Project Configuration

Current configuration in platformio.ini:

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
lib_deps = 
    martinsos/HCSR04@^2.0.0
    knolleary/PubSubClient@^2.8
```

### 3. MQTT Broker

On Linux, example Mosquitto setup:

```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl start mosquitto
sudo systemctl enable mosquitto
```

## MQTT Topics Used

- Publish distance: sensors/esp32_Owen/distance
- Subscribe threshold config: config/esp32_Owen/threshold

## Build and Run

```bash
pio run
pio run --target upload
pio device monitor --baud 115200
```

## Runtime Flow

1. ESP32 connects to Wi-Fi.
2. ESP32 connects to MQTT broker on port 1883.
3. Distance is measured every second.
4. Distance is published to sensors/esp32_Owen/distance.
5. Threshold updates are received from config/esp32_Owen/threshold.
6. LED behavior:
   - ON when distance < threshold
   - OFF otherwise

## Configuration Values to Update

Edit in src/main.cpp before deployment:

- ssid
- password
- mqtt_server
- device_id
- threshold

## Quick MQTT Test

Publish a new threshold value from any MQTT client:

- Topic: config/esp32_Owen/threshold
- Payload: 15

## Troubleshooting

- Verify broker IP and port are correct.
- Ensure ESP32 and broker are on reachable networks.
- Confirm firewall rules allow MQTT traffic.
- Check Serial Monitor at 115200 for connection logs.

## Real-World Extensions

- Add TLS and broker authentication
- Add cloud broker integration (AWS IoT or Azure IoT)
- Send data to database and dashboard (for example Grafana)
- Move credentials into a local config file excluded from git
