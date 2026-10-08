# Sources, decisions and open questions

The design originated in the “Design prostration counter” conversation on 2026-10-08. This repository consolidates its concepts into an explicit engineering proposal; no private conversation transcript is published. The original generated board is retained, while unsupported endurance estimates are treated as targets.

## Primary references

Checked for design context on 2026-10-08; implementation must recheck the exact selected hardware and pinned software versions.

- [Seeed XIAO ESP32C3 guide](https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/): candidate board, interfaces and battery considerations.
- [Adafruit Sharp Memory Display guide](https://learn.adafruit.com/adafruit-sharp-memory-display-breakout?view=all): reflective display integration; exact panel datasheet governs VCOM timing.
- [ESP-IDF ESP32-C3 sleep documentation](https://docs.espressif.com/projects/esp-idf/en/stable/esp32c3/api-reference/system/sleep_modes.html): sleep modes and wake constraints.
- [Cloudflare D1 database API](https://developers.cloudflare.com/d1/worker-api/d1-database/): database access and batch transaction behavior.

Prices in the BOM are explicit budgeting allowances, not sourced vendor quotes or purchase recommendations. Nuxt/Cloudflare is the requested stack; compatibility and deployment setup remain implementation work.

## Current design decisions

1. Start with a floating digital-switch plate; investigate pressure sensing only through comparison tests.
2. Count locally and commit before showing success; radio availability cannot gate normal practice.
3. Session reset closes history rather than erasing it; Undo edits only the active session.
4. Use revisioned absolute session snapshots for idempotent sync and bounded storage.
5. Keep dashboard count editing out of the MVP to preserve a single writer per session.
6. Treat battery runtime, silence, dimensions and load tolerance as targets until measured.

## Decisions requiring prototype evidence

- Switch force/geometry, spring preload, guide clearance, surface material and floor placement.
- Actual display part/resolution, inversion timing and sleep-compatible VCOM circuit.
- Wake pin allocation and whether a latch is needed for short presses.
- Charger load sharing, USB-off isolation, cell protection and charge-current compatibility.
- Whether 112 proposed FRAM session slots meet retreat/offline needs; larger storage may be necessary.
- Reliable timestamp recovery: retain unknown dates in MVP or add an RTC and its power/BOM cost.
- Auth provider, pairing/bootstrap transport and production credential protection.
- Nuxt hosting adapter, supported build versions, D1 consistency/readback and backup policy.

## Scope of this initial publication

Implemented: repository and written design artifacts only. Included: original generated image. Not implemented: CAD, PCB, firmware, API, database, dashboard, BLE setup or test rigs. No battery, noise, structural, accuracy or security tests have run.
