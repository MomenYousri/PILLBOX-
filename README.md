# PILLBOX-
# 💊 SHEFAA Pillbox System 

![IoT](https://img.shields.io/badge/IoT-System-blue)
![ESP32](https://img.shields.io/badge/Microcontroller-ESP32-red)
![Flutter](https://img.shields.io/badge/App-Flutter-02569B)
![Firebase](https://img.shields.io/badge/Cloud-Firebase-FFCA28)

## 📖 Overview
The **SHEFAA Pillbox** system is a personal electronic nurse powered by the Internet of Things (IoT). It features a smart medication organizer divided into 6 compartments, seamlessly connected via the cloud (Firebase) to a mobile application (Flutter). 

**Goal:** To organize complex medical schedules and alert the patient at the exact dose time using visual effects (OLED screen and illuminated LEDs) and audio alerts (Buzzer), while providing caregivers or doctors with the ability to track the patient's medication adherence in real-time.

---

## 🛑 Problem Statement & 💡 Solutions

This smart pillbox was designed to solve four major problems in the healthcare sector:

### 1. Medication Non-Adherence & Missed Doses
* **The Problem:** Elderly individuals, Alzheimer's patients, and those with chronic diseases often forget their medication schedules, leading to severe health complications.
* **The SHEFAA Solution:** A **"dual" alarm system**. An audio alarm (Buzzer) alerts the patient remotely, and a visual alarm (Red LED) illuminates exactly in front of the specific compartment, ensuring the patient takes the right pill.

### 2. Double Dosing (Overdose Risk)
* **The Problem:** Patients may take their medication and later forget, leading them to accidentally take a second dose.
* **The SHEFAA Solution:** The moment the lid is opened, a magnetic sensor detects the action. The ESP32 instantly turns off the Red LED and turns on the Green LED, serving as a reassuring message: *"You have already taken this dose, do not worry."*

### 3. Caregiver Anxiety & Lack of Remote Monitoring
* **The Problem:** Caregivers away from home constantly worry if their elderly parent took their medication on time.
* **The SHEFAA Solution:** Wi-Fi connectivity ensures that when the box is opened, the caregiver receives a **"Taken"** notification via the app. If the dose time passes and the box remains closed, a **"Missed"** alert is triggered.

### 4. Difficulty in Programming Traditional Boxes
* **The Problem:** Older electronic pillboxes rely on tiny buttons and complex screens, making them difficult for the elderly to configure.
* **The SHEFAA Solution:** Zero physical interaction is required to program the box. All scheduling is done effortlessly through the SHEFAA mobile app, and the box automatically syncs data from the cloud.

---

## 🛠️ Hardware Components

### The Brain
* **ESP32 Board:** Chosen for its built-in Wi-Fi capabilities for cloud connectivity.

### Power System
* **18650 Lithium-ion Battery** (3.7V)
* **TP4056 Charging Module** (with Type-C port)
* **MT3608 Boost Converter**
* **Mechanical ON/OFF Switch**

### User Interface (UI) & Indicators
* **LEDs:** 12 total (6 Red + 6 Green). Each compartment has a Red LED (time for dose) and a Green LED (dose taken).
* **OLED Screen (128x64):** Displays time and upcoming dose details.
* **Buzzer:** For audio alerts.

### Safety Sensor & Enclosure
* **Magnetic Reed Sensor:** Placed on the lid to detect when the box is opened.
* **3D Printed Enclosure:** Custom-designed with 6 compartments, a bottom base for electronics, and a transparent acrylic lid.

---

## ⚡ Power Flow & System Logic

### Power Matrix
1. **Type-C** provides 5V to the TP4056 charging module.
2. The **18650 Battery** connects to the `B+` and `B-` terminals on the TP4056.
3. Power exits from the `OUT+` of the TP4056, passes through the **ON/OFF Switch**, and routes to the `IN+` of the **MT3608 boost converter**. (Negative `OUT-` connects to `IN-`).
4. The MT3608 potentiometer is adjusted to output exactly **5V**.
5. This 5V output connects to the `VIN` (or 5V) and `GND` pins on the **ESP32**.

### System Logic
1. The ESP32 fetches the schedule from Firebase (e.g., 2:00 PM).
2. At exactly 2:00 PM: 
   * Sends `HIGH` to the respective Red LED.
   * Sends pulsed signal to the Buzzer.
   * Continuously monitors the Reed Sensor.
3. Once the lid opens (Sensor reads `HIGH`):
   * Stops Buzzer (`LOW`).
   * Turns off Red LED (`LOW`).
   * Turns on Green LED (`HIGH`) to confirm the dose.
   * Updates Firebase status to trigger the mobile app notification.

---

## 🔌 Detailed Wiring Maps

### 1. 12 LEDs Wiring
> **Note:** Long leg (Anode/+) connects to a 220-ohm resistor then to the ESP32 pin. Short leg (Cathode/-) connects to common `GND`.

| Compartment | Red LED 🔴 | Green LED 🟢 |
| :---: | :---: | :---: |
| **Box 1** | Pin D13 | Pin D14 |
| **Box 2** | Pin D25 | Pin D26 |
| **Box 3** | Pin D27 | Pin D32 |
| **Box 4** | Pin D33 | Pin D4 |
| **Box 5** | Pin D16 | Pin D17 |
| **Box 6** | Pin D18 | Pin D19 |

### 2. Peripheral Wiring

#### OLED Screen (I2C Protocol)
| OLED Pin | ESP32 Pin | Description |
| :--- | :--- | :--- |
| **VCC** | 3.3V | Power supply |
| **GND** | GND | Common Ground |
| **SDA** | Pin D21 | Data Line |
| **SCL** | Pin D22 | Clock Line |

#### Buzzer
| Buzzer Terminal | ESP32 Pin |
| :--- | :--- |
| **Positive (+)** | Pin D23 (3.3V signal when active) |
| **Negative (-)** | Any GND |

#### Magnetic Reed Switch (Safety Sensor)
| Sensor Terminal | Connection |
| :--- | :--- |
| **Terminal 1** | Pin D5 *(Defined as `INPUT_PULLUP` in code)* |
| **Terminal 2** | GND |

> **Engineering Note:** Pin D5 uses a weak internal pull-up voltage. Lid closed = ESP32 reads `LOW` (0). Lid open = circuit breaks, ESP32 reads `HIGH` (1).

---

## 📊 Feature Comparison Matrix

| Feature | SHEFAA Smart System | Traditional Solutions |
| :--- | :--- | :--- |
| **Alert Accuracy** | Simultaneous visual (LEDs) & audio (Buzzer) alerts. | Audio only, or relies purely on patient memory. |
| **Adherence Monitoring** | Remote, real-time confirmation (Taken/Not Taken). | No reliable method to verify adherence. |
| **Ease of Scheduling** | Effortless adjustment remotely via mobile app. | Requires manual timer resets on physical hardware. |
| **Dose History Logging** | All events recorded securely in a cloud database. | No historical records available. |
