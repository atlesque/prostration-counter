# Contributing

This project begins as a specification. Open an issue with a concrete requirement, design tradeoff, measurement or proposed experiment. Label work as proposed, implemented, bench-tested or practice-validated and include evidence for any claimed result.

First implementation priorities are the button fixture, offline count/Undo state machine, atomic storage recovery and display integration. Keep counting independent of network operations. Review identity/retry behavior before adding cloud history.

Use conventional branches such as `feature/offline-counter`, `bugfix/release-debounce` or `chore/documentation`; never use the `codex/` prefix. Keep changes focused, record relevant verification, preserve generated-image provenance, and do not commit credentials, Wi-Fi passwords, private practice records or vendor files without appropriate rights.

Contributions are offered under the MIT License. Hardware files should include editable source and exports, dimensions, revision and assembly guidance. Dependency licenses must be retained. Record actual tests rather than treating the acceptance plan as a passed test report.
