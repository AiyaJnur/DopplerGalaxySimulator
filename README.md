# Doppler Galaxy Simulator

An interactive educational simulation of Doppler shift and relative radial motion, developed in **Unreal Engine 5.8 using Blueprints**.

The project explores how mathematical and physical models can be transformed into an interactive simulation while keeping the underlying assumptions and limitations clear.

## Overview

The project contains two simulations.

### Simulation 1 — Doppler Shift

The user controls a galaxy's normalized radial-motion input from `-1` to `+1`, corresponding to velocities from approximately:

```text
-3000 km/s to +3000 km/s
```

The simulation calculates the Doppler shift using the low-speed approximation:

```text
z ≈ v_r / c
```

where:

- `v_r` is radial velocity;
- `c = 299792.458 km/s`.

Using the H-alpha spectral line as a reference:

```text
λ_rest ≈ 656.3 nm
```

the observed wavelength is calculated as:

```text
λ_observed = λ_rest × (1 + z)
```

Negative radial velocity represents motion toward the observer and produces blueshift, while positive radial velocity represents motion away from the observer and produces redshift.

---

### Simulation 2 — Relative Observation

The second simulation extends the model to four galaxies with individual position and velocity vectors.

The user can select any galaxy as the observer.

For every target galaxy, the simulation calculates:

```text
RelativeVelocity =
TargetVelocity - ObserverVelocity
```

and the viewing direction:

```text
LineOfSight =
Normalize(TargetPosition - ObserverPosition)
```

The radial component of relative motion is then found using the dot product:

```text
RelativeRadialVelocity =
Dot(RelativeVelocity, LineOfSight)
```

This extracts the component of relative velocity directed along the observer's line of sight.

The resulting radial velocity is passed through the same Doppler calculation used in Simulation 1.

Changing the observer therefore changes the calculated motion of the other galaxies relative to that reference frame.

---

## Key Features

- Two connected Doppler-shift simulations
- Relative-motion calculations using vectors and dot products
- H-alpha wavelength calculation
- Runtime observer switching
- Data-driven galaxy configuration
- Reusable Blueprint mathematical functions
- Niagara-based galaxy visualization
- UMG interface with educational explanations
- Event-driven simulation logic without continuous `Event Tick`

---

## Software Architecture

The project separates data, calculations, simulation state, visualization, and interface logic.

```text
Data
ST_DGS_GalaxyData
DT_DGS_Galaxies
        ↓
Mathematics
BFL_DGS_DopplerMath
        ↓
Simulation Logic
BP_DGS_Sim1Controller
BP_DGS_Sim2Controller
        ↓
Galaxy Presentation
BP_DGS_Galaxy
Niagara / Materials
        ↓
User Interface
WBP_DGS_Sim1
WBP_DGS_Sim2
WBP_DGS_LearnMore
```

`BFL_DGS_DopplerMath` contains reusable stateless functions for velocity conversion, redshift, wavelength calculation, shift classification, color mapping, and relative radial velocity.

Simulation 2 reuses the Doppler-processing logic developed for Simulation 1 rather than implementing a separate calculation pipeline.

Galaxy configuration is stored in a DataTable using:

```text
DisplayName
WorldPosition
VelocityKmS
```

The Simulation 2 controller reads these records, spawns the galaxies at runtime, initializes their data, and stores their references for later calculations.

---

## Visualization

Each galaxy is represented using a Niagara particle system.

The simulation uses:

- blue for blueshift;
- gray for approximately neutral radial motion;
- red for redshift;
- a gold ring to identify the currently selected observer.

These colors are **educational visual indicators** and should not be interpreted as the literal visible colors of real galaxies.

Observer selection is displayed separately from Doppler state so that physical information and interface state remain visually distinct.

---

## Technical Challenges

### Consistent Numerical Input

During testing, the displayed slider value could appear as `0.00` while the internal floating-point value was still slightly different from zero.

The input pipeline was therefore changed to:

```text
Raw Input
→ Clamp
→ Snap to Grid
→ Controller State
→ Physics Calculation
→ UI
```

This keeps the displayed value and the value used by the simulation consistent.

### Runtime Niagara Updates

Initially, galaxy color was applied mainly during particle initialization, so already existing particles did not always respond correctly to runtime changes.

The Niagara update logic was changed so that the Blueprint-controlled color parameter continuously updates the active particles.

### Observer and Physical State

A selected observer and a galaxy with near-zero radial motion could otherwise both appear neutral.

The observer was therefore given a separate gold selection ring instead of modifying its Doppler color.

---

## Scientific Scope

The project is intentionally a simplified educational model.

It models:

- relative radial motion;
- low-speed Doppler approximation;
- wavelength shift of a reference spectral line;
- observer-dependent vector projection.

It does **not** model:

- cosmological redshift caused by expansion of space;
- gravitational redshift;
- realistic galactic dynamics;
- astronomical distance scales;
- real observational galaxy data;
- relativistic Doppler equations.

The galaxy positions inside Unreal Engine are visualization coordinates rather than real astronomical distances.

---

## AI-Assisted Development

AI-assisted tools were used during software development as learning and technical support resources, particularly for Unreal Engine research, explanation of unfamiliar concepts, mathematical review, implementation guidance, and debugging.

I defined the project goals and constraints, built and integrated the system in Unreal Engine, tested its behavior, identified implementation problems, evaluated suggested solutions, and made the final decisions about the project's design and functionality.

---

## Technology

- Unreal Engine 5.8
- Blueprint Visual Scripting
- Niagara
- UMG
- DataTables
- Blueprint Structs and Enums
- Material Editor
- Git
- Git LFS

---

## How to Run

### Requirements

```text
Unreal Engine 5.8
```

1. Clone or download the repository.
2. Ensure Git LFS assets are downloaded.
3. Open:

```text
DopplerGalaxySimulator.uproject
```

4. Open or run:

```text
L_DGS_Sim1
```

5. Press Play.

Simulation 2 can be opened from Simulation 1 using the interface.

6. Enjoy the learning process!

---
