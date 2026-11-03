# L-015 — The admit record cannot carry the `memory_id` the design says it audits

**Gap.** `docs/LINTEL_IR.md:79` and `docs/LATER.md:19` put `memory_id` (the session / retrieval snapshot the procedure read) *on the admit record, for audit, outside `%k`*. `schemas/admit-record.v0.schema.json` `pins` (lines 45–56) is `additionalProperties: false` with exactly `hw_id, compiler_ver, adapter_id, model_id, policy_id`, so a schema-valid record cannot name the memory snapshot at all. `memory_id` appears only in `examples/later/controller-dev.sketch.lintel`. Consequence: the replay law's clause "a new `memory_id` under the same `policy_id` … does not authorize a second digest" (`LINTEL_IR.md:84`) cannot be checked from records, and a retrieved claim used by a proposal has no recorded source. Found while auditing the Auto Ontology intake (STATUS.md, 2026-11-03).

**Carrier.** Two admit records with identical `pins` and `%k`, differing only in `memory_id` (`snap.2026-11-01` → `snap.2026-11-03`): the first lands digest D; the second must either replay D or hard-fail, never land D′.

**Change.** `admit-record.v1`: optional `pins.memory_id` (string), documented as audit-only; `cache-key` unchanged (no new `%k` axis). Whether the record also lists the retrieved claims (with applicability scope, measurement label from L-008, parent from L-002) is a separate, later gap; this one is only the identity.

**Falsifier.** A carrier pair where recording `memory_id` changes nothing a reviewer or the replay check can decide (the second record is already rejected by `%k` + digest alone, and no audit question needs the snapshot), in which case the field is dead weight. Survey item: T3 (freeze + replay), P2 (memory); survey §4.6, §5.1.5.

**Loop.** application (record shape; memory is not the controller loop)

**Depends on.** none (related: L-011 `kind: memory`)

**Status.** open
