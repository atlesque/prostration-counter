# Mechanical design alternatives

**Proposed mechanisms; no printable CAD or tested load rating yet.**

| Option | Benefits | Risks / validation | Role |
| --- | --- | --- | --- |
| Floating plate on 3–4 switches | Simple digital input, short travel, tactile confirmation | Edge rocking, guides binding, combined spring force, click noise | Preferred first custom build |
| 100 mm arcade button | Ready-made electrical reference, fast bring-up | Height, audible click, high force, lighting drain | Bench reference only |
| Silicone dome / membrane | Soft feel, potentially quiet and shallow | Custom tooling, force curve variation, aging and sealing | Compare after first plate |
| FSR pressure surface | Little motion, thin construction | Creep, temperature drift, threshold tuning, static hand false positives | Experimental alternative |
| Load cell and amplifier | Quantifiable force and hysteresis | Height, calibration, overload protection, amplifier power | Experimental alternative |

## First printed iteration

Use the Bambu Lab A1 for a rigid 110 mm plate, 160 mm base, guides and replaceable switch mounts. PLA is suitable for rapid fit trials; evaluate PETG for the structural enclosure. Add a thin replaceable silicone/TPU palm covering and rubber contact pads. Avoid tiny textures that abrade skin and pinch gaps around the moving plate.

Separate the plate's structural support from the switches. Hard stops engage before switch overtravel, with loads routed through perimeter supports into the base. Keep the battery and display out of the force path. Guides need clearance for print variation while limiting lateral motion; start with tunable shims rather than assuming a single tolerance. Spring force and friction must be measured on the assembled mechanism.

Offset the plate slightly if needed for an angled display readable while kneeling. Recess Undo and shield the power slider from palm contact. Provide a screw-removable bottom cover, strain relief, antenna clearance away from metal/battery, and cell clearance for dimensional variation. The 25–30 mm height is a target to validate with the actual battery and display stack.

## Selection experiment

For each mechanism record center and eight edge force/displacement curves, activation noise, 1,000 press accuracy, rolling-hand behavior, release recovery, floor slip on wood/tile/mat and practitioner comfort after 108 prostrations. Pressure sensors need separate press/release thresholds, dwell filtering, slow baseline correction only while unloaded, and tests with long static loads. Choose using these measurements rather than artwork appearance.

Conduct structural tests with a fixture before asking anyone to transfer body weight to a prototype. Increase loads in controlled steps and document permanent deflection, cracks, fastener movement and switch function. Commercial enclosure materials such as ABS/PC remain later manufacturing alternatives, not validated selections.
