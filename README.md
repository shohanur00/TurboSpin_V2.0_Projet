<div align="center">

# ⚡ TurboSpin V2

### 40A-class Sensorless BLDC / FOC ESC — Hardware Platform

*Design the hardware. Understand the current. Control the motor.*

<br>

![MCU](https://img.shields.io/badge/MCU-STM32G431CBT6-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white)
![Gate Driver](https://img.shields.io/badge/Gate_Driver-DRV8301-CC0000?style=for-the-badge)
![Current Class](https://img.shields.io/badge/Current_Class-40A-orange?style=for-the-badge)

![Hardware](https://img.shields.io/badge/Hardware-Design_Complete-brightgreen?style=flat-square)
![Firmware](https://img.shields.io/badge/Firmware-In_Progress-yellow?style=flat-square)
![Control](https://img.shields.io/badge/Control-Sensorless_FOC-8A2BE2?style=flat-square)
![Current Sense](https://img.shields.io/badge/Current_Sense-INA240A1-blue?style=flat-square)
![Interfaces](https://img.shields.io/badge/Interfaces-DShot_%7C_USB_%7C_SWD-lightgrey?style=flat-square)
![Last Commit](https://img.shields.io/github/last-commit/shohanur00/TurboSpin_V2?style=flat-square)
![Stars](https://img.shields.io/github/stars/shohanur00/TurboSpin_V2?style=flat-square)

</div>

---

**TurboSpin V2** is a custom **40A-class sensorless BLDC ESC** built around the **STM32G431CBT6** microcontroller and the **DRV8301** three-phase gate driver.

It is a compact, learning-oriented hardware platform with a dedicated power stage, current sensing and protection circuitry, and an MCU architecture ready for **six-step sensorless control and sensorless FOC** firmware.

> **Project status:** hardware design complete · firmware development in progress

## 📑 Contents

[Demo](#-demo) · [Key features](#-key-features) · [Architecture](#-hardware-architecture) · [Components](#-main-components) · [Subsystems](#-subsystems) · [Thermal](#-thermal-design) · [PCB philosophy](#-pcb-design-philosophy) · [Repository](#-repository-structure) · [Roadmap](#-roadmap) · [Safety](#-safety) · [Author](#-author)

---

## 🎬 Demo

<!-- Upload the clip to the repo (or drag it into a GitHub issue/README edit box) and update the path. -->
[https://github.com/shohanur00/TurboSpin_V2.0_Projet/blob/main/Test/TurboSpin_V2.mp4]

---

## ✨ Key Features

| | |
|---|---|
| ⚡ **Power** | 40A-class 3-phase MOSFET power stage |
| 🧠 **Control** | STM32G431CBT6 with advanced motor-control timers, fast ADCs and DMA |
| 🔌 **Gate drive** | DRV8301 with fault reporting |
| 📏 **Current sensing** | Low-side shunt + INA240A1 (20 V/V) |
| 🛡️ **Protection** | Hardware over-current architecture, gate-driver fault handling |
| 🔄 **Sensorless** | External comparator for back-EMF zero-cross detection |
| 📡 **Interfaces** | DShot and telemetry (planned), USB (planned), SWD debug |
| 🔥 **Layout** | High-current PCB with thermal considerations |

---

## 🏗️ Hardware Architecture

```text
                 ┌─────────────────────┐
                 │    Control Input    │
                 │  DShot / Future USB │
                 └──────────┬──────────┘
                            ▼
┌───────────────────────────────────────────────────────┐
│                     STM32G431CBT6                     │
│  PWM Generation │ ADC │ Timers │ DMA │ Protection     │
│               Future FOC / Control FW                 │
└─────────────┬─────────────────────────────▲───────────┘
              │ PWM (3 × complementary)     │ Current feedback
              ▼                             │
     ┌─────────────────┐            ┌───────┴──────────┐
     │     DRV8301     │            │ Shunt + INA240A1 │
     │  3-Phase Gate   │            └───────▲──────────┘
     │     Driver      │                    │
     └────────┬────────┘                    │
              ▼                             │
     ┌─────────────────┐                    │
     │  MOSFET Power   │────────────────────┘
     │  Stage (3 × ½B) │
     └────────┬────────┘
              │
        U ────┼──── V ──── W
              ▼
        BLDC / PMSM Motor
```

---

## 🔌 Main Components

| Function | Component |
| --- | --- |
| MCU | STM32G431CBT6 |
| Gate driver | DRV8301DCAR |
| Current amplifier | INA240A1 |
| Current shunt | 10 mΩ class <!-- TODO: verify final shunt value against BOM --> |
| Power MOSFET | TPH1R204PL1 |
| Motor phases | U / V / W |
| Control interface | DShot / future interfaces |
| Debug | SWD |
| USB | USB 2.0 |
| Sensorless feedback | External comparator |
| Input voltage | _TBD_ |

---

## 🧩 Subsystems

### 🧠 Microcontroller — STM32G431CBT6

Chosen for its motor-control peripherals: Cortex-M4 core, advanced PWM timers, high-speed ADCs, analog peripherals, DMA, multiple communication interfaces and SWD debugging.

```text
PWM Generation ──► 3-phase inverter
ADC ─────────────► Phase / bus current · DC bus voltage · Temperature
Comparator ──────► Sensorless feedback
                   └──► Motor control algorithm
```

### ⚡ Power Stage

A 3-phase MOSFET inverter with one high-side and one low-side switch per phase.

```text
                DC BUS
                  │
        ┌─────────┼─────────┐
       HS-U      HS-V      HS-W
        ├── U     ├── V     ├── W
       LS-U      LS-V      LS-W
        └─────────┼─────────┘
                 GND
```

Layout considerations: high-current paths, switching loops, gate-drive return paths, power/logic ground separation, current-sense routing, thermal dissipation, switching-node noise and low-inductance connections.

### 🔋 Gate Driver — DRV8301

Handles high-side / low-side gate drive, gate-drive supply management, current-sense amplifier interface, protection and fault reporting. The STM32 generates complementary PWM with firmware-controlled dead time to prevent shoot-through.

### 📏 Current Sensing

```text
Motor current ─► Low-side shunt ─► INA240A1 (20 V/V) ─► STM32 ADC
```

Used for current monitoring, limiting, protection, future FOC current-loop control and diagnostics. Kelvin-style routing around the shunt and amplifier keeps measurement error and switching noise low.

### 🛡️ Protection

Hardware and firmware layers, designed so critical faults can disable the power stage independently of normal control where practical:

* Over-current protection
* Gate-driver fault detection
* DC bus over-voltage / under-voltage monitoring
* MOSFET / board temperature monitoring
* PWM shutdown on fault

### 🔄 Sensorless Feedback

```text
Motor back-EMF ─► Comparator ─► STM32G431 ─► Zero-cross / rotor position
```

Supports six-step sensorless commutation, back-EMF zero-cross detection, sensorless startup and future sensorless FOC research.

### 🔌 USB and Communication

Planned: configuration, debugging, firmware update, parameter monitoring and PC tools over USB; DShot and telemetry for flight-controller use; SWD for debug. The USB pair is routed short with series termination. **Firmware support is not implemented yet**, and protocol details may evolve.

---

## 🌡️ Thermal Design

Key considerations: MOSFET dissipation, copper area around power devices, thermal vias, high-current routing, gate-driver thermals, shunt dissipation and PCB temperature monitoring.

> [!NOTE]
> **40A is a design-class target, not a guaranteed continuous rating.** Real capability depends on PCB construction, copper thickness, cooling, switching conditions, motor and environment.

---

## 🎯 PCB Design Philosophy

| Area | Approach |
| --- | --- |
| **Power path** | Short, wide, low-impedance, low-inductance |
| **Gate drive** | Compact loops to reduce ringing, EMI, switching loss and false triggering |
| **Current sense** | Sensitive traces routed away from switching nodes |
| **Grounding** | Power ground (high-current return, switching current) kept separate from signal/analog ground (ADC, current sense, control) to reduce ADC noise |

---

## 📂 Repository Structure

```text
TurboSpin_V2/
├── Hardware/
│   ├── Schematic/
│   ├── PCB/
│   ├── Gerber/
│   └── BOM/
├── Documentation/
│   ├── Hardware_Architecture.md
│   ├── Power_Stage.md
│   ├── Current_Sensing.md
│   └── Design_Notes.md
├── Firmware/
│   └── README.md
├── Images/
└── README.md
```

> Firmware directories may stay empty or hold development notes until firmware work begins.

---

## 🧪 Roadmap

**Hardware**

- [x] System architecture
- [x] Schematic design
- [x] Power-stage design
- [x] Current-sense design
- [x] MCU peripheral allocation
- [x] PCB design
- [ ] PCB fabrication
- [ ] Hardware bring-up
- [ ] Power-stage validation
- [ ] Current-sense calibration
- [ ] Protection testing

**Firmware**

- [ ] MCU startup
- [ ] PWM driver
- [ ] ADC driver
- [ ] Current measurement
- [ ] Gate-driver interface
- [ ] Fault handling
- [ ] Six-step BLDC control
- [ ] Sensorless zero-cross detection
- [ ] DShot
- [ ] Telemetry
- [ ] Closed-loop control
- [ ] Sensorless FOC
- [ ] USB configuration interface

---

## ⚠️ Safety

> [!WARNING]
> This project involves **high current, fast switching and potentially dangerous motors**. Improper testing can cause MOSFET failure, PCB or battery damage, fire, motor overspeed and flying mechanical parts.
>
> Test with current limiting, protective equipment and controlled conditions. Do not assume the 40A target is safe or achievable until the full thermal, electrical and mechanical system is validated.

---

## 👨‍💻 Author

**Md. Shohanur Rahman** — Embedded Systems / Hardware Design Engineer

Embedded systems · Motor control · BLDC / PMSM · Sensorless FOC · STM32 · Power electronics · PCB design · Hardware bring-up · Real-time firmware

[![GitHub](https://img.shields.io/badge/GitHub-shohanur00-181717?style=flat-square&logo=github)](https://github.com/shohanur00)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Shohanur_Rahman-0A66C2?style=flat-square)](https://www.linkedin.com/in/engr-shohanur-rahman-181a69250)

---

<div align="center">

## ⭐ Why TurboSpin?

TurboSpin V2 isn't meant to be just another ESC board. It's a complete learning and engineering platform where **power electronics, PCB design, embedded firmware, motor control and real-time systems** come together.

**If you find this project useful, consider giving it a ⭐**

</div>

