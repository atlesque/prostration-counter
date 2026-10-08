# Phased prototyping and acceptance plan

**All phases and tests are planned, not completed.** The earlier two-to-three-weekend estimate is exploratory; procurement and mechanical iteration may take longer.

| Phase | Build / evidence | Exit gate |
| --- | --- | --- |
| 0 — Requirements and fit | Cardboard/printed height mockup; confirm reset semantics and one-handed reach | Comfortable placement during a 108-prostration practice; choose measurable targets |
| 1 — Button on USB | ESP32-C3, switches, printed floating plate; compare arcade reference | 1,000 cycles with zero misses/duplicates, including edge presses and holds |
| 2 — Offline instrument | Reflective display, FRAM, Undo/reset, storage fault handling | Power-cut recovery suite passes; gestures and display latency pass |
| 3 — Enclosure | PETG frame, soft surface, hard stops, serviceable bottom and feet | Force/noise/slip tests and fixture overload screen pass |
| 4 — Rechargeable power | Reviewed charger/load isolation, protected cell, wake and VCOM schedule | Charging/off tests, wake capture, mode-current measurements; endurance test started |
| 5 — Cloud and setup | Nuxt login/history, Worker API, D1 schema, Wi-Fi sync and opt-in BLE | Auth isolation and retry/reordering tests pass; offline flow unchanged |
| 6 — Practice validation | Multiple sessions over at least two weeks; inspect mechanism and compare independent tally | Accuracy/comfort evidence; all critical issues closed; measured battery result or explicitly unresolved target |

Start with parts on hand. Delay a full BOM order until the button demonstrates reliable edge activation. Keep each phase's hardware revision and measurements in a dated report, with photos and raw traces.

## Acceptance matrix

| Test | Procedure | Proposed pass criterion |
| --- | --- | --- |
| Center/edge counting | 1,000 fixture/hand cycles across center and eight equally spaced edge locations, 0.2–2 Hz | Exactly 1,000 durable increments, zero missing/extra counts |
| Hold and roll | Hold 10 s; roll between switch positions; introduce measured bounce traces | One count until all switches release stably; no retrigger |
| Real practice | Independently tally 108 prostrations, repeated on three sessions | Each display matches tally; no accidental reset or control activation |
| Force/travel | Gauge at nine points on assembled button | Initial target 2–8 N and 1–2 mm travel; no binding |
| Noise | 20 presses at 0.5 m, background below 30 dBA; record meter response | Initial peak target below 40 dBA and no distracting click reported |
| Readability/latency | Kneeling, 0.5–1 m, 100–500 lux; timestamp input/commit/display | Read six digits; active ≤150 ms and verified wake ≤250 ms |
| Undo/reset | Undo at zero; short tap; long hold; confirm; cancel by main/timeout; power cycle during transition | No negative totals; no decrement on long hold; new session zero, old history/lifetime preserved |
| Simultaneous controls | Main and Undo overlap; cancellation press during prompt | Documented precedence, exactly one intended action |
| Crash consistency | Automated power cuts around every write/marker/pointer/ack step, at least 100 trials per transition | All acknowledged counts survive; no torn valid rows or duplicate sessions; uncommitted input never falsely acknowledged |
| Boot/wake | Hold main during cold boot; short press from idle; long hold wake | Boot-held input waits for release; armed wake records exactly once |
| Offline history | Disable Wi-Fi; count/undo/close/reboot; approach and exceed slot capacity | Counts retained through supported capacity; storage-full error before overwrite or unpersisted increment |
| Compaction recovery | Reclaim acknowledged closed sessions; cut power around archived-subtotal checkpoint and slot reuse | Lifetime preserved exactly once; unacknowledged/open records never recycled |
| Corruption/full memory | Inject CRC failure, failed bus write, exhausted slots | Recover valid copy or visible fault; never silently clear count/history |
| Retry | Upload identical batch 10 times; lose response; reboot before ack persistence | One canonical row per session and unchanged totals |
| Reordering/concurrency | Deliver R+1 then R; overlap retries; count locally during sync | Newest revision retained; local newer snapshot remains dirty |
| Undo after sync | Sync count 108 at R; undo and send count 107 at R+1 | Cloud and local latest count 107; lifetime decreases once |
| Conflicts | Equal revision/different payload, new revision on closed row | Explicit conflict; no local discard or cloud overwrite |
| Identity | Cross-device token, cross-owner query, expired pairing code, revoked token | Requests rejected; another owner's history inaccessible; offline counting available |
| Time attribution | Offline reboot without trusted time; midnight session; timezone change | Unknown dates visible; documented start-date policy, no invented upload-date history |
| Floor stability | 108 angled presses on wood, tile and representative mat; measure movement | Initial target <5 mm net slip on each; no tipping or uncomfortable reach |
| Structural screen | Fixture-applied 300 N for 10 s, then inspect and retest | No cracks, permanent binding or damaged switches/cell; not a product load rating |
| Mechanical wear | 10,000-cycle screening, inspect guides/surface/fasteners | No accuracy failure; force/noise drift documented; longer lifecycle validation remains required |
| Power and charge | Measure all modes, radio peaks, low cell state and off with USB present | Stable supply, matched charging limits, no back-feed, off behavior matches wiring contract |
| Endurance | Defined product-spec profile at 20–25 °C, three charge/discharge trials | 30-day target evaluated with actual assembly; publish measured duration even if target fails |

## Recording results

Each report needs date, operator, hardware/print revision, cell part/capacity, firmware commit, SDK version, instruments, environment, exact procedure, raw evidence, pass/fail and limitations. Record failed Wi-Fi energy and display-maintenance current as well as successful operation. Manufacturer ratings and simulated results do not replace assembled-prototype measurements. Do not label the MVP validated until every critical gate has recorded evidence.
