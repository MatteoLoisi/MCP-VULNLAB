# Experiments v4: data added after the 23 October package

This folder holds the data of the executions run with laboratory version 4, between 25 and 30 October 2026, which are not part of the 23 October experiment package in the parent folder. Each subfolder has one CSV row per run or per probe position, summary tables, and a report.

| Folder | Content | Unit | Rows |
| --- | --- | --- | ---: |
| [client-defenses/](client-defenses/README.md) | Client-side controls tested one at a time on the JSON variant of the hostile tool result | agentic run | 320 |
| [external-host/](external-host/README.md) | The same scenarios on the laboratory harness and on an existing agent framework (LangChain/LangGraph) | agentic run | 160 |
| [real-servers/](real-servers/README.md) | The probe battery on 22 open-source MCP servers selected from npm and PyPI | server × probe | 286 |
| [fourteen-targets/](fourteen-targets/README.md) | All probes on the 14 targets (4 reference servers, 10 DVMCP challenges), three replicas | target × probe × replica | 564 |
| [ssrf-scanners/](ssrf-scanners/README.md) | Seven scanners and probe P6 on five SSRF product/version configurations | scanner × configuration | 40 |

## Key results

| Data set | Result |
| --- | --- |
| Client defences | Without defences the model decides to exfiltrate in 25/40 hostile runs and the token leaves in 22/40. The allowlist stops every hostile call (0/40, Wilson 95% 0–9%); token masking stops every exfiltration (0/40); argument validation has no effect. The hostile decision persists in every arm. |
| External host | On the JSON variant the attack chain repeats on both hosts: decision 12/20 (harness) and 11/20 (LangChain), effect 11/20 on both. Description and name poisoning reach the model in all 40 runs per host and are never followed. |
| Real servers | 44 candidates, 31 selected, 29 installed, 23 started, 22 probed. Two `vulnerable` positions, both on `markitdown-mcp` (P5 file read through `file://`, P6 SSRF), stable in three repetitions; the behaviour is declared by the package. |
| 14 targets | 188 applicable positions, 564/564 executions, no disagreement between replicas, 11 `vulnerable` positions. |
| SSRF scanners | Probe P6 shows 3 effects and 2 complete-sweep negatives; RedCell and Mcpwn show 1 effect each; the five metadata, static or fuzzing scanners are not applicable to an active SSRF test. |

## Conventions

- **Agentic metrics.** D: the model proposes the hostile call. A: the client forwards it. E: the collector receives the run's synthetic token. U: the legitimate task is completed. They are separate observations: E can be 0 with D and A at 1.
- **Probe statuses.** `vulnerable`: effect observed with proof. `secure`: complete sweep without effect. `info`: incomplete sweep or result to be reviewed. `skipped`: the surface is absent. `error`: the probe could not test the surface. Only `vulnerable` and `secure` are verdicts.
- **Technical errors** (`status = technical_error`) are not outcomes: their metrics are empty and they are excluded from every rate.
- **Intervals** are Wilson 95% and descriptive; there is no power guarantee.
- CSV files are UTF-8 with BOM and comma separator. Evidence texts are the probe engine's messages; quoted lists and payloads are copied as recorded.

## Model and environment

Agentic runs use `qwen2.5:7b` (Ollama digest `845dbda0ea48ed749caafd9e6037047aa19acfcfd82e704d7ca97d631a0b697e`), temperature 0.2, seeds from the frozen protocol, Ollama in Docker on CPU only. Probe campaigns run in Docker; third-party servers run in isolated containers without network, with synthetic canaries and a loopback collaborator. No paid provider was used.

## Raw evidence

Transcripts, model requests, network captures and per-attempt reports are archived with the laboratory and are not published here, for size (about 230 MB). Every row carries the SHA-256 of its raw record (`raw_sha256`, `report_sha256`, `evidence_sha256`, `proof_sha256`), so any record shared later can be checked against this table.
