---
layout: page
title: "Solid Rocket Motor Design"
description: "Design study of a 1447 N-s solid motor - nozzle, chamber and bulkhead - for a 1 km sounding rocket"
img: assets/img/motor/01_motor_half_section.png
importance: 3
category: rocketry
---

Project : *Build Your Own Rocket*, Helios rocketry team
My role : Propulsion - solid motor design (nozzle, combustion chamber, bulkhead), Autodesk Inventor
Partner : airframe, recovery and vehicle integration

This was the first project I led inside the club. Two of us split a whole vehicle between us: I designed the motor, my partner designed the airframe and recovery system, and we met in the middle at the interface.

**Scope.** The project ended at design review. Nothing here was manufactured or fired — every number below is a designed or simulated value, and the drawings stop at CAD.


## Requirements

The vehicle-level goal was deliberately constrained — reach the target with the smallest airframe that will do it, rather than with the biggest motor we could build.

| | Requirement |
| --- | --- |
| Target apogee | 1 km |
| Maximum speed | Mach 0.5 |
| Propellant mass | ≤ 1.5 kg |
| Recovery | reliable enough for a safe recovery, single deployment |

From these I derived the motor-level requirements myself:

| ID | Item | Constraint |
| --- | --- | --- |
| SR-1 | Chamber pressure | ≤ 5.0 MPa |
| SR-2 | Propellant load | fixed grain mass and segment count |

Chamber pressure is the requirement that drives everything else in the motor. It sets the wall thickness, the O-ring configuration, the bolt count at both closures, and — through the throat area — the thrust curve. Fixing it first meant every later dimension had something to be derived from.


## Motor

Sized in an SRM spreadsheet, then modelled in Inventor as three parts: nozzle, combustion chamber, bulkhead.

| Parameter | Value |
| --- | --- |
| Total impulse | 1447 N·s |
| Peak thrust | 625 N |
| Burn time | 2.667 s |
| Propellant mass | 1197 g |
| Chamber pressure | 5.00 MPa |
| Casing | Al 6061-T6 |
| Nozzle / bulkhead | SUS 304 |
| O-rings | AN-229 |

Average thrust is 543 N, so the peak-to-average ratio is 1.15 — close to neutral, which is what a constant-pressure design should give.

![](/assets/img/motor/01_motor_half_section.png)


### Nozzle

| Parameter | Value |
| --- | --- |
| Outer diameter | 68 mm |
| Length | 88.47 mm |
| Throat diameter | 11.13 mm |
| Exit diameter | 31.47 mm |
| Expansion ratio | 8 |
| Convergent half-angle | 35° |
| Divergent half-angle | 12° |
| O-ring groove | AN-229 × 2, Φ48.2 |

SUS 304 for strength and corrosion resistance, because the nozzle is meant to survive repeated firings rather than be consumed. Two O-ring locations including a backup ring, and twelve M6 carbon steel bolts at the closure.

The geometry is self-consistent: (31.47 / 11.13)² = 7.99, which is the specified expansion ratio of 8. Throat area is 97.3 mm², exit area 778 mm².

![](/assets/img/motor/02_nozzle.png)

![](/assets/img/motor/03_nozzle_alt.png)


### Combustion chamber

| Parameter | Value |
| --- | --- |
| Outer diameter | 74 mm |
| Wall thickness | 3 mm |
| Length | 270 mm |

Wall thickness came from hoop stress, then got checked against two things that were not negotiable — the sizes commercial aluminium pipe actually comes in, and the 68 mm inner diameter of the 3 mm phenolic liner that had to fit inside.

At 5 MPa with a 68 mm bore and a 3 mm wall:

$$
\sigma_\theta = \frac{p\,d_i}{2t} = \frac{5 \times 68}{2 \times 3} \approx 57\ \text{MPa}
$$

Against the ~276 MPa yield of 6061-T6 that is a safety factor of roughly 4.9 — comfortable, and largely set by the pipe stock rather than by the calculation.

![](/assets/img/motor/05_chamber.png)

![](/assets/img/motor/04_chamber_detail.png)


### Bulkhead

| Parameter | Value |
| --- | --- |
| Outer diameter | 67.5 mm |
| Length | 55.05 mm |
| Instrumentation | NPT 1/4″ × 2 |
| O-ring groove | AN-229 × 2, Φ61.8 |

