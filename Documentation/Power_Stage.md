# TurboSpin V2 — Power Stage

## Overview

The TurboSpin V2 power stage is a three-phase inverter designed to drive a high-current BLDC motor.

The inverter consists of six N-channel power MOSFETs arranged as three half-bridges.

## Three-Phase Inverter

Each motor phase contains:

* One high-side MOSFET
* One low-side MOSFET
* Gate-drive connection
* Current path through the power stage

```text
                DC+
                 │
        ┌────────┼────────┐
        │        │        │
      Q1A      Q1B      Q1C
        │        │        │
        A        B        C
        │        │        │
      Q2A      Q2B      Q2C
        │        │        │
        └────────┼────────┘
                 │
                GND
```

The three switching nodes are connected to the corresponding BLDC motor phases.

## MOSFET

**TPH1R204PL1**

Key characteristics used in the design include:

* N-channel MOSFET
* 60 V drain-source rating
* Approximately 3 mΩ typical RDS(on)
* High-current capability
* PowerFLAT 5×6 package

Six MOSFETs are used in the complete three-phase bridge.

## Gate Driver

The MOSFET gates are controlled through the **DRV8301** gate-driver IC.

The MCU generates the control signals while the gate driver provides the required interface between the MCU and power MOSFETs.

The gate-driver section is also responsible for maintaining appropriate switching behavior and dead-time between complementary high-side and low-side devices.

## PWM

The target PWM frequency for TurboSpin V2 is:

```text
PWM Frequency = 20 kHz
```

A high-frequency PWM scheme is used to provide suitable motor-control resolution while keeping switching operation above the typical audible range.

## Dead-Time

Dead-time is required between turning off one MOSFET and turning on its complementary MOSFET.

Conceptually:

```text
High Side:  ────────┐       ┌────────
                    │       │
                    └───────┘

Low Side:   ───────────────┐       ┌──
                           │       │
                           └───────┘

                 ← Dead Time →
```

The exact dead-time value is configured according to the selected MOSFET, gate-driver behavior, switching characteristics, and measured hardware performance.

## Current Path

High-current PCB routing is kept as short and low-impedance as practical.

Special attention is given to:

* MOSFET-to-MOSFET current loops
* DC bus connections
* Motor phase routing
* Shunt resistor connections
* Gate-drive loops
* Power and signal ground arrangement

## PCB Considerations

The power-stage layout is designed with the following objectives:

* Minimize high-current loop area
* Reduce parasitic inductance
* Keep gate-drive paths short
* Separate sensitive analog signals from high-current switching nodes
* Provide adequate copper for high-current paths
* Maintain controlled thermal paths around power devices

## Protection Considerations

The hardware includes provisions for monitoring and controlling the motor power stage.

Protection strategy may include:

* Over-current detection
* DC bus over-voltage monitoring
* Under-voltage monitoring
* MOSFET/gate-driver fault handling
* Firmware-based shutdown

The final protection behavior will depend on the firmware implementation and hardware validation.

> Power-stage limits must be experimentally validated before high-current operation.
