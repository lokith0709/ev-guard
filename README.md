# EV-GUARDIAN 2.0

**Intelligent EV Battery Safety, Predictive Diagnostics and Safe-Drive Interlock**

A low-cost embedded diagnostic layer that sits beside the BMS of a low-voltage 4S battery demonstrator. It explains *what* is abnormal, *where* it is occurring, *how serious* it is and *what should happen next* — and blocks the drive / contactor permit whenever a required safety input is missing, invalid or critical.

> Mini project (II Year B.E. ECE) — KPR Institute of Engineering and Technology (Autonomous), Coimbatore. Team **Electronova**, November 2026.

> ⚠️ **Status:** the software-in-the-loop (SIL) simulator is complete. The hardware prototype is **under development**. All results below come from simulated faults on virtual sensor data — not from measurements on a physical battery.

![Concept render](docs/images/fig3-1-concept-render.png)

## Concept

`SENSE → DIAGNOSE → LOCALIZE → PREDICT → PROTECT → INTERLOCK → RECORD`

- **Signal validation** – range, freshness and plausibility checks on every input
- **Feature extraction** – cell-voltage imbalance (ΔV), temperature difference (ΔT), temperature rise rate (dT/dt), current trend
- **Hybrid diagnosis** – deterministic rules with hysteresis + lightweight edge anomaly score (ML is never the sole safety mechanism)
- **Fault localization** – suspected cell / pack / sensor network / cooling subsystem
- **Explainable EV Safety Score** – 0–100 with the reasons for every reduction
- **Closed-loop protection** – buzzer/LED warning, fan cooling, relay isolation
- **Safe-Drive Interlock** – fail-safe FSM, default = *no permit*
- **Safety Black Box** – microSD event log for post-event review

![Architecture](docs/images/fig3-3-architecture.png)

## Try the simulator

The simulator is a single self-contained HTML file — no install needed.

1. Open [`simulator/index.html`](simulator/index.html) in any modern browser (or enable GitHub Pages — see below).
2. Use the **Fault Injection** buttons: Normal, C3 Thermal, C2 Undervoltage, Overcurrent, Sensor Failure, Cooling Failure, Multiple Fault, Recover.
3. Watch the Safety Score, diagnosis, drive permit and Black Box react.

| Healthy | C3 thermal fault | Multiple fault |
|---|---|---|
| ![normal](docs/images/fig4-1-simulator-normal.png) | ![c3](docs/images/fig4-2-simulator-c3-thermal.png) | ![multi](docs/images/fig4-3-simulator-multiple-fault.png) |

## Simulated results

| Scenario | Max temp (°C) | Current (A) | Pack V | Score | State | Suspected location | Permit |
|---|---|---|---|---|---|---|---|
| Normal | 32 | 8.2 | 16.08 | 94 | SAFE | No fault | Allowed |
| C3 thermal | 48 | 8.2 | 16.08 | 50 | WARNING | Cell 3 | Blocked |
| C2 undervoltage | 32 | 8.2 | 14.68 | 56 | WARNING | Cell 2 | Blocked |
| Overcurrent | 32 | 20.7 | 16.08 | 48 | WARNING | Pack | Blocked |
| Sensor failure | 32 | 8.2 | 16.08 | 30 | CRITICAL | Sensor network | Blocked |
| Cooling failure | 45.5 | 8.2 | 16.08 | 48 | WARNING | Cooling subsystem | Blocked |
| Multiple fault | 51 | 22.2 | 15.08 | 30 | CRITICAL | C2 / C3 / pack | Blocked |

The simulator's score profile is **scripted**; the formula-based score (`Score = clamp(100 − Σ wᵢ·Rᵢ, 0, 100)`) is to be implemented and tuned in firmware.

## Hardware (planned)

ESP32 DevKit · 4S cells + BMS board · 4× DS18B20 · INA219 · cell-voltage MUX · OLED/touch display · relay, 5 V fan, buzzer, LEDs · microSD module · hard-wired E-stop. Estimated budget ≈ ₹4,400. Details in [`hardware/BOM.md`](hardware/BOM.md).

## Repository layout

```
ev-guardian-2.0/
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── EV-GUARDIAN_2.0_Project_Report.pdf
│   ├── pseudocode.md
│   └── images/
├── simulator/
│   └── index.html
├── hardware/
│   ├── BOM.md
│   └── bill_of_materials.csv
└── firmware/
    └── README.md
```

## Publish the simulator with GitHub Pages

*Settings → Pages → Source: Deploy from a branch → Branch: `main`, folder `/ (root)`*. The simulator will then be at  
`https://<your-username>.github.io/<repo-name>/simulator/`

## Safety boundaries

- Demonstrated only on **low-voltage, current-limited** hardware with simulated / injected faults.
- Thermal runaway, short-circuit, puncture and other destructive lithium-battery tests are **excluded**.
- The Safety Score is a prototype engineering indicator, not a certified safety metric.
- No ISO 26262 compliance is claimed. Not for use in a real vehicle.

## Team

| Name | Roll no. |
|---|---|
| Malini K | 25EC121 |
| Haresh S | 25EC066 |
| Madhan Raja S | 25EC116 |
| Lokith G | 25EC115 |

**Guide:** Supriya M, Dept. of ECE · **HoD:** Dr. M. Kathirvelu

## License

Released under the [MIT License](LICENSE).
