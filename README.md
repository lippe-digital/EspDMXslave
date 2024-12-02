# EspDMXslave
# Bidirectional DMX Slave with ESP32-C3, SN75176 IC, and SK6812-W LED Strip

This project demonstrates how to build a **bidirectional DMX slave** using an **ESP32-C3**, an **SN75176 RS-485 transceiver**, and an **SK6812-W LED strip**. The system includes **WiFi connectivity**, **OTA (Over-the-Air) updates**, a **web interface for control**, and **MQTT** support. It allows you to control the LED strip and update device settings via a web interface without reflashing the firmware.

## Features:
- **Bidirectional DMX communication** (send and receive DMX data)
- **Web Interface** to control RGBW LEDs and modify settings (WiFi, MQTT, OTA)
- **WiFi connectivity** for remote control and integration with other devices
- **OTA (Over-the-Air) updates** for easy firmware upgrades
- **MQTT support** for remote control of the device

## Components:
- **ESP32-C3 Module** (with GPIO pins)
- **SN75176 RS-485 Transceiver IC**
- **SK6812-W LED Strip** (RGB + White)
- **Power Supply**:
  - **5V DC** for LED strip
  - **3.3V DC** for ESP32-C3 and SN75176
- **Step-up Converter** (if required, to step-up 3.3V to 5V for SN75176)
- **DMX Cable** (with XLR connectors)
- **Termination Resistor** (optional, for long cable runs)
- **Decoupling Capacitors** (0.1µF and 10µF)
- **Fuses/Circuit Breakers** for protection

## Setup:

### Step 1: Wiring the Circuit

1. **Power the LED Strip**:
   - Use a **5V power supply** to power the **SK6812-W LED strip**.
   - Connect the **GND** from the power supply to the common ground.

2. **Power the ESP32-C3 and SN75176**:
   - Power the **ESP32-C3** using a **3.3V power supply** (e.g., **Mean Well HDR-15-3.3**).
   - If the **SN75176** needs **5V**, use a **step-up converter**.

3. **Connect the SN75176 RS-485 Transceiver**:
   - **RO** (Pin 2) → GPIO pin on ESP32-C3 for **receiving DMX** data.
   - **RE** (Pin 3) → GPIO pin on ESP32-C3 (controls **receive** mode).
   - **DE** (Pin 4) → GPIO pin on ESP32-C3 (controls **transmit** mode).
   - **DI** (Pin 1) → GPIO pin on ESP32-C3 for **transmitting DMX** data.
   - **A (Pin 6)** → **DMX+**.
   - **B (Pin 7)** → **DMX-**.

4. **Connect the ESP32-C3 to the LED Strip**:
   - **GPIO 5** → **Data In** of the **SK6812-W** LED strip.
   - Connect the **GND** from both **ESP32-C3** and **LED power supply** to the common ground.

5. **Optional: Termination Resistor**:
   - For long **DMX cable runs** (over 50 meters), place a **120-ohm termination resistor** at the end of the DMX chain.

### Step 2: Firmware Setup

1. **Install the Arduino IDE**:
   - Download and install the **Arduino IDE**.
   - Add the ESP32 board via the **Boards Manager**.

2. **Install Required Libraries**:
   - Install these libraries in the Arduino IDE:
     - **ESP-DMX** (for DMX communication)
     - **FastLED** (for controlling the LED strip)
     - **WiFi** (for WiFi connectivity)
     - **ArduinoOTA** (for OTA updates)
     - **WebServer** (for the web interface)
     - **PubSubClient** (for MQTT communication)

3. **Upload the Code**:
   - Use the provided Arduino code to control the DMX communication, LED strip, and web interface.
   - Customize the **WiFi credentials**, **MQTT server**, and **MQTT credentials** in the code.

```cpp
// Example of the code configuration for WiFi and MQTT setup
const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
String mqtt_server = "your_MQTT_broker";
String mqtt_user = "your_MQTT_user";
String mqtt_password = "your_MQTT_password";
```

4. **Upload the code** to the ESP32-C3 via USB and test it using the web interface.

### Step 3: Web Interface Access and Control

1. **Connect to WiFi**:
   - After powering up the device, it will connect to the WiFi network using the provided credentials.
   - The device's IP address will be shown in the serial monitor.

2. **Access the Web Interface**:
   - Open a web browser and navigate to the device's IP address.
   - You will see a page where you can:
     - Control the **RGBW values** of the LED strip.
     - Toggle **WiFi**, **OTA updates**, and **MQTT**.
     - Update **MQTT server** details.

3. **Update the LED Strip**:
   - Use the sliders to adjust the **Red**, **Green**, **Blue**, and **White** channels of the LED strip.
   - Press "Update LEDs" to apply the changes.

4. **Change MQTT Settings**:
   - Modify the MQTT credentials (server, username, password) directly via the web interface.
   - The changes are stored and persist across reboots.

### Step 4: OTA Updates

- **OTA Updates** allow for firmware upgrades over-the-air, meaning no physical access to the device is needed. Enable OTA through the web interface, and when new firmware is uploaded via the Arduino IDE, it will automatically update the device.

---

## Code Explanation:

- The provided code uses **ESP-DMX** for DMX communication and **FastLED** to control the SK6812-W LEDs.
- The **WebServer** library hosts a simple webpage to control LEDs and update settings.
- **PubSubClient** integrates **MQTT**, allowing for remote control of the device.
- **ArduinoOTA** enables OTA updates.

---

## License:
This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for more details.

---

## Acknowledgements:
- **ESP32-C3** and the **ESP32** platform.
- **FastLED** for easy LED control.
- **ESP-DMX** for handling DMX communication.
- **PubSubClient** for MQTT functionality.

---

## Support:
If you have any issues or questions, feel free to open an **Issue** on this repository, and I'll be happy to help!

---

This README gives a detailed overview of how to set up and use the project. It also includes installation, configuration, and usage instructions for controlling the DMX slave and LED strip with WiFi, OTA, and MQTT capabilities.
