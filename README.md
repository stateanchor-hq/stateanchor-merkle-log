# StateAnchor Public Merkle Log

This repository is the public append-only audit log for [StateAnchor](https://stateanchor.dev) gate decisions.

## What this is

Every gate decision StateAnchor makes is recorded as a leaf in a Merkle tree following [RFC 6962](https://datatracker.ietf.org/doc/html/rfc6962) (Certificate Transparency). The root hash of the current tree is published here on every update. This means:

- Gate decisions cannot be retroactively altered without changing the root hash
- Anyone can verify that a specific sync run's verdict was included in the log
- The log is append-only: entries are never deleted or modified

## Repository structure

```
roots/
  latest.json    — most recent Merkle tree root
```

### `roots/latest.json` format

```json
{
  "tree_size": 6,
  "root_hash": "cc2ef84a6044e18676ff67ec7bb2f8bc1d3c11730ecd317c6cfe90b0af7d0839",
  "computed_at": "2026-04-25T00:03:17.248Z",
  "verify_cli": "npx stateanchor verify-gate-decision <sync_run_id>"
}
```

| Field | Description |
|---|---|
| `tree_size` | Number of leaves (gate decisions) in the current tree |
| `root_hash` | SHA-256 Merkle root of all recorded gate verdicts |
| `computed_at` | UTC timestamp of the last root publication |
| `verify_cli` | Command to verify a specific sync run's inclusion |

## Verifying a gate decision

If you have a `sync_run_id` from a StateAnchor gate verdict, verify its inclusion:

```bash
npx stateanchor verify-gate-decision <sync_run_id>
```

This fetches the inclusion proof from StateAnchor's API and verifies it against the root hash published in this repository. A successful verification confirms the verdict was recorded before the root was published and has not been altered.

## Root publication

The root is updated automatically as new gate decisions are recorded. The `computed_at` timestamp reflects the last publication.

## Why this matters

StateAnchor's trust model for compliance teams is not just that it enforces API contracts — it is that enforcement decisions are tamper-evident and independently verifiable.

- **SOC 2 auditors** can verify gate decisions without relying on StateAnchor's own logs
- **API partners** can confirm their changes were evaluated at a specific point in time
- **Retroactive manipulation** of gate verdicts produces a detectable root hash mismatch

## Further reading

- [stateanchor.dev/trust](https://stateanchor.dev/trust) — full data handling policy and sub-processor list
- [stateanchor.dev/docs/reliability](https://stateanchor.dev/docs/reliability) — SLO and outage policy
- Questions: [hello@stateanchor.dev](mailto:hello@stateanchor.dev)
