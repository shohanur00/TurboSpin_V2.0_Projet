# TurboSpin V2 — Current Sensing

## Overview

Accurate current measurement is an important part of a BLDC ESC.

TurboSpin V2 uses low-value shunt resistors together with INA240 current-sense amplifiers to measure motor current.

The measured signals are intended to be sampled by the STM32G431 ADC.

## Shunt Resistors

The design uses:

```text
Shunt Resistance = 1 mΩ
```

Low-value shunt resistors are used to minimize power loss while still producing a measurable voltage proportional to current.

The basic relationship is:

```text
VSHUNT = IMOTOR × RSHUNT
```

For a 1 mΩ shunt:

```text
VSHUNT = IMOTOR × 0.001
```

For example:

```text
I = 20 A

VSHUNT = 20 × 0.001
       = 20 mV
```

## Current Sense Amplifier

**INA240A1D**

The INA240 is used to amplify the small voltage developed across the shunt resistor.

The selected A1 variant provides:

```text
Gain = 20 V/V
```

Therefore:

```text
VOUT ≈ VSHUNT × 20
```

For example:

```text
20 mV × 20 = 400 mV
```

This amplified signal can then be measured by the STM32 ADC.

## Signal Chain

```text
Motor Current
     │
     ▼
┌──────────────┐
│  1 mΩ Shunt  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   INA240A1   │
│    Gain 20   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ STM32G431    │
│     ADC      │
└──────────────┘
```

## PWM Rejection

The INA240 is specifically useful in motor-control applications because of its enhanced PWM rejection capability.

This is important because the current-sense circuitry operates in an environment containing:

* High dV/dt switching
* High di/dt current transitions
* PWM switching edges
* MOSFET gate-drive activity

## ADC Interface

The amplified current-sense outputs are connected to STM32 ADC inputs.

Firmware will eventually use these measurements for:

* Current monitoring
* Over-current protection
* Motor-control algorithms
* Current limiting
* Future FOC implementation

## Layout Considerations

Current sensing is highly sensitive to PCB layout.

The following principles are used:

* Kelvin-style sensing where applicable
* Short shunt-sense connections
* Minimized high-current coupling into sense traces
* Separation from switching nodes
* Careful analog ground routing
* Short connection between shunt and amplifier

## Calibration

Before using current measurements for closed-loop control, the system should be calibrated for:

* ADC offset
* Amplifier offset
* Shunt tolerance
* Gain error
* ADC reference accuracy

A practical calibration flow can be:

```text
Power ON
   │
   ▼
Measure zero-current ADC value
   │
   ▼
Calculate offset
   │
   ▼
Apply offset correction
   │
   ▼
Convert ADC value → Current
```

The final conversion equation will be implemented in firmware after hardware validation.

> Current-sense accuracy should be verified experimentally using a calibrated external measurement method before using the signal for protection or closed-loop control.
