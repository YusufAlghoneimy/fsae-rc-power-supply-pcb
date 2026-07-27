# FSAE RC Power Supply PCB

Design and validation record for the power regulation board of a 1/10-scale Formula SAE
inspired RC validation platform, part of the larger Passive Autonomy project.

This board takes raw battery voltage and produces two clean, regulated output rails used
to power the platform's microcontrollers, FPGA, and sensors. All values on this board
were simulated and validated in LTspice before being carried into the schematic and PCB
layout.

<img width="317" height="594" alt="image" src="https://github.com/user-attachments/assets/78c51b22-0703-4046-8b81-6a00a7d4a0c7" />
<img width="764" height="346" alt="image" src="https://github.com/user-attachments/assets/49f4d9a0-9c0d-40a4-b9c0-a70d80a37c9e" />


## Why This Exists

The platform's compute and sensor electronics cannot run directly from a 2S LiPo
battery. A 2S pack swings from roughly 8.4 V fully charged down to 6.0 V near depletion,
while the onboard electronics require a stable 5 V and a stable 3.3 V regardless of
battery state. This board performs that regulation in two stages, isolated from the
vehicle's separate high current drive electronics, so that switching noise from the
motor side of the platform does not corrupt the sensitive logic and sensor rails.

## Architecture

Battery voltage is stepped down in two sequential stages.

1. **Buck converter stage** - steps the raw battery voltage down to a regulated 5 V rail
2. **Ferrite bead isolation** - separates the 5 V rail from the following stage to reduce
   noise coupling
3. **Low dropout regulator stage** - steps the 5 V rail down further to a clean,
   regulated 3.3 V rail

## Key Components

| Stage | Part | Package |
|---|---|---|
| Buck converter | LT8610 | MSOP-16 |
| Low dropout regulator | LT1761 / LT1762 | SOT-23-5 |
| Ferrite bead | Murata BLM18 series | 0603 |

## Validated Design Values

All values below were confirmed through LTspice simulation, including startup behavior,
full battery voltage range sweep, and a realistic load step test simulating sensor
sampling bursts.

| Parameter | Value |
|---|---|
| Inductor (L1) | 2.5 uH |
| Output capacitor, buck (C5) | 47 uF |
| Feedback divider, top (R1) | 243 kOhm |
| Feedback divider, bottom (R2) | 18.2 kOhm |
| RT resistor | 1 MOhm |
| Input capacitor (C1) | 4.7 uF |
| Soft start capacitor (C2) | 10 nF |
| INTVcc capacitor (C3) | 1 uF |
| BST capacitor (C4) | 0.1 uF |
| BIAS resistor (R3) | 100 kOhm |
| LDO output capacitor | 1 uF |
| LDO bypass capacitor | 0.01 uF |
<img width="969" height="455" alt="image" src="https://github.com/user-attachments/assets/ec352bee-a7a8-411a-b611-46c5f434ce7e" />
<img width="966" height="476" alt="image" src="https://github.com/user-attachments/assets/39ab36f4-7d4d-4dd4-afc9-f137f457b0b0" />
<img width="615" height="585" alt="image" src="https://github.com/user-attachments/assets/be8837cd-32db-44e9-9d00-5d79160db640" /> <img width="646" height="612" alt="image" src="https://github.com/user-attachments/assets/e7352fbd-3f21-4ebb-9ed7-5e1d3cb7a1c6" />




## Current Budget

The 5 V rail was sized against the combined estimated draw of every downstream board
and sensor.

| Load | Estimated Draw |
|---|---|
| Microcontroller | 100 mA |
| FPGA | 500 mA |
| Telemetry module | 250 mA |
| Sensor suite (IMU, current sensor, wheel speed sensors) | 50 mA |
| Subtotal | 900 mA |
| Design target, with margin | 1.5 A |

## Results Validated in Simulation

- Startup behavior confirmed clean, with the output ramping smoothly to its regulated
  value with no overshoot or oscillation.
- Output regulation confirmed stable across the full real battery voltage range, from
  6.0 V to 8.4 V.
- A simulated load step, representing a burst of sensor sampling current, was applied to
  the 3.3 V rail and confirmed to produce only a brief, small magnitude dip with no
  sustained oscillation, correctly recovering to the regulated value.

## Connectors

| Reference | Purpose |
|---|---|
| J1 | Battery input |
| J2 | 5 V output |
| J3 | 3.3 V output |
| J4 | Test and debug header |

## PCB Design Notes

The board is a two layer design. Power carrying traces use a 0.75 mm track width, with
signal traces at 0.25 mm. Vias on power nets use a 0.4 mm drill with a 0.8 mm pad, while
signal net vias use a 0.3 mm drill with a 0.6 mm pad. Ground is implemented with a star
topology separating the buck converter's switching section from the quieter linear
regulator section.

## Current Status

Schematic, LTspice simulation, and PCB layout are complete. This board has not yet been
fabricated or bench tested against physical hardware. Real world verification, including
confirming rail voltages under load with a multimeter and oscilloscope, is planned as
the next step once the board is manufactured.

## Repository Scope

This repository documents the power supply board only. It does not contain the
platform's sensor interconnect board, motor drive electronics, or firmware, which are
maintained as separate repositories.
