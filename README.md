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
---

### 🔹 Actuation Logic Block

      +------------------+
      | Gas Value Input  |
      +--------+---------+
               |
       gas > threshold ?
         /           \
       YES             NO
        |               |

