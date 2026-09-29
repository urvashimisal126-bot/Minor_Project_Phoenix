# HydroSense — Product Requirements Document (PRD)

**Project:** HydroSense — Real-Time Water Leakage Detection & Quality Monitoring System
**Type:** Minor Project (Team Phoenix), B.Tech CSE (Cyber Security), AITR Indore
**Version:** 1.0
**Status:** Simulation stage (synthetic data); hardware prototype in progress

---

## 1. Overview

HydroSense monitors a water pipeline section in real time. It detects leakage, tampering/theft, and contamination together, and responds automatically by shutting off supply, without waiting for a manual inspection or a complaint.

## 2. Problem

- India loses an estimated 40% of treated water to leakage and theft (Non-Revenue Water).
- Leaks are found reactively, through visible pooling or billing anomalies weeks later.
- Illegal tapping is invisible without physical inspection.
- Water quality is checked by periodic manual sampling, so contamination is often found only after consumption.
- Existing smart-water solutions usually do either flow metering or quality testing, and rarely trigger an automatic response.

## 3. Goals and Non-Goals

**Goals**
1. Detect a sustained leak within seconds of onset, not days.
2. Detect pipe tampering (drilling/tapping) from vibration.
3. Monitor pH, TDS, turbidity, and temperature continuously and flag out-of-range values.
4. Cut supply automatically on a confirmed leak or tamper, and restore it safely once clear.
5. Avoid false shutdowns caused by ordinary sensor noise.
6. Show all of the above in an interactive simulation for demonstration.

**Non-Goals (this version)**
- Predicting future pipe failure or degradation
- Large-scale field deployment
- Billing, consumer metering, or leak repair workflows
- Automatic cutoff on quality-only violations (alert only)

## 4. Users

| User | Need |
|---|---|
| Municipal water board operator (primary) | Know instantly which pipe section has a leak, theft, or contamination |
| Housing society / campus facility manager | Monitor a private network without manual rounds |
| Rural water scheme operator | Early contamination warning where inspection teams are scarce |

## 5. User Stories

- As an operator, I want an alert the moment a leak starts, so I can stop water loss early.
- As an operator, I want supply to shut automatically on a confirmed leak or tamper, so damage is limited even when no one is watching.
- As an operator, I want a quality alert without a supply cutoff when only pH/TDS/turbidity is off, so I am not cutting water unnecessarily.
- As an operator, I want to see which node and location raised the alert.
- As an evaluator, I want to inject a leak, tamper, or contamination event in a demo and watch the system respond.

## 6. System Architecture

**Node (per pipe section):**
Main Supply → Relay 1 → F1 (inlet flow) → pH / TDS / Turbidity / Temperature sensors → Vibration sensor → F2 (outlet flow) → Relay 2. An ESP32 reads all sensors and drives the relays and two solenoid valves (S1, S2) through 24V transformers.

**Data path:** ESP32 → Firebase (Realtime Database / Firestore) → detection layer → dashboard and alerts.

## 7. Functional Requirements

### FR1. Sensing
| ID | Requirement |
|---|---|
| FR1.1 | Read F1 and F2 flow (L/min), pH, TDS (ppm), turbidity (NTU), temperature (°C), and vibration (g) at a fixed sampling interval |
| FR1.2 | Timestamp every reading and tag it with the node ID and GPS location |
| FR1.3 | Stream readings to the database in real time |

### FR2. Leak detection
| ID | Requirement |
|---|---|
| FR2.1 | Compute the flow differential (F1 − F2) on every reading |
| FR2.2 | Run CUSUM on the differential; raise a leak alarm when the accumulated sum exceeds the threshold |
| FR2.3 | Reset the CUSUM accumulator after an alarm so the alert clears once flow returns to normal |

### FR3. Tamper / theft detection
| ID | Requirement |
|---|---|
| FR3.1 | Maintain a rolling vibration baseline |
| FR3.2 | Raise a tamper alarm when the vibration z-score exceeds the threshold |

### FR4. Quality monitoring
| ID | Requirement |
|---|---|
| FR4.1 | Flag a quality alert when pH, TDS, or turbidity leaves its allowed range |
| FR4.2 | A quality-only alert must not cut supply |

### FR5. General anomaly cross-check
| ID | Requirement |
|---|---|
| FR5.1 | Run an Isolation Forest on the six-signal feature window as a second opinion on unusual combinations |

### FR6. Control response
| ID | Requirement |
|---|---|
| FR6.1 | On a confirmed leak or tamper, open both relays and shut both valves |
| FR6.2 | Latch the shutoff; release only after a run of consecutive clean readings (prevents relay flapping) |
| FR6.3 | Restore supply and return status to NORMAL after release |

### FR7. Dashboard and alerts
| ID | Requirement |
|---|---|
| FR7.1 | Show live readings for all sensors |
| FR7.2 | Plot F1 and F2 on a live chart so a leak is visible as divergence |
| FR7.3 | Show system status (NORMAL / QUALITY ALERT / LEAK / TAMPER), relay states, and valve states |
| FR7.4 | Keep an event log of status changes |
| FR7.5 | Show leak and anomaly location on a map (GPS-tagged nodes) |
| FR7.6 | Push SMS/app alerts for high-severity events |

