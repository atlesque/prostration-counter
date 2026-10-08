# Backend, data model and idempotent synchronization

**Protocol proposal. No deployed service, runnable dashboard or schema migrations exist.**

Nuxt dashboard → HTTPS API on Cloudflare Workers → D1 database. ESP32-C3 sends durable session snapshots over Wi-Fi to the same API. BLE is for provisioning, not an always-connected cloud transport. Hosting the Nuxt app on Cloudflare requires an adapter/runtime compatibility check when implementation begins.

## Why snapshots

Repeated requests must not add counts twice. Send a session's absolute count and monotonically increasing revision, rather than “add 108” or an ever-growing per-press event queue. One dirty session consumes one storage slot regardless of how many presses occur. This preserves session totals, not per-prostration timestamps. Undo increments revision while reducing count; session reset closes the old session and creates a new session ID.

## Proposed data model

| Entity | Key fields and constraints |
| --- | --- |
| User | `id`, auth subject, IANA timezone, goal; account access required |
| Device | `id`, owner ID, token hash, revoked flag, hardware/firmware version, last sync |
| Session | Composite PK `(device_id, epoch, session_id)`; owner via device; ordinal, revision, count ≥0, closed flag, optional start/end UTC, date quality, assigned practice date, payload hash, server updated time |
| Pairing grant | Hashed short-lived single-use code, device ID, expiry, consumed owner |

Session IDs combine epoch with a durably allocated ordinal. A reboot retains epoch/ordinal; factory reset starts a new epoch and requires pairing. JSON wide counters/revisions/ordinals are decimal strings to avoid JavaScript precision loss; validate against a defined signed 64-bit maximum before conversion. Closed sessions are immutable after their final revision. Device writers are authoritative; browser count edits are out of MVP scope.

Lifetime total is the sum of latest counts for all an owner's sessions. Starting a new session does not subtract the old count. Undo affects only its active session and updates the total. Multi-device sessions add independently; moving a device to another owner requires an explicit history policy, not silent reassignment of old sessions.

## API surface

| Endpoint | Authentication / behavior |
| --- | --- |
| `POST /api/v1/pairing-grants` | Physically authorized device provisioning bootstrap, rate limited; creates short-lived grant |
| `POST /api/v1/devices/pair` | Logged-in owner + one-time grant; issues device-specific credential through verified provisioning channel |
| `POST /api/v1/devices/:deviceId/sync` | Device bearer credential, scoped to path device and owner; up to 32 snapshots / 16 KiB body |
| `GET /api/v1/sessions?cursor=...` | Logged-in owner; paginated session history |
| `GET /api/v1/summary?timezone=...` | Logged-in owner; daily/lifetime/goal totals and last sync |
| `GET /api/v1/export.csv` | Logged-in owner; private export |
| `PATCH /api/v1/settings` | Logged-in owner; goal/timezone |
| `PATCH /api/v1/sessions/:id/date` | Logged-in owner; date metadata only, no count/revision mutation |
| `DELETE /api/v1/devices/:deviceId/credential` | Logged-in owner; revokes future sync, retains history |

Use established session authentication for browser routes, CSRF protection for cookie-based mutations, HTTPS with verified server certificates for device routes, scoped revocable random device credentials, hashed tokens at rest, limits and payload validation. Pairing must require physical code confirmation and an authenticated owner; a self-asserted device ID is not an ownership proof. Final provisioning transport and bootstrap implementation require a security review before release. Do not expose credentials in logs, URLs or public practice data.

## Sync example (illustrative only)

```json
{
  "protocolVersion": 1,
  "epoch": "EXAMPLE-EPOCH",
  "sessions": [
    {
      "sessionId": "EXAMPLE-SESSION-17",
      "ordinal": "17",
      "revision": "110",
      "count": "107",
      "closed": false,
      "startedAt": null,
      "endedAt": null,
      "timeQuality": "unknown"
    }
  ]
}
```

Three inputs could be increment, increment and undo: revisions 108, 109, 110, with latest count 107. Arrival order must not determine the final count.

## Server transaction and acknowledgment

1. Authenticate and validate the entire batch, including ownership, bounds and duplicate keys within the batch. Reject conflicting duplicates in the request before writing.
2. Canonicalize device-owned payload fields and compute a payload hash. Owner-assigned date metadata is separate and excluded from this hash.
3. Atomically insert absent session rows or conditionally replace rows only when incoming revision is greater. Use a unique composite key and an SQL revision predicate, not a read-then-write decision in application memory. Prevent updates after a session is closed. Close/count/revision changes occur in the same row mutation.
4. An equal revision and equal hash is a successful replay. Equal revision with different payload is `409 revision_conflict`; a newer revision for a closed session is `409 closed_session`. Lower revisions are stale and cannot roll the row back. Read canonical rows after commit; if racing requests changed them, return that committed newer revision and hash.
5. Reply only for durable rows, with per-session `storedRevision`, `storedHash` and status (`stored`, `duplicate`, `stale`, `conflict`). Mixed valid batches may have per-record conflicts; no conflicting record is acknowledged as accepted. Database errors/timeouts are retriable and confer no acknowledgment.

Implement using D1's documented transactional batch behavior and guarded SQL, with readback consistency accounted for. Database constraints, mutation and any materialized totals must be one atomic operation. Prefer deriving totals from canonical session rows for MVP; avoid applying count deltas on every request. See [Cloudflare D1 database API](https://developers.cloudflare.com/d1/worker-api/d1-database/).

The device captures an immutable snapshot at revision R. It marks R clean only when the server hash/revision match that captured payload and persists the acknowledgment. If local presses produce R+1 during upload, R+1 remains dirty. A server-newer response with an unexpected hash is a reconciliation fault, not permission to discard local state. Cloud values never automatically replace the local count.

After a lost HTTP response, resend the same snapshot. After a crash before ack persistence, resend again. Duplicate submission, concurrent retries and older revision arrival must leave one latest row and one total. Reclaim only closed sessions whose final snapshot has a matching durable acknowledgment. Retain a bounded local history according to capacity and clearly show that the dashboard has the archived records.

## Time and offline limits

Use trusted network time when available plus monotonic offsets while running. Without a battery-backed RTC, reboot after offline power loss can leave the date unknown; never substitute upload time as practice time. Preserve `timeQuality` and offer manual date assignment. The MVP buckets an entire session by its start practice date; splitting sessions at midnight or per-press time history would require a new data contract.

Factory-reset epochs keep historical keys separate. Removed/expired device credentials require user intervention; retain local counts while showing an auth error. Storage exhaustion stops accepting new durable counts with a visible error until space is safely recovered or exported; “unlimited offline” is not a claim. Backend loss of previously acknowledged history requires a separate backup/recovery strategy before production release.
