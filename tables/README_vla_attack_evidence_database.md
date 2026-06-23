# VLA Attack Evidence Database Supplement

Version: `VLA-Attack-ER-v2026-06-16`

Last search date: 2026-06-16

This supplement supports the manuscript "Attacks on Vision-Language-Action Robotic Systems: Mechanisms, Evidence, and Evaluation." It provides a fixed evidence appendix used for descriptive counts in the paper.

## Files

- `vla_attack_evidence_database_v2026-06-16.csv`: complete evidence appendix with 85 entries.
- `direct_vla_coding_audit_v2026-06-16.csv`: independent second audit pass for the 23 direct VLA records.
- `direct_vla_validation_flags_v2026-06-16.csv`: companion file separating mutually exclusive highest validation tier from multi-label validation flags for direct VLA records.
- `vla_attack_corpus_source_v2026-06-16.csv`: companion file mapping each record to `core` or `contextual` corpus and recording discovery source.

## Database Counts

The fixed evidence appendix contains 85 entries:

- `direct`: 23
- `embodied-adjacent`: 28
- `analogical`: 34

The corpus split used for attack-evidence statistics is:

- `core`: 30 records (`23 direct` + `7 embodied-adjacent attack studies`)
- `contextual`: 55 records

Use the following PowerShell commands from the repository root to reproduce the counts:

```powershell
$rows = Import-Csv "tables/vla_attack_evidence_database_v2026-06-16.csv"
$rows.Count
$rows | Group-Object evidence_stratum | ForEach-Object { "$($_.Name)=$($_.Count)" }
$src = Import-Csv "tables/vla_attack_corpus_source_v2026-06-16.csv"
$src | Group-Object corpus_type | ForEach-Object { "$($_.Name)=$($_.Count)" }
```

## Direct Evidence Decision Rule

A record is `direct` only if both conditions hold:

1. The target is a named VLA model, reasoning-enabled VLA, VLA-controlled robot, VLA training channel, or VLA privacy channel.
2. The measured outcome concerns VLA actions, trajectories, action representations, reasoning-to-action behavior, triggered VLA behavior, or VLA training-data membership.

If the target is an embodied LLM/VLM planner, robot middleware, sensor path, HRI channel, or robot system without VLA policy measurement, the record is `embodied-adjacent`. If the work provides only an attack mechanism, benchmark substrate, or representation baseline without embodied VLA validation, the record is `analogical`.

## Field Dictionary

- `db_version`: fixed appendix version.
- `record_id`: stable record identifier. Direct VLA records use `Dxx`; adjacent records use `Axx`; analogical/methodological records use `Gxx`.
- `work_key`: BibTeX key used in the manuscript.
- `work_short`: short human-readable name.
- `year`: publication or preprint year.
- `evidence_stratum`: `direct`, `embodied-adjacent`, or `analogical`.
- `family`: attack or evidence family used for synthesis.
- `status`: peer-reviewed, preprint, workshop, benchmark, or substrate status.
- `lifecycle`: training, inference, runtime, post-deployment, or combinations.
- `knowledge_level`: what the attacker knows: white-box, gray-box, black-box, transfer-only, mixed, or not applicable.
- `access_channel`: how the adversarial intervention is delivered: digital, physical, human-mediated, supply-chain, hybrid, etc.
- `privilege_level`: role or privilege boundary: user, bystander, operator, data contributor, model provider, tool/API, middleware, model query, or no privilege.
- `entry_surface`: semantic/system surface carrying the intervention.
- `target_representation`: representation or decision changed by the attack.
- `objective`: security objective affected: availability, integrity, confidentiality, authorization, safety, or combinations.
- `temporal_profile`: one-shot, repeated, delayed, persistent, or combinations.
- `highest_validation_tier`: mutually exclusive strongest tier: digital-input-only, real-sensor-input, simulation-rollout, hardware-in-the-loop, real-robot-actuation, or human-proximate.
- `target_model_or_system`: victim model/system.
- `benchmark_or_dataset`: benchmark, dataset, or experimental context.
- `real_robot_actuation`: whether physical robot actuation is reported (`yes`, `no`, `NR`, or special note).
- `human_proximate`: whether human-proximate evaluation is reported.
- `transfer_reported`: transfer evidence. `yes` indicates explicit transfer; `limited` indicates narrow or partial transfer; `NA/NR` are used when not applicable or not reported.
- `benign_utility_reported`: whether benign or clean-task utility is reported.
- `query_budget_reported`: whether query/edit/rollout budget is reported.
- `action_representation`: action representation under attack or evaluation.
- `chunk_horizon_reported`: whether action chunk horizon or equivalent is reported.
- `controller_frequency_reported`: whether controller/control frequency is reported.
- `safety_filter_reported`: whether safety filter/shield/CBF/monitor is reported.
- `recovery_policy_reported`: whether recovery policy or recovery outcome is reported.
- `pre_post_controller_trace`: whether both pre-controller and post-controller traces are reported.
- `asr_or_metric`: attack success metric or privacy metric, as reported.
- `main_limitation`: concise evidence limitation used in synthesis.

