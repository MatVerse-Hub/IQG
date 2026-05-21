# API v1 Contract

Status: `HOLD_BACKEND_BRIDGE_REQUIRED`

This contract defines the minimum causal backend for MatVerse/IQG.

## Principle

```text
request -> MNB -> Omega Gate -> Ledger -> Receipt -> Replay -> Evidence Pack
```

## P0 endpoints

### POST /v1/mnb

Creates an MNB from a canonical raw event.

Minimum input:

```json
{
  "event_type": "claim|artifact|decision|work|observation",
  "payload": {},
  "source": "string",
  "policy": "string",
  "evidence_class": "UNVERIFIED|OBSERVED_TEXT|FILE_READ|COMPUTED|SIGNED_RECEIPT|EXTERNAL_VERIFIED",
  "trace_id": "string"
}
```

Minimum output:

```json
{
  "mnb_id": "string",
  "payload_hash": "sha256",
  "core_hash": "sha256",
  "status": "CREATED|HOLD|BLOCK"
}
```

### POST /v1/omega/evaluate

Evaluates admissibility.

Minimum input:

```json
{
  "mnb_id": "string",
  "psi": 0.0,
  "theta": 0.0,
  "cvar": 0.0,
  "pole": 0.0,
  "replay_required": true
}
```

Minimum output:

```json
{
  "decision": "PASS|HOLD|BLOCK|ESCALATE|QUARANTINE",
  "omega": 0.0,
  "reason": [],
  "receipt_hash": "sha256"
}
```

### POST /v1/ledger/append

Appends a decided MNB to the ledger.

Minimum output:

```json
{
  "ledger_position": 0,
  "state_hash": "sha256",
  "previous_hash": "sha256"
}
```

### POST /v1/replay/verify

Verifies deterministic continuity.

Minimum output:

```json
{
  "replay_valid": true,
  "expected_hash": "sha256",
  "observed_hash": "sha256",
  "divergence": null
}
```

### POST /v1/evidence-pack

Exports an evidence pack.

Minimum output:

```json
{
  "pack_id": "string",
  "manifest_hash": "sha256",
  "files": [],
  "decision": "PASS_LOCAL|HOLD_EXTERNAL_REQUIRED|BLOCK"
}
```

## Fail-closed rules

```text
missing hash -> BLOCK
missing policy -> BLOCK
missing evidence_class -> HOLD/BLOCK
missing required replay -> BLOCK
metric outside [0,1] -> BLOCK
NaN/Inf -> BLOCK
```
