# MVP product specification

Revision 0.1 · 2026-10-08 · **Proposed; no acceptance tests have been executed.**

## Purpose and scope

Support repeated full-body Buddhist prostrations with a floor device the practitioner can locate and press without looking or using a second hand. The device counts deliberate button cycles, not body movement; it cannot verify that a prostration was completed. A phone and account are optional for offline practice and required only for cloud history.

The MVP includes a large quiet button, reflective counter display, undo, guarded session reset, physical power control, rechargeable battery, offline storage, Wi-Fi synchronization, deliberate BLE provisioning, and a private web dashboard. BLE provisioning follows the offline hardware milestone but remains part of the complete MVP. Social features, audio cues, automatic movement detection, OTA updates, commercial manufacture and native phone apps are later work.

## Functional contract

| ID | Proposed requirement |
| --- | --- |
| P01 | Main surface 100–110 mm diameter; prototype body about 160 × 160 mm and 25–30 mm high. Rounded edges, replaceable soft palm surface and slip-resistant base. |
| P02 | Activation at center and edge with one hand; initial force target 2–8 N across test points, 1–2 mm travel. Final values follow user testing. |
| P03 | One increment for each debounced press followed by full release. Long holds and rolling across multiple switches produce one increment. |
| P04 | Display current session count up to at least 999,999, battery state and unsynced/error state. No wraparound; show overflow indication if display capacity is exceeded. Internal totals use checked wide integers. |
| P05 | Short Undo decrements current session by one, minimum zero. Lifetime total falls by the same amount. Undo at zero is a no-op; it cannot edit a closed session. |
| P06 | Hold Undo for 5 s to prompt “New session?”. Release, then short-press Undo within 5 s to confirm; main press or timeout cancels. The long hold must not also decrement. Confirmation archives the old session and creates a zero-count session. History and lifetime total remain intact. |
| P07 | Power off preserves committed state. Charging with power off is desired; switching must isolate the system load from both battery and USB while retaining the charger path, subject to schematic review. |
| P08 | Display changes within 150 ms of a valid active-mode press, after durable commit. Wake-and-count target 250 ms; short wake presses must be captured by a latch or a verified wake design. |
| P09 | A successfully acknowledged count survives unexpected power loss. An interrupted, not-yet-committed press may not count; no increment is displayed as successful before its durable record validates. |
| P10 | Wi-Fi failure, login failure or cloud outage never disables counting. Unsent session state persists; a full history store produces an explicit “Storage full” error rather than silently dropping data. |
| P11 | BLE provisioning is opt-in, times out after 2 minutes, and uses a device-displayed pairing code. Wi-Fi credentials never appear in dashboard logs or repository files. USB setup is available for bench prototypes. |
| P12 | Wi-Fi sync occurs on session close and during bounded idle opportunities; radio stays off during normal practice. A manual sync is available through the dashboard/provisioning UI. |

## Comfort, sound and robustness

No buzzer or illuminated arcade ring in the meditation build. Initial quietness target is an actuation peak below 40 dBA at 0.5 m in a room with background below 30 dBA; record meter response and ambient noise. Practitioner feedback overrides a nominal sound target if the mechanism is distracting. Reflective digits must be readable while kneeling at 0.5–1 m under 100–500 lux. The product is intended for dry indoor use; no water-resistance rating is established.

Hard stops route palm force into the frame rather than switch bodies, display or battery. A 300 N fixture test is an initial engineering screen, not a certification or authorization to stand on the device. Determine realistic accidental loads and safety factors before human loading tests.

## Power target

Candidate battery: protected single-cell Li-ion/LiPo, nominal 3.7 V, 1,000–2,000 mAh; USB-C recharge with matched charging circuit. Initial endurance target: 30 days under 30 minutes of practice and 300 presses daily, one successful sync of at most 30 s daily, no routine BLE use, and idle for the remaining time at 20–25 °C. Measure unsuccessful Wi-Fi attempts separately. Earlier “4–8 weeks” artwork/conversation estimates are aspirations and are superseded by this defined test profile. No endurance result exists.

## Dashboard contract

Private login, device pairing/revocation, current session as last synced, today's total, daily history, lifetime total, configurable goal such as 100,000 and CSV export. Show device last-sync time, pending/stale status and uncertain session dates. Multi-device totals sum independent sessions; the cloud never overwrites a physical device's local count. No public practice history by default. Web editing of counts is deferred to avoid multiple writers.

“Today” uses the account's selected IANA timezone, not the server timezone. Sessions that cross midnight are assigned to their start date for MVP; unknown offline dates remain in an undated bucket until the user assigns a date. This limitation must be visible.

## Release gate

Release requires all core acceptance tests in [the plan](prototyping-plan.md), reviewed power wiring, measured button force and sound, documented storage capacity, and private-account sync verification. Unmeasured targets remain explicitly unverified.
