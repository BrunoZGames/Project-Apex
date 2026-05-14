# 🏎️ Project Apex: Production-Grade AI Race Engineer & 60Hz Telemetry Orchestration Suite

Project Apex is a high-performance, edge-computed engineering layer designed to operate symmetrically with the **EA Sports F1 25** racing engine. Built for low-latency tactical feedback, the platform isolates high-frequency, physics-bound telemetry ingestion from synchronous cognitive reasoning. By parsing the 60Hz local UDP stream within an asynchronous, multi-threaded worker framework, Project Apex transforms volatile binary signals into persistent states, passing tightly scoped event markers to the Google Gemini API only when critical thresholds are breached.

## 🏗️ Architectural Core & Design Framework

The codebase is engineered around a **Microkernel / Plugin Architecture Pattern** paired with an asynchronous central registry framework.

* **The Plugin Registry:** Every analytical module runs as an independent **Daemon** extending a common abstract base class. New modules integrate with zero structural modifications to the underlying socket logic.
* **The Non-Blocking Event Tracking:** Daemons process high-frequency telemetry data and pass event blocks through a central registry without blocking execution. Consumers can handle data mapping, local SQLite database storage, and core API operations concurrently.

---

## 🧠 The 8 Analytical Logic Daemons

### 1. Global Strategy & Dynamic Pitstop Engineer
A predictive race strategist that evaluates pit stop windows using a custom spatio-temporal gap re-entry model.

$$\text{Projected Race Time}_{\text{Driver}} = \text{Current Race Time}_{\text{Driver}} + \text{Effective Pit Loss}$$

* **SC vs. VSC Physics:** Under a Virtual Safety Car (VSC), competitor delta times are legally locked, rendering temporal spacing static. Because pit lane transit times remain steady, your relative time penalty falls significantly, prompting the daemon to flag clear pit exit opportunities. Under a full Safety Car (SC), the field packs together, erasing time gaps. Pitting immediately on Lap 1 exploits the uncompressed spacing without major position drops, whereas pitting on subsequent loops leaves you at the absolute rear of the train.
* **Rule Compliance & Stage Profiling:** Ensures compliance with mandatory regulations forcing the use of at least two unique dry compounds during a race. If an early neutralization occurs while running a compound like the Hard tire, the daemon overrides game suggestions to prevent a rule violation. Distances are classified into early, mid, and late phases; during late-stage scenarios, it restricts pitting to prioritize raw defensive track position over fresh rubber.
* **Undercut/Overcut Modeling & Rival Spy:** Monitors the pit entry states of active cars simultaneously across the field to calculate immediate strategic advantages.
* **Weather Horizon:** Parses incoming 5, 15, and 30-minute weather arrays to seamlessly extend dry stints during an impending storm, transitioning directly to Intermediate tires to save an entire stop.

### 2. Spatio-Temporal Traffic & Out-Lap Radar
Constructs a real-time 2D spatial coordinate map of all 20 cars during Qualifying and Practice. Projects opponent paths to guarantee a 6.5-second window of clean track air before a garage release. On out-laps, it identifies slow cars prepping tires and flags closure rates to preserve clean air for flying laps.

### 3. Tire Carcass & Thermal Degradation Engine
Tracks surface and core carcass temperatures at 60Hz, separating decay behaviors across Soft, Medium, and Hard compounds. Shifts to an exponential decay calculation if sliding or lock-ups push surface temperatures past the 110°C threshold, flagging a strategic alert before hitting the 60% mechanical grip cliff.

### 4. Kinematic Setup & Dynamics Analyzer
Operating exclusively in Free Practice, this daemon evaluates car mechanical stability across track sectors to construct a real-time chassis map. It processes suspension travel arrays and lateral/longitudinal G-forces to isolate aerodynamic floor bottoming-out stalls or low-speed mechanical understeer.

### 5. ERS State-of-Charge & Clipping Optimizer
Focuses on electrical energy utilization along major straightaways. Detects when the car hits aerodynamic drag saturation ("clipping") without acceleration gains and advises shifting from Overtake to Balanced mode to preserve battery power.

### 6. Predictive Fuel & Lift-and-Coast Calculator
Regulates fuel burn constants to eliminate underweight penalties by sampling volume down to the milligram. If a negative fuel delta develops, it calculates the exact distance parameters required to lift the throttle before braking zones to recover fuel with minimal lap time loss.

### 7. Dynamic Damage & Aero-Loss Assessment Engine
Maps component structural impacts into precise performance deficits based on integer damage scales. Balances cumulative lap time loss staying out against the 23-second pit stop penalty required for a full structural nosecone replacement to deliver an optimal strategic directive.

### 8. Brake Thermal & Pressure Matrix
Monitors deceleration infrastructure to keep brake operating boundaries within the stable 400°C to 800°C window. Flags surface glazing or fluid boiling fade at 60Hz and prompts active brake bias migrations to stabilize stopping loads.

---

## 🎙️ Two-Way Radio Voice Pipeline (PTT Loop)

Project Apex implements a completely offline, low-latency voice loop to ensure split-second strategic response times:
1. **Hardware Button Hook:** Intercepts racing wheel Push-To-Talk (PTT) button inputs via local event listeners.
2. **RAM Ingestion:** Logs microphone input directly into an isolated RAM buffer to eliminate file I/O performance bottlenecks.
3. **Local Speech-to-Text:** Transcribes voice data within 150ms using a local instance of the OpenAI Whisper engine, filtering out ambient simulation noise.
4. **Context Serialization:** Serializes transcribed text into a prompt block alongside structured snapshots derived from core logic daemons.
5. **Cognitive Synthesis:** Dispatches the combined payload to the developer-licensed Google Gemini API environment.
6. **Local Audio Rendering:** Transforms the concise text strategy command back to speech using a local 'Edge-TTS' module straight to the driver's headset.

---

## 📊 Relational Data Warehouse (SQLite + PowerBI)

Telemetry data is structured and saved into a local SQLite database to decouple real-time processing from analytical reporting tools:
* **sessions:** Tracks unique session IDs, track IDs, session types, ambient temperatures, and weather states.
* **laps:** Tracks lap time progression, sector times, lap numbers, compound logs, and fuel deltas.
* **telemetry_samples:** Stores 60Hz high-resolution samples tracking Speed, Throttle, Brake, and tire temperatures.
