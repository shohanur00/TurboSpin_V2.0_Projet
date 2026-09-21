# TurboSpin V2 — Design Notes

## Project Goal

TurboSpin V2 was developed as a personal learning and development platform for high-current BLDC motor control.

The project combines:

* Embedded systems
* Power electronics
* Motor control
* Analog signal measurement
* PCB design
* Real-time firmware development

The primary goal is to build and validate the hardware platform first and then develop the motor-control firmware incrementally.

## MCU Selection

### STM32G431CBT6

The STM32G431CBT6 was selected as the primary controller because it provides peripherals suitable for real-time motor-control applications.

Important requirements include:

* High-performance Cortex-M4 core
* Advanced timers
* Multiple ADC resources
* Fast interrupt response
* Flexible GPIO
* Communication peripherals
* Suitable development ecosystem

## Gate Driver Selection

### DRV8301

The DRV8301 was selected as the three-phase MOSFET gate-driver solution.

The device provides a dedicated interface between the MCU control signals and the external MOSFET bridge.

This reduces the need to implement the complete gate-drive stage using discrete circuitry.

## MOSFET Selection

### TPH1R204PL1

The selected MOSFET provides a combination of:

* 60 V voltage rating
* Low RDS(on)
* High current capability
* Compact power package

The low conduction resistance is particularly useful for reducing conduction losses in the three-phase inverter.

Actual operating current capability depends on PCB thermal performance, switching losses, cooling, bus voltage, motor characteristics, and operating conditions.

## Current Measurement

Low-value shunts and INA240 current-sense amplifiers were selected to provide a measurement path suitable for motor-control development.

The design intentionally keeps the shunt resistance low to reduce power dissipation while providing sufficient voltage for amplification.

## Switching Frequency

The target PWM frequency is:

```text
20 kHz
```

The frequency was selected as a practical starting point for BLDC motor-control development.

Actual switching losses and thermal performance must be validated experimentally.

## PCB Design Philosophy

The PCB was designed with particular attention to the difference between:

### Power Domain

High-current and high-frequency switching paths include:

* DC bus
* MOSFET bridge
* Motor phases
* Gate-drive loops
* Current shunts

### Signal Domain

Sensitive signals include:

* Current-sense outputs
* ADC inputs
* Control signals
* Communication interfaces
* Oscillator signals

The layout attempts to minimize unwanted coupling between these domains.

## Grounding

The design uses careful separation and routing of power-current paths and sensitive signal paths.

High-current return paths should not share narrow traces with sensitive analog measurement signals.

The final grounding performance must be verified experimentally using oscilloscope measurements and current-sense validation.

## USB

A USB Type-C connector is included on the board for development and interface purposes.

USB signal integrity depends on:

* Differential-pair routing
* Trace impedance
* Trace length matching
* Connector quality
* ESD protection
* Appropriate series termination

The final USB performance will be validated during hardware testing.

## Oscillator

An external 8 MHz CMOS oscillator is used as the clock source for the MCU.

The oscillator provides a stable external reference for the system clock configuration.

## Development Strategy

The project is being developed incrementally.

### Stage 1 — Hardware

* Schematic
* Component selection
* PCB layout
* Manufacturing
* Visual inspection

### Stage 2 — Hardware Bring-Up

* Power rails
* MCU programming
* Clock verification
* GPIO validation
* Gate-driver validation
* ADC validation
* Current-sense validation

### Stage 3 — Motor Control

Planned firmware development:

```text
PWM
 ↓
6-Step Commutation
 ↓
Open-Loop Startup
 ↓
Back-EMF / Zero-Cross Detection
 ↓
Sensorless BLDC Control
 ↓
DShot
 ↓
Telemetry
 ↓
FOC Research
```

## Current Project Status

The hardware design is the current focus of TurboSpin V2.

Firmware development has not yet been completed.

This repository will be updated as hardware bring-up, firmware development, testing, and motor-control experiments progress.

## Disclaimer

TurboSpin V2 is a personal engineering and learning project.

High-current motor controllers can involve hazardous voltages, currents, temperatures, and rotating machinery.

Testing should be performed with appropriate laboratory equipment, current limiting, protection, and safety procedures.

Do not assume the design is production-ready or that the stated component ratings represent the safe operating limit of the complete system.
