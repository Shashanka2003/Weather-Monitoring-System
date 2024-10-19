# Weather-Monitoring-System

## Overview

This project implements an IoT-based weather monitoring system using Arduino, ESP32, and various sensors to collect environmental data in real time. The system transmits data, including temperature, humidity, gas levels, and rainfall, to a Telegram bot for easy access and monitoring.

## Components Used

- **Microcontrollers**:
  - Arduino
  - ESP32
- **Sensors**:
  - DHT11 (Temperature and Humidity)
  - MQ-2 or MQ-135 (Gas Detection)
  - Rain Sensor
- **Other Components**:
  - Breadboard
  - Jumper Wires
  - Resistors
  - Power Supply


## Implementation Steps

### 1. Gather Components

- Ensure you have all the required components as listed above.

### 2. Circuit Design

- Connect the DHT11 sensor:
  - VCC to 3.3V on the ESP32
  - GND to GND
  - Data pin to a digital pin (e.g., D4)
- Connect the gas sensor (MQ-2/MQ-135):
  - VCC to 5V on the ESP32
  - GND to GND
  - Analog output to an analog pin (e.g., A0)
- Connect the rain sensor:
  - VCC to 5V on the ESP32
  - GND to GND
  - Digital output to a digital pin (e.g., D5)

### 3. Programming the ESP32

- **Arduino IDE Setup**:
  1. Install the latest version of Arduino IDE.
  2. Add the ESP32 board to the Arduino IDE through the Board Manager.

- **Install Required Libraries**:
  - Open the Library Manager and search for:
    - `DHT sensor library` by Adafruit
    - `UniversalTelegramBot` by Brian Lough

- **Write the Code**:
  
  Here’s a sample code structure:

  ```cpp
  #include <WiFi.h>
  #include <DHT.h>
  #include <UniversalTelegramBot.h>

  // Constants
  #define DHTPIN 4            // DHT11 data pin
  #define DHTTYPE DHT11       // DHT 11
  #define BOTtoken "your_bot_token"  // Replace with your Bot Token

  // Create DHT object
  DHT dht(DHTPIN, DHTTYPE);
  WiFiClient client;
  UniversalTelegramBot bot(BOTtoken, client);

  // Wi-Fi Credentials
  const char* ssid = "your_SSID";           // Replace with your SSID
  const char* password = "your_PASSWORD";    // Replace with your Wi-Fi password

  void setup() {
      Serial.begin(115200);
      dht.begin();
      WiFi.begin(ssid, password);
      
      // Wait for connection
      while (WiFi.status() != WL_CONNECTED) {
          delay(500);
          Serial.print(".");
      }
      Serial.println("Connected to Wi-Fi");
  }

  void loop() {
      // Reading temperature and humidity
      float h = dht.readHumidity();
      float t = dht.readTemperature();
      
      // Read gas and rain sensor values
      int gasValue = analogRead(A0);
      int rainValue = digitalRead(D5);

      // Send data to Telegram
      String message = "Temperature: " + String(t) + "°C\n";
      message += "Humidity: " + String(h) + "%\n";
      message += "Gas Level: " + String(gasValue) + "\n";
      message += "Rain Detected: " + String(rainValue == HIGH ? "No" : "Yes") + "\n";

      bot.sendMessage("your_chat_id", message, ""); // Replace with your chat ID
      
      delay(10000); // Send data every 10 seconds
  }
