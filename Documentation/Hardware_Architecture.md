# TurboSpin V2 — Hardware Architecture

## Overview

TurboSpin V2 is a custom high-current BLDC ESC hardware platform built around the STM32G431CBT6 microcontroller.

The hardware is designed as a foundation for developing sensorless BLDC motor control, digital throttle input, telemetry, and future FOC-based control.

The design is divided into several functional blocks:

```text
                    ┌──────────────────────┐
                    │      USB Type-C      │
                    │  Development / I/O   │
                    └──────────┬───────────┘
                               │
                               ▼
┌────────────────────────────────────────────────────┐
│                 STM32G431CBT6                      │
│                                                    │
│  PWM Generation   ADC   Timers   GPIO   Interfaces │
└───────┬──────────────┬───────────────┬─────────────┘
        │              │               │
        │              │               └── Control / I/O
        │              │
        │              └── Current / Voltage Feedback
        │
        ▼
┌──────────────────────┐
│      DRV8301         │
│   Gate Driver        │
└──────────┬───────────┘
           │
           ▼
┌────────────────────────────────┐
│       3-Phase Power Stage      │
│                                │
│   Phase A   Phase B   Phase C  │
│     │         │         │      │
│     └─────────┴─────────┘      │
└───────────────┬────────────────┘
                │
                ▼
             BLDC Motor
```

## Main Components

### Microcontroller

**STM32G431CBT6**

The STM32G431 is the main control device of TurboSpin V2.

Planned responsibilities include:

* PWM generation
* Motor commutation timing
* ADC sampling
* Current measurement
* Rotor position estimation
* Zero-crossing detection
* DShot input
* Telemetry
* Protection and fault handling

The MCU was selected because of its high-performance Cortex-M4 core and motor-control-oriented peripherals.

### Gate Driver

**DRV8301DCAR**

The DRV8301 is used to interface the STM32 control signals with the three-phase MOSFET power stage.

The gate-driver stage provides the required gate-drive capability for the external N-channel MOSFET bridge.

### Power Stage

The three-phase inverter consists of six N-channel power MOSFETs.

Each motor phase is controlled by a high-side and low-side MOSFET pair.

```text
             DC BUS
               │
        ┌──────┼──────┐
        │      │      │
       HS-A   HS-B   HS-C
        │      │      │
        A      B      C ────► BLDC Motor
        │      │      │
       LS-A   LS-B   LS-C
        │      │      │
        └──────┴──────┘
               │
              GND
```

### Current Sensing

TurboSpin V2 uses low-value shunt resistors together with INA240 current-sense amplifiers.

The current-sense signals are routed to the STM32 ADC subsystem for measurement and future closed-loop motor-control algorithms.

### Power Regulation

A dedicated 3.3 V LDO supplies the low-voltage digital and analog circuitry.

The power architecture separates the high-current motor path from sensitive control and measurement circuitry as much as practical.

## Development Direction

The hardware is intended to support the following development stages:

1. Hardware bring-up
2. PWM and gate-driver validation
3. Current-sense validation
4. 6-step BLDC commutation
5. Sensorless operation
6. DShot interface
7. ESC telemetry
8. Future FOC development

> Firmware implementation is currently under development.
