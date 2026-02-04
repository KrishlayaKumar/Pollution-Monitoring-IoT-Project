# Pollution Monitoring IoT Project

## Project Overview
The **Pollution Monitoring IoT Project** is a real-time air quality monitoring system built using ESP32 and Raspberry Pi. Environmental data is collected using sensors, transmitted wirelessly, stored in a database, analyzed using ThingSpeak and MATLAB, and based on pollution thresholds, an alert is triggered using LED actuation.

This project demonstrates an **end-to-end IoT pipeline** including sensing, communication, storage, cloud analytics, decision-making, and actuation.

---

## System Workflow
Sensor → ESP32 → Raspberry Pi → Database → ThingSpeak → MATLAB Analysis → Raspberry Pi → ESP32 → Actuation (LED)


---

## Block Diagrams

### Overall System Architecture
                                                                        +-------------------+
                                                                          | Gas / Temp / |
                                                                          | Humidity Sensor |
                                                                        +---------+---------+
                                                                                  |
                                                                                  v
                                                                        +-------------------+
                                                                             | ESP32 |
                                                                          | Sensor Reading |
                                                                         | BLE Transmission |
                                                                        +---------+---------+
                                                                                  |
                                                                            (Bluetooth / BLE)
                                                                                  |
                                                                                  v
                                                                         +-------------------+
                                                                            | Raspberry Pi |
                                                                          | Data Processing |
                                                                          | Threshold Check |
                                                                         +---------+---------+
                                                                                   |
                                                                                   v
                                                                         +-------------------+
                                                                             | InfluxDB |
                                                                           | Time-Series DB |
                                                                         +---------+---------+
                                                                                  |
                                                                                  v
                                                                         +-------------------+
                                                                            | ThingSpeak |
                                                                           | Visualization |
                                                                         +---------+---------+
                                                                                   |
                                                                                   v
                                                                         +-------------------+
                                                                           | MATLAB Analysis |
                                                                           | Threshold Logic |
                                                                         +---------+---------+
                                                                                   |
                                                                                   v
                                                                         +-------------------+
                                                                            | Raspberry Pi |
                                                                           | Control Command |
                                                                         +---------+---------+
                                                                                   |
                                                                                   v
                                                                         +-------------------+
                                                                              | ESP32 |
                                                                          | LED Actuation |
                                                                         +-------------------+
                                        

### 🔹 Actuation Logic Block

                                                                         +------------------+
                                                                          | Gas Value Input  |
                                                                         +--------+---------+
                                                                                  |
                                                                          gas > threshold ?
                                                                            /           \
                                                                         YES             NO
                                                                          |               |
                                                                 +----------------+ +----------------+
                                                                  | RED LED ON |      | RED LED OFF |
                                                                 +----------------+ +----------------+

---

## Working Explanation

1. Sensors measure **gas concentration, temperature, and humidity**
2. ESP32 reads sensor data and sends it via **Bluetooth / BLE**
3. Raspberry Pi receives and stores data in **InfluxDB**
4. Data is pushed to **ThingSpeak** for visualization
5. **MATLAB** performs analysis and threshold evaluation
6. Control signal is sent back to ESP32
7. **Red LED turns ON** if pollution exceeds threshold

---

## Hardware Components
- ESP32
- Raspberry Pi 4
- MQ Gas Sensor
- DHT11 Temperature & Humidity Sensor
- Red LED
- Resistors
- Jumper wires

---

## Software & Technologies
- Embedded C (Arduino IDE)
- Python (Raspberry Pi)
- Bluetooth / BLE
- InfluxDB
- ThingSpeak
- MATLAB
- Raspberry Pi OS (Linux)

---

## ESP32 Code (Sensor + BLE)

```cpp
#include "BluetoothSerial.h"
#include <DHT.h>

BluetoothSerial SerialBT;

#define DHTPIN 4
#define DHTTYPE DHT11
#define MQ_PIN 34
#define LED_PIN 2

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32-BT-Server");
  
  dht.begin();
  pinMode(MQ_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);

  Serial.println("ESP32 Bluetooth Started");
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  int gas = analogRead(MQ_PIN);

  char data[64];
  snprintf(data, sizeof(data), "%f,%f,%d", temp, hum, gas);
  SerialBT.println(data);

  // Actuation
  if (SerialBT.available()) {
    char cmd = SerialBT.read();
    if (cmd == '1') digitalWrite(LED_PIN, HIGH);
    else digitalWrite(LED_PIN, LOW);
  }

  delay(2000);
}
```
## Raspberry Pi Code (BLE + Database)
```python
import bluetooth
import time
from influxdb import InfluxDBClient

INFLUX_DB = "pollution_db"
MEASUREMENT = "sensor_data"

client = InfluxDBClient(host="localhost", port=8086, database=INFLUX_DB)

sock = bluetooth.BluetoothSocket(bluetooth.RFCOMM)
sock.connect(("ESP32_BT_ADDRESS", 1))

THRESHOLD = 400

while True:
    data = sock.recv(1024).decode().strip()
    temp, hum, gas = map(float, data.split(","))

    json_body = [{
        "measurement": MEASUREMENT,
        "fields": {
            "temperature": temp,
            "humidity": hum,
            "gas": gas
        }
    }]
    client.write_points(json_body)

    if gas > THRESHOLD:
        sock.send("1")  # LED ON
    else:
        sock.send("0")  # LED OFF

    time.sleep(2)
```
## Threshold Logic

IF gas_value > threshold → Turn ON Red LED
ELSE
→ Turn OFF Red LED

## Key Features

- Real-time pollution monitoring
- Wireless data transmission
- Cloud-based visualization
- MATLAB analytics
- Automatic alert system
- End-to-end IoT pipeline
  
## Future Enhancements

- Mobile alerts
- Multi-level pollution indication
- ML-based pollution prediction
- Weather data integration
- Cloud deployment (AWS / Azure)
