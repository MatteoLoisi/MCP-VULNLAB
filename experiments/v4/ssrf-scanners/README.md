# Other scanners on the five SSRF configurations

Request: run the other scanners on the five SSRF configurations.

## Design

Five product/version configurations with a known expected outcome: `ssrf-1-webresearch` (`@mzxrai/mcp-webresearch` 0.1.7) and `open-websearch` 2.1.6 and 2.1.7 are expected vulnerable; `open-websearch` 2.1.8 and 2.1.11 are expected secure. Eight analysers: MCTS 0.1.4, Cisco YARA 4.8.6, MCP-Shield 1.0.4 without Claude, MCP Fuzzer 0.7.0, MCP Guard 2.0.0 (static, without dependencies), RedCell 0.1.0, Mcpwn 1.0 (SSRFTest module) and the laboratory probe P6. 5 × 8 = **40 positions**; 53 attempts, 40 selected for the comparison and 13 excluded (preparation failures or interruptions, not negatives). Campaign of 23 October; read-only integrity audit on 29 October (244/244 file hashes verified).

## Capability by analyser

| Analyser | Class | Effects | Not observed | Sweep negative | Not applicable |
| --- | --- | ---: | ---: | ---: | ---: |
| Cisco YARA 4.8.6 | metadata analysis | 0 | 0 | 0 | 5 |
| MCP Fuzzer 0.7.0 | fuzzing | 0 | 0 | 0 | 5 |
| MCP Guard 2.0.0 | static analysis | 0 | 0 | 0 | 5 |
| MCP-Shield 1.0.4 | metadata analysis | 0 | 0 | 0 | 5 |
| MCTS 0.1.4 | metadata analysis | 0 | 0 | 0 | 5 |
| Mcpwn 1.0 (SSRFTest) | active SSRF | 1 | 4 | 0 | 0 |
| RedCell 0.1.0 | active SSRF | 1 | 4 | 0 | 0 |
| Probe P6 | active SSRF | 3 | 0 | 2 | 0 |

P6 shows the effect on the three expected-vulnerable configurations and completes its sweep without effect on the two expected-secure ones. RedCell and Mcpwn show the effect only on `webresearch`. The metadata, static and fuzzing analysers cannot test an active SSRF and are recorded as not applicable, not as misses.

## Files

| File | Content |
| --- | --- |
| [positions.csv](positions.csv) | One row per configuration × analyser: state, SSRF detected, evidence type, generic alarms, tools, attempts, proof and its SHA-256, note |
| [attempts.csv](attempts.csv) | All 53 attempts with exit code, times, timeout, selection and error |
| [comparison.csv](comparison.csv) | Compact comparison: expected vulnerability, scanner outcome and probe outcome per configuration |
| [scanner_capability.csv](scanner_capability.csv) | The capability table above, with evidence types and limits |
| [capability_matrix.csv](capability_matrix.csv) | Analyser × configuration matrix |

## Limitations

- Only local callbacks received are evidence of SSRF; access to cloud metadata, real exfiltration and DNS rebinding are not demonstrated.
- P6 runs on Windows/Node, RedCell and Mcpwn in Linux containers: the groups are not identical replicas and must not be aggregated.
- For 2.1.6/2.1.7 the P6 proof uses `::ffff:127.0.0.1`, a variant absent from the scanners' native payloads: the gap is declared, not a false negative of the scanners.
- On 2.1.8 and 2.1.11 P6 completes 30 attempts per version with 30 policy rejections: a sweep negative, not a security certification.
- Mcpwn was run through its SSRFTest module, not through the standard CLI, which does not call it; its eight heuristic `SSRF_RESPONSE` alarms on the open-websearch versions have no HTTP receipt and are not counted as SSRF.
- `not_observed` is not a negative and must not be summed with the observed effects.
