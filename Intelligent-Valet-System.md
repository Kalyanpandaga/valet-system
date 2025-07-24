# Intelligent Valet System – Multi-Gate Car Retrieval

## 1. System Overview

### Objective

Design an intelligent valet system for a mall with multiple entry/exit gates. The system must predict the user’s intended exit gate without explicit input and dispatch their car seamlessly.

### Key Components

- **User Mobile Web App**

  - Trigger car retrieval with one tap (link sent via SMS).
  - Collects and sends real-time location data (BLE, Wi-Fi, GPS) to backend.

- **In-Mall Sensors**

  - **BLE Beacons**: Placed along walkways and near exit approach corridors.
  - **Virtual Geofences**: Define approach zones for each gate (30–50m before exit).

- **Backend Processing Engine**

  - Multi-sensor fusion and probabilistic scoring model.
  - Manages valet tickets and triggers dispatch logic.

- **Valet Dashboard (Mobile/Tablet)**

  - Receives dispatch requests with ETA.
  - Updates car status: retrieved → en route → delivered.

### High-Level Flow

1. User clicks retrieval link → mobile app activates.
2. App sends continuous sensor data (BLE/Wi-Fi/GPS) to backend.
3. Backend infers likely exit gate via scoring model.
4. Once confident, backend dispatches car to predicted gate.
5. Valet team delivers car in sync with user’s arrival.

**Simple Block Diagram:**

```
User App → BLE/Wi-Fi Data → Backend → Probabilistic Gate Detection → Valet Dashboard → Car Dispatch
```

---

## 2. Exit Gate Detection Logic

### Data Sources & Priority

1. **BLE Beacons (1–5m accuracy)**

   - Most reliable for indoor tracking.
   - Placed densely in approach corridors and intersections.

2. **Geofencing**

   - Virtual zones for approach paths 30–50m before exits.

3. **Wi-Fi Triangulation (10–15m accuracy)**

   - Fallback when BLE unavailable.

4. **Motion Vector (IMU)**

   - Direction + speed from accelerometer/gyroscope.

5. **GPS (low priority)**

   - Used only near entrances with sky visibility.

### Probabilistic Scoring Model

For each gate (A, B, C, D):

```
Score_Gate_X = (Proximity * 0.4) + (Direction * 0.4) + (Dwell Time * 0.2)
```

- **Proximity**: Based on BLE/geofence distance to gate.
- **Direction (Vector)**: User heading towards gate → boosts score.
- **Dwell Time**: Longer presence in approach zone → higher confidence.

**Dispatch Trigger**

- Confidence Score > 90% for ≥ 10 seconds.
- Dispatch valet with ETA matching user pace.

**Flowchart (Simple Blocks)**

```
[User Requests Car] → [Collect Sensor Data] → [Score Each Gate]
    ↓
[Highest Score > 90% ?]
    ↓ Yes
[Dispatch Car to Gate]
```

---

## 3. Handling Real-World Scenarios

### Inaccurate or Lost Signal

- Hierarchy: BLE → Wi-Fi → IMU → GPS.
- If no signal > 90 sec, alert valet manager + notify user.

### User Changes Direction After Dispatch

- Continuously monitor post-dispatch.
- If confidence shifts to another gate:

  - Redirect car if still in parking area.
  - Else notify user: “Car arriving at Gate X.”

### Congestion at Gate

- Backend tracks pending pickups per gate.
- Adjust scoring to favor less crowded gates when user is equidistant.

### Stationary Users

- Hold dispatch until movement detected toward an exit.
- App status: “Awaiting movement towards exit.”

---

## 4. Why This Design Stands Out

- **Multi-sensor fusion** ensures accuracy indoors.
- **Probabilistic scoring** prevents false positives.
- **Dynamic redirection** adapts to user behavior changes.
- **Scalable**: Supports multiple gates and future mall expansions.
- **Low hardware cost**: BLE beacons are inexpensive and energy-efficient.

---

## Conclusion

This intelligent valet system predicts user intent without explicit input, uses layered sensing for accuracy, and handles real-world edge cases gracefully. The result is a seamless, “magical” valet experience where cars arrive at the right gate, at the right time — every time.