### FR8. Simulation
| ID | Requirement |
|---|---|
| FR8.1 | Provide toggles to inject a leak, a tamper event, and a contamination event |
| FR8.2 | Provide Start, Stop, and Reset controls |
| FR8.3 | Run on synthetic data with configurable noise |

## 8. Detection Parameters (baseline values)

| Parameter | Value |
|---|---|
| Nominal flow | 15 L/min |
| Injected leak loss | ~2.5 L/min sustained |
| CUSUM threshold / drift | 6.0 / 0.4 |
| Vibration z-score threshold | > 3.0 |
| Vibration baseline window | rolling 50 readings (min 10) |
| pH allowed range | 6.5 – 8.5 |
| TDS limit | < 500 ppm |
| Turbidity limit | < 5 NTU |
| Isolation Forest window / contamination | 30 readings / 0.1 |
| Shutoff release | 5 consecutive clean readings |

These are starting values for simulation and must be re-tuned on real sensor data.

## 9. Non-Functional Requirements

- **Latency:** alert within a few sampling intervals of onset (seconds)
- **Reliability:** no shutoff flicker during a sustained event; no false shutoff from ordinary noise
- **Explainability:** every alert states its cause (leak, tamper, or which quality parameter)
- **Cost:** target roughly ₹2,000–4,000 per node using off-the-shelf parts (estimate)
- **Connectivity:** must tolerate intermittent connectivity; buffer readings locally on the ESP32 and sync when back online
- **Security:** authenticated access to the dashboard and database; relay control commands accepted only from the trusted controller
- **Portability:** demo simulation runs in a browser with no install

## 10. Tech Stack

| Layer | Technology |
|---|---|
| Edge / hardware | ESP32, flow sensors, pH/TDS/turbidity/temperature sensors, vibration sensor, relays, solenoid valves |
| Detection / ML | Python, scikit-learn (Isolation Forest), CUSUM, Z-score |
| Backend / database | Firebase Cloud Functions (Node.js), Firebase Realtime Database / Firestore |
| Frontend | React.js, Tailwind CSS, Chart.js, Leaflet / Google Maps API |
| Services | Blynk (SMS/app alerts) |

## 11. Success Metrics (targets to validate)

| Metric | Target |
|---|---|
| Leak detection latency | within seconds of onset |
| False shutoffs during normal noise | zero across the test scenarios |
| Alert clearing after event ends | clears without flicker |
| Quality violations flagged | 100% of injected contamination events |
| Detection correctness | verified against labeled leak / tamper / contamination scenarios |

## 12. Validation Plan

1. Run labeled synthetic scenarios: normal, leak, tamper, contamination.
2. Confirm each alert fires shortly after injection and clears after the event stops.
3. Compare against a single-threshold baseline (F1 − F2 above a fixed value) for detection latency and false-alarm rate.
4. Repeat with higher noise to test false-shutoff resistance.
5. Later: bench-test with the hardware node and real sensors, then re-tune thresholds.

## 13. Assumptions

- One node covers one pipe section; multiple nodes are placed along a pipeline
- Stable power and connectivity at each node
- Sensor accuracy is roughly ±2–5% on flow
- Synthetic data is a fair stand-in for early logic validation, not for field performance

## 14. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Vibration false positives from traffic or construction | Rolling baseline, z-score, and shutoff confirmation before action |
| Small leaks lost in flow-sensor noise | Shorter node spacing; CUSUM accumulates small drift |
| Sensor drift or fouling over time | Periodic recalibration; alert on stuck or flat readings |
| Thresholds tuned only on synthetic data | Re-tune with real data during hardware testing |
| Plastic pipes dampen vibration | Closer node spacing for PVC pipelines |
| Power or network loss | Local buffering; solar with GSM/LoRa for remote sites |

## 15. Node Spacing (planning estimate)

| Area | Spacing |
|---|---|
| Dense urban | ~300–500 m |
| Semi-urban / campus | ~500 m – 1 km |
| Rural / long line | ~2–3 km |

Final spacing to be set after a pilot, based on pipe material and required leak sensitivity.

## 16. Milestones

| Phase | Deliverable |
|---|---|
| 1 | Detection logic and interactive simulation (done) |
| 2 | Hardware node bench test (ESP32, sensors, relays, valves) |
| 3 | Live Firebase dashboard with GPS-tagged alerts |
| 4 | Threshold tuning on real sensor data |
| 5 | Multi-node pilot on a small pipeline |

## 17. Future Scope

- Predictive maintenance (pipe failure risk)
- LSTM-Autoencoder as an additional detector
- Public SMS/app alert layer
- Solar power with GSM/LoRa for remote nodes
- Multi-node network view with leak localization between nodes

## 18. Open Questions

- Which pipe material and diameter will the first pilot use?
- Which sampling interval balances detection speed against data volume?
- Should quality violations above a severe limit also trigger shutoff?
- Who receives alerts, and through which channel?

## 19. Team

Urvashi Misal, Vanshika Chaudhary
