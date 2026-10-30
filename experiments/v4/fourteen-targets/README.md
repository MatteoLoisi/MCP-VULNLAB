# Single campaign on the 14 targets

Request: run all probes on all 14 targets in one campaign, complete P5 on Filesystem, and explain why challenges c6 and c10 escaped.

## Design

Targets: four reference servers (`R1-filesystem`, `R2-memory`, `R3-sequential-thinking`, `R4-everything`) and the ten DVMCP challenges `c1`–`c10`. Probes P0–P15: 14 × 16 = 224 positions, of which **188 are applicable**; the others have a verifiable reason (absent surface or inapplicable transport). Three replicas of every applicable position, run on 29 October; the P7 judge is `llama3.2:3b`.

## Results

| Measure | Value |
| --- | --- |
| Executions | 564/564 completed, 0 errors |
| Agreement between replicas | 0 discordant positions (P7 included) |
| Outcomes per replica | 97 `info`, 72 `secure`, 8 `skipped`, **11 `vulnerable`** |
| Agreement with the 25 October replica | identical in all 188 positions |

The 11 `vulnerable` positions: P2 on c2, c5 and c10 (hostile instructions in tool descriptions); P5 on c3, c8 and c10 (canary read through a path argument); P11 on c7; P15 on c1, c4, c6 and c10 (listed resources delivering secrets).

P5 on Filesystem is complete: 17/17 tool–argument combinations on `@modelcontextprotocol/server-filesystem` 1.0.0, result `secure` (report in [../../../evidence/p5-filesystem-20261025/](../../../evidence/p5-filesystem-20261025/README.md)).

## Why c6 and c10 had escaped

The diagnosis refers to the earlier comparison (Exp3, 18 October), which ran only P2, P4 and P5. With the full battery of this campaign c6 has a P15 finding and c10 has findings from P2, P5 and P15.

- **c10** was not entirely missed: P2 detects the hidden instruction in the description of `malicious_check_system_status`. What escaped was the effect: P4 finds no marker because `run_system_diagnostic` maps its input to a fixed set of commands; the credible effect vector is `analyze_log_file(file_path)`, confirmed by P5. The hidden resource `internal://credentials` is not listed, so it is outside P15.
- **c6** escaped by coverage, not by defence: the keys sit in `internal_memo.txt`, readable through `read_document`, and in an unlisted resource. P15 reads only listed resources and P11 does not use the document name: reaching them requires the chain `documents://list` → `read_document("internal_memo.txt")`.

## Files

| File | Content |
| --- | --- |
| [results.csv](results.csv) | One row per target × probe × replica: status, evidence, duration, attempt identifier, report and evidence SHA-256 |
| [coverage.csv](coverage.csv) | One row per applicable position: replicas, completion, execution status and the statuses of the three replicas |

## Limitations

The probes are deterministic and P7 uses one judge model: replica stability does not estimate a model's variability. At the time of this campaign P7 chose the tools to judge in the order returned by `tools/list`; this was made deterministic afterwards, so a re-run can change P7 hit counts (not its `info` status).
