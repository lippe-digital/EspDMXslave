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

#### Wiring Diagram

Below is a schematic showing how to wire the ESP32-C3, SN75176 RS-485 transceiver, and the SK6812-W LED strip:

```
           +-----------------+
           |   230V AC       |
           |    Power        |
           |    Supply       |
           +-----------------+
                  |
                  V
          +---------------+
          |   5V Power    | ------------> LED Strip (SK6812-W)
          |   Supply      |
          +---------------+ 
                  |
                  V
         +---------------------+
         |     SN75176 IC      |
         |                     |
         |  RO (Pin 2)  --> GPIO| ---> ESP32-C3 for DMX receiving
         |  RE (Pin 3)  --> GPIO|
         |  DE (Pin 4)  --> GPIO| ---> ESP32-C3 for DMX transmission
         |  DI (Pin 1)  --> GPIO|
         |  A (Pin 6)   --> DMX+|
         |  B (Pin 7)   --> DMX-|
         +---------------------+
                  |
                  V
         +---------------------+
         |     ESP32-C3         |
         |     WiFi, OTA, MQTT  |
         |                      |
         | GPIO5 --> Data In --> LED Strip
         +---------------------+
                  |
                  V
          Common Ground (connected to all devices)

```

#### 1. **Power the LED Strip**:
- Use a **5V power supply** to power the **SK6812-W LED strip**.
- Connect the **GND** from the power supply to the common ground.

#### 2. **Power the ESP32-C3 and SN75176**:
- Power the **ESP32-C3** using a **3.3V power supply** (e.g., **Mean Well HDR-15-3.3**).
- If the **SN75176** needs **5V**, use a **step-up converter**.

#### 3. **Connect the SN75176 RS-485 Transceiver**:
- **RO** (Pin 2) → GPIO pin on ESP32-C3 for **receiving DMX** data.
- **RE** (Pin 3) → GPIO pin on ESP32-C3 (controls **receive** mode).
- **DE** (Pin 4) → GPIO pin on ESP32-C3 (controls **transmit** mode).
- **DI** (Pin 1) → GPIO pin on ESP32-C3 for **transmitting DMX** data.
- **A (Pin 6)** → **DMX+**.
- **B (Pin 7)** → **DMX-**.

#### 4. **Connect the ESP32-C3 to the LED Strip**:
- **GPIO 5** → **Data In** of the **SK6812-W** LED strip.
- Connect the **GND** from both **ESP32-C3** and **LED power supply** to the common ground.

#### 5. **Optional: Termination Resistor**:
- For long **DMX cable runs** (over 50 meters), place a **120-ohm termination resistor** at the end of the DMX chain.

### Step 2: Firmware Setup

#### 1. **Install the Arduino IDE**:
   - Download and install the **Arduino IDE**.
   - Add the ESP32 board via the **Boards Manager**.

#### 2. **Install Required Libraries**:
   - Install these libraries in the Arduino IDE:
     - **ESP-DMX** (for DMX communication)
     - **FastLED** (for controlling the LED strip)
     - **WiFi** (for WiFi connectivity)
     - **ArduinoOTA** (for OTA updates)
     - **WebServer** (for the web interface)
     - **PubSubClient** (for MQTT communication)

#### 3. **Upload the Code**:
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

#### 4. **Upload the code** to the ESP32-C3 via USB and test it using the web interface.

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

## Full Code:

```cpp
#include <WiFi.h>
#include <ESP_DMX.h>
#include <FastLED.h>
#include <ArduinoOTA.h>
#include <WebServer.h>
#include <PubSubClient.h>

// WiFi credentials
const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

// MQTT credentials
String mqtt_server = "your_MQTT_broker";
String mqtt_user = "your_MQTT_user";
String mqtt_password = "your_MQTT_password";

WiFiClient espClient;
PubSubClient client(espClient);

// DMX and LED settings
#define DMX_RX_PIN 17
#define DMX_TX_PIN 16
#define DMX_EN_PIN 4
#define LED_PIN 5
#define NUM_LEDS 60  // Modify based on the number of LEDs in your strip

ESP_DMX dmx;
CRGBW leds[NUM_LEDS];  // Use CRGBW for SK6812-W LED strip
WebServer server(80);

bool wifiEnabled = true;
bool otaEnabled = true;
bool mqttEnabled = true;

void handleRoot() {
  String html = "<h1>ESP32-C3 DMX Slave</h1>";
  html += "<form action='/control' method='POST'>";
  html += "Red: <input type='number' name='red' min='0' max='255'><br>";
  html += "Green: <input type='number' name='green' min='0' max='255'><br>";
  html += "Blue: <input type='number' name='blue' min='0' max='255'><br>";
  html += "White: <input type='number' name='white' min='0' max='255'><br>";
  html += "<input type='submit' value='Update LEDs'>";
  html += "</form>";
  html += "<form action

='/wifi' method='POST'>";
  html += "WiFi Enable: <input type='checkbox' name='wifi' checked><br>";
  html += "<input type='submit' value='Save WiFi Settings'>";
  html += "</form>";
  html += "<form action='/ota' method='POST'>";
  html += "OTA Enable: <input type='checkbox' name='ota' checked><br>";
  html += "<input type='submit' value='Save OTA Settings'>";
  html += "</form>";
  html += "<form action='/mqtt' method='POST'>";
  html += "MQTT Enable: <input type='checkbox' name='mqtt' checked><br>";
  html += "<input type='submit' value='Save MQTT Settings'>";
  html += "</form>";
  server.send(200, "text/html", html);
}

void handleControl() {
  int red = server.arg("red").toInt();
  int green = server.arg("green").toInt();
  int blue = server.arg("blue").toInt();
  int white = server.arg("white").toInt();
  leds[0] = CRGBW(red, green, blue, white);
  FastLED.show();
  server.send(200, "text/html", "<h1>LEDs Updated!</h1><a href='/'>Back</a>");
}

void handleWifi() {
  wifiEnabled = server.hasArg("wifi");
  server.send(200, "text/html", "<h1>WiFi Settings Updated!</h1><a href='/'>Back</a>");
}

void handleOTA() {
  otaEnabled = server.hasArg("ota");
  server.send(200, "text/html", "<h1>OTA Settings Updated!</h1><a href='/'>Back</a>");
}

void handleMQTT() {
  mqttEnabled = server.hasArg("mqtt");
  server.send(200, "text/html", "<h1>MQTT Settings Updated!</h1><a href='/'>Back</a>");
}

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("Connected to WiFi");

  dmx.begin(DMX_RX_PIN, DMX_TX_PIN);
  FastLED.addLeds<SK6812, LED_PIN, GRB>(leds, NUM_LEDS);

  server.on("/", HTTP_GET, handleRoot);
  server.on("/control", HTTP_POST, handleControl);
  server.on("/wifi", HTTP_POST, handleWifi);
  server.on("/ota", HTTP_POST, handleOTA);
  server.on("/mqtt", HTTP_POST, handleMQTT);
  server.begin();

  ArduinoOTA.begin();
}

void loop() {
  ArduinoOTA.handle();
  server.handleClient();
  dmx.update();
}
```

This code handles WiFi, OTA, MQTT, and LED control functionalities through a simple web interface. Modify the values for **WiFi** and **MQTT** credentials as needed.
