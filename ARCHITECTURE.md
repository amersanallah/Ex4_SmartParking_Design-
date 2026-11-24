# System Architecture & Object-Oriented Design (`Ex4_SmartParking_Design`)

**Course:** 61756 Software Engineering Methods (Academic Year 2026-2027)
**Institution:** ORT Braude College of Engineering | Department of Software Engineering
**Domain:** Smart Campus Parking, LPR, and EV Network (Continued from Exercise 1)

---

## 1. Engineering Team Details

| Role | Full Name | Student ID | Braude Academic Email |
| :--- | :--- | :--- | :--- |
| **Student A (Role A)** | `gaith mostafa` | `327910717` | `gaithmostafa@e.braude.ac.il` |
| **Student B (Role B)** | `amir sanallah` | `212716773` | `amir.sanallah@e.braude.ac.il` |
| **Student c (Role )** | `kosia` | `318487529` | `kosia.habaiallah@e.braude.ac.il` |

* **Group Number:** `G[XX]`
* **GitHub Repository URL:** `https://github.com/amersanallah/Ex4_SmartParking_Design-.git`

---

## 2. Step 1: Business Process Modeling — Activity Diagram (`Visual Paradigm`)

* **PlantUML Source:** [`diagrams/activity_appeal.puml`](diagrams/activity_appeal.puml)
* **Visual Paradigm Model:** [`SystemModel_Ex4.vpp`](SystemModel_Ex4.vpp)
* **Partitions / Swimlanes:** Separates external **Actors** (`Security Guard`, `Student / User`, `Traffic Administrator`) from the internal **System Controller** (`EnforcementControl`), using `initial node`, `fork`/`join` (AND), `decision` (XOR) with explicit `[guards]`, `merge` (OR), and `activity final node`.

![Activity Diagram - Enforcement and Digital Appeal](diagrams/activity_appeal.png)

---

## 3. Step 2: Structural Modeling — ECB Class Diagram (`PlantUML` -> `Visual Paradigm`)

Following the **ECB (Entity-Control-Boundary)** architectural pattern:

| ECB Stereotype | Classes in System | Architectural Responsibility |
| :--- | :--- | :--- |
| **`<<boundary>>`** | `StudentDashboardUI`, `GuardTerminalUI`, `AdminAppealUI`, `LprGateBoundary`, `PaymentGatewayBoundary`, `SmsGatewayBoundary` | Interfaces with external Actors and external hardware/payment/SMS systems. Communicates ONLY with `<<control>>` classes. |
| **`<<control>>`** | `ReservationControl`, `EvBillingControl`, `EnforcementControl` | Multiple specialized controllers orchestrating business rules, timers, concurrency checks, and workflows. |
| **`<<entity>>`** | `User` (`Student`, `Faculty`, `SecurityGuard`, `TrafficAdministrator`), `GuestPass`, `Vehicle`, `ParkingZone`, `ParkingSpot`, `Reservation`, `PaymentAuthorization`, `ChargingSession`, `Violation`, `Appeal` + 4 UML `enum`s | Passive domain data classes storing persistent business state and domain helper methods. |

* **PlantUML Source:** [`diagrams/class_diagram_ecb.puml`](diagrams/class_diagram_ecb.puml)
* **Visual Paradigm Model:** [`SystemModel_Ex4.vpp`](SystemModel_Ex4.vpp)

![ECB Class Diagram](diagrams/class_diagram_ecb.png)

---

## 4. Step 3: Behavioral Modeling — ECB Sequence Diagrams (`PlantUML` -> `Visual Paradigm`)

### 4.1 Sequence Diagram 1: Spot Reservation, LPR Gate Entry & No-Show Destruction
* **PlantUML Source:** [`diagrams/sequence_reservation_ecb.puml`](diagrams/sequence_reservation_ecb.puml)

![Sequence Diagram 1 - Reservation and LPR](diagrams/sequence_reservation_ecb.png)

### 4.2 Sequence Diagram 2: EV Charging 30-Minute Idle Penalty (`loop` & `<<create>>`)
* **PlantUML Source:** [`diagrams/sequence_idle_penalty_ecb.puml`](diagrams/sequence_idle_penalty_ecb.puml)

![Sequence Diagram 2 - EV Idle Penalty](diagrams/sequence_idle_penalty_ecb.png)

---

## 5. Independent AI Prompt & Architectural Design Critique (Part 4)

```text
You are reviewing my UML models for a university Software Engineering course.
Using SYSTEM_STORY.md as the only source of truth, audit class_diagram_ecb.puml
and sequence_reservation_ecb.puml against the two edge cases in Section 3:

Edge Case A – Double-Booking Race Condition:
Two students click "Reserve" on the last available spot in the Student Zone
at the exact same millisecond.
- Does ParkingSpot correctly model the version attribute and tryReserve(expectedVersion)?
- Does the sequence diagram show that only one reservation succeeds and the
  other user receives an error, with no Reservation object created for the loser?

Edge Case B – Physical vs. Electrical EV State:
An EV driver unplugs the cable at minute 29 but leaves the car in the EV bay.
- Does ChargingSession keep isPluggedIn and isPhysicallyOccupied as separate attributes?
- Is the idle penalty decided by physical occupancy and not by cable state,
  so the penalty is still applied after minute 30?

For each edge case, answer: Covered / Partially covered / Not covered,
explain why, and list concrete fixes (attribute, method, message or guard).
Also suggest any other improvements that keep the diagrams consistent with
each other and with ECB rules (Boundary talks only to Control).
Do not rewrite the diagrams – give me a list of findings only.
```

* **Improvement 1 Applied (Race Condition):** Added `version: int` and `tryReserve(expectedVersion: int): boolean` on `ParkingSpot` (and nested `alt acquired == true/false` in Sequence Diagram 1) to prevent simultaneous double-booking.
* **Improvement 2 Applied (EV Physical Occupancy):** Separated `isPluggedIn` from `isPhysicallyOccupied` on `ChargingSession` so unplugging the cable at minute 29 without moving the car still triggers the 30-minute Charger Idle Penalty.

---

## 6. License
Distributed under the **ORT Braude College Academic License** (61756 Software Engineering Methods, 2026-2027).