The companion corpus/source file defines:

- `corpus_type`: `core` or `contextual`.
- `discovery_source`: `database query`, `backward snowballing`, `forward snowballing`, `targeted verification`, `reviewer suggestion`, or `project-page discovery`.

## Validation Tier and Flags

`highest_validation_tier` is mutually exclusive. Multi-label validation flags are stored separately in `direct_vla_validation_flags_v2026-06-16.csv` for the direct VLA subset.

Example:

- A paper with simulation and physical robot trials has `highest_validation_tier=real-robot-actuation`.
- Its `validation_flags` may include `simulation_rollout|real_sensor_input|real_robot_actuation`.

## Audit Procedure

The 23 direct VLA records received an independent second coding/audit pass for principal fields:

- evidence stratum
- family
- lifecycle
- knowledge level
- access channel
- privilege level
- highest validation tier
- target representation
- objective
- action representation
- real-robot actuation
- transfer reporting
- query-budget reporting
- recovery reporting

The audit file records per-row agreement and reconciliation notes. The audit identified two conservative corrections to `real_robot_actuation`: `RoboGCG` and `VLALeaks` were changed to `no`. `FreezeVLA` was kept as `NR` for real-robot actuation.

## Reproducing Main Statistics

Direct VLA counts:

```powershell
$rows = Import-Csv "tables/vla_attack_evidence_database_v2026-06-16.csv"
$d = $rows | Where-Object evidence_stratum -eq "direct"
$d.Count
$d | Group-Object highest_validation_tier | Sort-Object Name
$d | Group-Object knowledge_level | Sort-Object Name
$d | Group-Object access_channel | Sort-Object Name
$d | Group-Object real_robot_actuation | Sort-Object Name
$d | Group-Object query_budget_reported | Sort-Object Name
$d | Group-Object recovery_policy_reported | Sort-Object Name
```

Model/benchmark concentration:

```powershell
($d | Where-Object { $_.target_model_or_system -match "OpenVLA" }).Count
($d | Where-Object { $_.benchmark_or_dataset -match "LIBERO" }).Count
($d | Where-Object { $_.target_model_or_system -match "pi0" }).Count
```

Robot-control reporting:

```powershell
($d | Where-Object { $_.chunk_horizon_reported -notin @("NR","NA","no") }).Count
($d | Where-Object { $_.controller_frequency_reported -notin @("NR","NA","no") }).Count
($d | Where-Object { $_.safety_filter_reported -notin @("NR","NA","no") }).Count
($d | Where-Object { $_.recovery_policy_reported -notin @("NR","NA","no") }).Count
($d | Where-Object { $_.pre_post_controller_trace -notin @("NR","NA","no") }).Count
```

## Version and Duplicate Handling

If a paper has both an arXiv and peer-reviewed venue version, the venue version is used for bibliography status when identifiable; the arXiv ID or project page may still be used for artifact or version notes. If a project page, repository, OpenReview entry, and arXiv page refer to the same work, they are merged into one record. Renamed preprints are merged when title, author set, arXiv ID, or project repository establishes identity.

## Fields Used for Main-Text Statistics

The main text uses:

- `evidence_stratum` and `corpus_type` for corpus counts.
- `highest_validation_tier` and `direct_vla_validation_flags_v2026-06-16.csv` for validation statistics.
- `knowledge_level`, `access_channel`, and `privilege_level` for threat-model statistics.
- `target_model_or_system` and `benchmark_or_dataset` for model/benchmark concentration.
- `transfer_reported`, `benign_utility_reported`, `query_budget_reported`, `chunk_horizon_reported`, `controller_frequency_reported`, `safety_filter_reported`, `recovery_policy_reported`, and `pre_post_controller_trace` for reporting completeness.

ASR values are not pooled because `asr_or_metric` contains heterogeneous objectives and denominators.