SUS 304 again, and two NPT 1/4″ ports intended to carry pressure and temperature fittings for a static fire. Putting the instrumentation ports into the closure at design time, rather than leaving them to be added once the motor existed, is the decision on this part I would repeat.

![](/assets/img/motor/06_bulkhead.png)

![](/assets/img/motor/07_bulkhead_view.png)


## Design iteration

The motor was not right the first time. The version that went into the vehicle is noticeably larger than the one the spreadsheet first produced.

| | First sizing | Vehicle build |
| --- | --- | --- |
| Total impulse | 1175 N·s | 1447 N·s |
| Propellant mass | 972 g | 1197 g |
| Burn time | 2.039 s | 2.667 s |
| Peak thrust | 679 N | 625 N |

Impulse rose 23% while peak thrust *fell* — a longer, flatter burn rather than a harder one. Both versions sit at a delivered specific impulse of about 123 s in the spreadsheet, so the change is grain geometry and loading rather than propellant chemistry.


## Vehicle-level result

My partner's airframe around my motor, as simulated:

| Parameter | Value |
| --- | --- |
| Overall length | 1270 mm |
| Liftoff mass | 7.135 kg |
| Burnout mass | 5.938 kg |
| CG / CP from nose | 939 mm / 1090 mm |
| Static stability | 2.01 cal |
| Predicted apogee | 1356 m |
| Maximum speed | Mach 0.524 |
| Maximum acceleration | 80.8 m/s² |

![](/assets/img/motor/08_rocket_profile.png)

The stability figure checks out: (1090 − 939) / 74 = 2.04 cal, against a body diameter of 74 mm.

The prediction sits above the stated numbers — 1356 m against a 1 km target, Mach 0.524 against Mach 0.5. That was accepted rather than corrected: the club treated these figures as a direction to design toward, not a specification to hit, and we were told the tolerance did not need to be tight. Sizing the motor down to land exactly on 1 km would have meant re-cutting the grain geometry for no gain the project cared about.


## Airframe and recovery (partner's work, for context)

Haack series nose cone, 74 mm diameter and 296 mm long, chosen to reduce drag. Recovery is a CO₂ pneumatic ejection system, single deployment, adapted from an existing linker-to-barrel assembly and scaled down. Parachute sizing set a target descent rate of 8 m/s, which gives a 1.28 m minimum diameter from a drag-equals-weight balance; a 1.5 safety factor took the final canopy to 1.92 m.

![](/assets/img/motor/09_rocket_model.png)

![](/assets/img/motor/10_rocket_model2.png)

![](/assets/img/motor/11_assembly.png)


## What I would do differently

**Keep one version of the requirements.** The requirement table went through more than one revision — the propellant load moved between a multi-segment and a single-segment grain — and the revisions were written under the same requirement ID rather than as a tracked change. Anyone reading the document afterwards cannot tell which version the hardware was built to.

**Justify the margin, not just the number.** The chamber safety factor of 4.9 came from the pipe stock that was available, not from a target I chose. That is a reasonable way to arrive at a wall thickness, but the document should say so — otherwise a reader assumes 4.9 was deliberate.

**Verify the closures, not only the vessel.** Hoop stress on the chamber was calculated. The twelve M6 bolts at the nozzle and the shear path at the bulkhead were sized from convention and reference designs rather than from a written calculation. The closures are where a motor actually fails.


## What this project gave me

It was the first time I owned a component from requirement to CAD, and the first time I felt how a single number propagates: fixing chamber pressure at 5 MPa fixed the wall thickness, the O-ring sizes, the bolt count, the throat area and, through it, the thrust curve and the flight profile.

It also left me with a number I could not check. The spreadsheet assumes a specific impulse of roughly 123 s, and every figure above rests on that constant — impulse, burn time, apogee, the lot. Nothing in a design process tells you whether the propellant you eventually cast will deliver it; only a static fire does. Stopping at design review meant I finished the project holding a stack of self-consistent numbers with no measurement anywhere underneath them.

That is what I wanted next, and it is why the work I looked for afterwards was the kind where something actually gets fired and recorded.

