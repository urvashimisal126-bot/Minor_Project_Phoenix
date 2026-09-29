# 💧 HydroSense — Real-Time Water Leakage Detection & Quality Monitoring System

**Minor Project · Team Phoenix**
B.Tech CSE (Cyber Security) · Acropolis Institute of Technology and Research, Indore

---

## 📌 Project Overview

HydroSense is an IoT and machine-learning based system that monitors a water pipeline in real time. It detects **leakage**, **tampering/theft**, and **contamination** together, and responds automatically by cutting the supply, without waiting for a manual inspection or a complaint.

## ❗ Problem Statement

India loses an estimated 40% of its treated water to leakage and theft (Non-Revenue Water). Today, leaks are found only when water pools on the surface or a billing anomaly appears weeks later. Illegal tapping goes unseen without physical inspection, and water quality is checked by occasional manual sampling, so contamination is often discovered only after consumption.

Existing smart-water solutions usually do either flow metering or quality testing. None combines detection with an automatic response.

## 🎯 Target Audience

- Municipal water boards / Jal Nigam departments
- Housing societies, campuses, and industrial estates
- Rural and semi-urban water schemes

## ⚙️ How It Works

Each monitoring node sits on a pipe section:

```
Main Supply → Relay 1 → F1 (inlet flow) → pH / TDS / Turbidity / Temperature
            → Vibration sensor → F2 (outlet flow) → Relay 2
                     (ESP32 controller · S1/S2 solenoid valves)
```

| Component | Role |
|---|---|
| F1 / F2 flow sensors | A sustained F1 > F2 gap means water is escaping → leak |
| pH, TDS, turbidity, temperature | Continuous water quality monitoring |
| Vibration sensor | Drilling or tapping on the pipe → tamper/theft |
| ESP32 | Reads sensors, runs detection, syncs to the cloud |
| Relays + solenoid valves (S1, S2) | Cut supply automatically on a confirmed leak or tamper |

## 🧠 Detection Logic

A hybrid detection layer, so no single noisy sensor can trigger a false shutdown:

| Method | Type | Detects |
|---|---|---|
| **CUSUM** | Statistical | Sustained drift in the F1 − F2 flow difference → leak |
| **Z-score** | Statistical | Sudden vibration spike against the rolling baseline → tamper |
| **Isolation Forest** | Unsupervised ML | Unusual combinations across all six signals → general anomaly |
| **Rule-based thresholds** | Standards-based limits | pH 6.5–8.5, TDS < 500 ppm, turbidity < 5 NTU → quality alert |

**Response logic:** a confirmed leak or tamper opens both relays and shuts both valves. The shutoff is latched and only released after 5 consecutive clean readings, which prevents the relay from flapping. A quality-only violation raises an alert without cutting supply.

## 🔬 Simulation

The working simulation is hosted online. Use the toggles to inject a leak, a tamper event, or a contamination event, and watch the detection and relay response in real time.

👉 **[Open Live Simulation](YOUR_SIMULATION_LINK)**

> Note: the simulation runs on synthetic sensor data with configurable noise. It demonstrates the detection logic and control response, not a field deployment.

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Edge / hardware | ESP32, flow sensors, pH/TDS/turbidity/temperature sensors, vibration sensor, relays, solenoid valves |
| Detection / ML | Python, scikit-learn (Isolation Forest), CUSUM and Z-score |
| Backend / database | Firebase (Realtime Database / Firestore) |
| Frontend | React.js, Tailwind CSS, Chart.js |
| External services | Google Maps API (leak geolocation), Blynk (SMS/app alerts) |

## ✅ Expected Outcomes

- Leak and tamper detection within seconds, instead of days or weeks
- Less water wasted, and lower losses for municipalities
- Early contamination alerts, reducing waterborne disease risk
- Aligned with **SDG 6: Clean Water and Sanitation**

## ⚠️ Scope and Limitations

**In scope:** leak detection (F1/F2 differential), tamper detection (vibration), quality monitoring (pH, TDS, turbidity, temperature), automated valve shutoff.

**Limitations:**
- Validated on simulated data, not a large field deployment
- Vibration sensing can be triggered by nearby construction or heavy traffic
- Detects active leaks but does not predict future pipe failure
- Each node needs stable power and connectivity (solar with GSM/LoRa is the future path for remote areas)

## 📄 Project Report

[View Project Report](docs/Minor_Project_Report.pdf)

## 📁 Repository Structure

```
Minor_Project_Phoenix/
│
├── docs/
│   └── Minor_Project_Report.pdf
│
└── README.md
```

## 👩‍💻 Team Members

- Urvashi Misal
- Vanshika Chaudhary
