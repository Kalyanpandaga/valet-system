# Intelligent Valet System – Multi-Gate Car Retrieval

## 1. System Overview

### Objective

Design a robust valet system for a multi-gate mall that predicts a user’s exit gate without explicit input and ensures their car is ready at the correct gate just as they arrive.

### Key Components

- **User Mobile Web App (Single-Use)**

  - Accessed via SMS link after drop-off.
  - Minimal interaction: one-tap car request.
  - Collects sensor data (BLE, Wi-Fi, GPS, IMU) and sends to backend.

- **In-Mall Sensor Network**

  - **BLE Beacons:** Primary indoor tracking (1–5m accuracy), placed along corridors and near exits.
  - **Virtual Geofences:** Define approach zones 30–50m before gates; entering zone signals intent.
  - **Wi-Fi Triangulation:** Secondary fallback for positioning (10–15m accuracy).

- **Backend Processing Engine (Cloud-Based “Brain”)**

  - Multi-sensor fusion for high-accuracy tracking.
  - Dynamic probabilistic scoring model to infer gate intent.
  - Manages valet ticket states and triggers dispatch.

- **Valet Operations Dashboard**

  - Mobile/tablet interface for valet staff.
  - Receives gate assignments, user ETA, and updates car status (retrieved → en route → delivered).

### High-Level Flow

1. User drops car and receives SMS link.
2. User taps link → app activates and streams sensor data.
3. Backend processes data, calculates confidence score for each gate.
4. When one gate surpasses threshold, dispatch is triggered.
5. Valet delivers car in sync with user’s arrival.

**Block Diagram:**

```
[User App] → [BLE/Wi-Fi/IMU Data] → [Backend Scoring Model] → [Valet Dashboard] → [Car Dispatch to Gate]
```

---

## 2. Exit Gate Detection Logic

### Data Sources & Priority

1. **BLE Beacons (Primary)** – Highest accuracy for indoor tracking.
2. **Geofencing** – Strong intent signal when entering approach zones.
3. **Wi-Fi Triangulation** – Secondary fallback in beacon shadow zones.
4. **Motion Vector (IMU)** – Detects user direction and pace.
5. **GPS (Lowest priority)** – Used only near entrances with sky visibility.

### Dynamic Confidence Scoring Model

For each gate (A, B, C, D), confidence score is updated every 5 seconds:

```
Confidence_Score_Gate_X = (Proximity × 0.4) + (Direction × 0.35) + (Dwell_Time × 0.15) + (History × 0.10)
```

- **Proximity:** BLE/geofence distance to gate.
- **Direction:** Motion vector toward gate.
- **Dwell Time:** Duration within approach zone.
- **History:** Optional bias for frequently used gates by same user.

### Dispatch Trigger

- Trigger when highest gate score > 95% for ≥10 seconds.
- System computes ETA based on walking speed and notifies valet with timing.

**Logic Flow:**

```
[Car Request] → [Sensor Data Fusion] → [Calculate Scores] → [Is Highest > 95%?]
    ↓ Yes
[Dispatch Car] → [Notify Valet & User]
```

---

## 3. Handling Real-World Limitations

### Inaccurate or Lost Signal

- Fallback hierarchy: BLE → Wi-Fi → IMU → GPS.
- If all signals lost >90s: alert valet manager + notify user.

### User Changes Direction After Dispatch

- Continuous scoring post-dispatch.
- If new gate becomes dominant before “Point of No Return,” redirect car.
- Otherwise, notify user to proceed to original gate.

### Congestion at Gates

- Track active dispatches per gate.
- Bias scoring toward less congested gate if equidistant options exist.

### Stationary Users After Request

- Hold dispatch until movement detected toward exit.
- App status: “Awaiting movement towards exit.”

---

## 4. Unique Advantages of Design

- **Multi-Sensor Fusion**: BLE, Wi-Fi, IMU combined for precision.
- **Predictive, Not Reactive**: Infers exit without explicit input.
- **Dynamic Adaptation**: Redirect logic handles real-world user behavior changes.
- **Historical Learning**: Improves predictions over repeat visits.
- **Scalable & Cost-Efficient**: BLE beacons are low-cost and easily expandable.
- **Privacy Respectful**: Session-based links; no permanent tracking.

---

## 5. Conclusion

This intelligent valet system creates a seamless experience by predicting exit gates with high confidence and adapting dynamically to user behavior. With layered sensing, probabilistic modeling, and robust fallback mechanisms, it ensures cars are ready at the right gate at the right time—delivering a premium, “magical” service for mall customers.
