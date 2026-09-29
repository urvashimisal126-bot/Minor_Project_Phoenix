# 💧 HydroSense — Real-Time Water Leakage Detection & Quality Monitoring System

> **🎓 Minor Project**  
> **Team Phoenix** · B.Tech CSE (Cyber Security)  
> **Acropolis Institute of Technology and Research (AITR), Indore**

[![Live Demo](https://img.shields.io/badge/Live-Simulation-brightgreen?style=for-the-badge&logo=vercel)](https://water-leakage-detection-quality-mon.vercel.app/)
[![Project Type](https://img.shields.io/badge/Academic-Minor%20Project-blue?style=for-the-badge)](https://github.com/urvashimisal126-bot/Minor_Project_Phoenix)
[![SDG](https://img.shields.io/badge/SDG%206-Clean%20Water%20%26%20Sanitation-00b4d8?style=for-the-badge)](https://sdgs.un.org/goals/goal6)

---

## 📌 Project Overview

**HydroSense** is an IoT and Machine Learning-powered real-time water pipeline monitoring and automated response system developed as a **Minor Project** by **Team Phoenix**. 

It simultaneously monitors pipeline health for:
1. **Flow Differential & Leakages**
2. **Physical Tampering & Water Theft (Illegal Tapping)**
3. **Multi-parameter Water Contamination (pH, TDS, Turbidity, Temperature)**

Unlike conventional smart-water solutions that merely monitor metrics or log data, HydroSense introduces an **automated shutoff control loop** with latched relays and solenoid valves to stop water wastage and contamination spread in real time without human delay.

---

## ❗ Problem Statement

- **Severe Water Loss:** India loses an estimated **40% of its treated water** to leakage, unmetered consumption, and theft (Non-Revenue Water - NRW).
- **Delayed Reactive Maintenance:** Traditional leak detection relies on visible surface pooling or monthly billing discrepancies.
- **Undetected Tampering:** Illegal pipe tapping goes unnoticed in underground or remote lines without manual physical inspections.
- **Delayed Quality Alerts:** Water quality testing is traditionally periodic and manual, leading to contamination being discovered only after outbreaks or consumption.
- **Lack of Integrated Automated Response:** Existing solutions either do flow metering or water testing in silos, with no automatic fail-safe valve shutoff.

---

## 🎯 Target Audience & Beneficiaries

- **Municipal Water Boards & Jal Nigam Departments** (Urban and semi-urban distribution)
- **Residential Housing Societies, Universities & Corporate Campuses**
- **Industrial Estates & Commercial Pipelines**
- **Rural & Semi-Urban Water Supply Schemes** (e.g., Jal Jeevan Mission)

---

## 🔬 Live Interactive Simulation

Experience the interactive web simulation demonstrating real-time sensor streams, anomaly detection algorithms, and automatic valve shutoffs:

🌐 **[Launch HydroSense Live Simulation](https://water-leakage-detection-quality-mon.vercel.app/)**

*(Test real-time responses by toggling leak injection, pipeline tampering, and chemical contamination events.)*

---

## ⚙️ System Architecture & Working Principle

Each monitoring node is installed along pipeline segments to capture multi-modal sensor data:

```
Main Supply ──► Relay 1 ──► [F1: Inlet Flow Sensor]
                                  │
                                  ▼
                    [Water Quality Sensor Suite]
                    • pH Sensor (6.5 – 8.5)
                    • TDS Sensor (< 500 ppm)
                    • Turbidity Sensor (< 5 NTU)
                    • Temperature Sensor (°C)
                                  │
                                  ▼
                    [Vibration / Tamper Sensor (g)]
                                  │
                                  ▼
                Relay 2 ◄── [F2: Outlet Flow Sensor] ──► Distribution
```

### Hardware & Sensor Specifications

| Component | Function & Role |
|---|---|
| **ESP32 Microcontroller** | Edge computing unit collecting sensor data, running local logic, and synchronizing with cloud |
| **Inlet & Outlet Flow Sensors (F1, F2)** | Measures differential flow ($F_1 - F_2$); sustained difference indicates leakage |
| **Vibration Sensor (Piezo/Accelerometer)** | Detects high-frequency mechanical vibration from drilling or illegal tapping |
| **pH Sensor** | Validates safe drinking water acidity/alkalinity range (6.5 – 8.5) |
| **TDS (Total Dissolved Solids) Sensor** | Measures dissolved mineral/contaminant levels (< 500 ppm) |
| **Turbidity Sensor** | Detects suspended particles and water clarity (< 5 NTU) |
| **Temperature Sensor** | Monitors water and environmental temperature |
| **Dual Relays & Solenoid Valves (S1, S2)** | Automated emergency isolation of upstream and downstream flow |

---

## 🧠 Hybrid Detection Logic

HydroSense uses a multi-tiered anomaly detection architecture to ensure rapid response while preventing false alarms caused by noisy sensors:

| Detection Method | Type | Primary Target | Behavior & Thresholds |
|---|---|---|---|
| **CUSUM (Cumulative Sum)** | Statistical | Pipeline Leakage | Tracks cumulative positive drift in $(F_1 - F_2)$ to detect micro-leaks ($h=6.0, k=0.4$) |
| **Rolling Z-Score** | Statistical | Physical Tampering / Theft | Triggers when vibration exceeds $Z > 3.0$ standard deviations above rolling baseline |
| **Isolation Forest** | Unsupervised ML | Multi-signal Anomalies | Evaluates 6-signal feature window ($W=30$) to detect correlated unusual states |
| **Rule-Based Thresholds** | Standards-based | Water Contamination | Direct checks against WHO / BIS drinking water standards |

### Automated Control & Failsafe Mechanism
- **Emergency Cutoff:** On confirmed leakage or tampering, both upstream and downstream solenoid valves are closed.
- **Anti-Flapping & Latch Mechanism:** System latches the shutoff and requires **5 consecutive clean readings** before restoring water supply.
- **Quality Alert Isolation:** Quality-only violations trigger priority alerts without immediately severing essential water supply unless critical limits are breached.

---

## 🛠️ Technology Stack

- **Hardware & Edge:** ESP32, Hall-effect Flow Sensors, Analog pH / TDS / Turbidity Sensors, Piezo Vibration Sensor, 24V Solenoid Valves, Relay Modules.
- **Detection & Analytics:** Python, Scikit-learn (Isolation Forest), Statistical CUSUM & Z-Score algorithms.
- **Backend & Cloud:** Firebase Realtime Database / Firestore, Firebase Cloud Functions.
- **Frontend & Visualization:** React.js, Tailwind CSS, Chart.js, Leaflet / Google Maps API.
- **Alert Services:** Blynk IoT / SMS Webhooks.

---

## 📁 Repository Structure

```
Minor_Project_Phoenix/
├── HydroSense_PRD.md        # Comprehensive Product Requirements Document
├── MinorHydroSense.pdf      # Detailed Academic Minor Project Report & Documentation
├── simulation.md            # Simulation overview and live deployment links
├── README.md                # Project documentation and guide
```

---

## 📄 Academic Project Report

For in-depth mathematical formulations, hardware schematics, and performance benchmarking, refer to our project report:
- 📖 [View Minor Project Report (PDF)](./MinorHydroSense.pdf)
- 📋 [View Product Requirements Document (PRD)](./HydroSense_PRD.md)

---

## 👩‍💻 Team Phoenix

*Department of Computer Science and Engineering (Cyber Security)*  
*Acropolis Institute of Technology and Research, Indore*

- **Urvashi Misal**
- **Vanshika Chaudhary**

---

## 🎯 Sustainable Development Goals (SDG) Alignment

This project directly contributes to:
- **SDG 6:** *Clean Water and Sanitation* — Halting Non-Revenue Water (NRW) loss and securing clean water delivery.
- **SDG 9:** *Industry, Innovation, and Infrastructure* — Smart IoT water grid modernization.
- **SDG 11:** *Sustainable Cities and Communities* — Resilient municipal infrastructure.
