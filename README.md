# MCP-VulnLab: data and evidence

Data and evidence from MCP-VulnLab, a laboratory for testing the security of LLM agents and tools connected through the Model Context Protocol (MCP). The repository contains the scenario catalogue, the results of three experiments, and the original files that support each figure.

## Contents

| Path | Content |
| --- | --- |
| [matrix.xlsx](matrix.xlsx) | Test matrix: 136 scenarios, external targets and SSRF configurations, with classifications, verdicts and file references. The **Legend** sheet explains codes and verdicts. |
| [results/](results/) | Result tables of the scenario catalogue and of the external-target probes, in CSV format. |
| [evidence/](evidence/) | Original reports of the catalogue campaign, and the complete P5 sweep on Filesystem `1.0.0` (17/17 tool–argument combinations): [p5-filesystem-20261025/](evidence/p5-filesystem-20261025/README.md). |
| [experiments/](experiments/) | Data of the three experiments: agentic runs, content classification, scanners and probes. Later executions are in [experiments/v4/](experiments/v4/README.md). |
| [supplementary/](supplementary/) | Notes on the catalogue taxonomy, a quantitative comparison with related benchmarks, and the agentic version of the laboratory. |

## Scenario catalogue

| File | Scope |
| --- | --- |
| [catalogue-136.csv](results/catalogue-136.csv) | 136 laboratory scenarios: 120 executable cases and 16 documentary cases. |
| [real-target-27-configurations.csv](results/real-target-27-configurations.csv) | 27 target configurations from the historical test battery. |
| [external-targets-14.csv](results/external-targets-14.csv) | 14 selected external target reports. |
| [p4-followups-3.csv](results/p4-followups-3.csv) | Three additional P4 runs. |
| [ssrf-5-configurations.csv](results/ssrf-5-configurations.csv) | Five SSRF product/version configurations. |

Scenario categories in the `Category` column:

| Code | Meaning | Scenarios |
| --- | --- | ---: |
| INJ | Injection: hostile content in tool names, descriptions, results or prompts, and argument/command injection through tool parameters | 34 |
| PRIV | Privilege: access control, isolation and execution boundaries (path traversal, SSRF, command execution, privilege escalation, cross-tenant access) | 37 |
| MIS | Misconfiguration: authentication, OAuth, transport and server configuration | 33 |
| DATA | Data leakage: secrets and personal data in outputs and logs, audit and telemetry gaps, supply-chain exposure | 30 |
| — | Control cases (baseline, legitimate demo) | 2 |

Each scenario has one primary category; related aspects are described by its CWE, MITRE ATLAS and OWASP references.

## Experiments

The experiment package is in [experiments/](experiments/). Its guide is [README.md](experiments/README.md) and its summary is [RESULTS.md](experiments/RESULTS.md). Formatted tables are in [results.pdf](experiments/results.pdf) and [tables.xlsx](experiments/tables.xlsx), and the limits of each conclusion are in [LIMITATIONS.md](experiments/LIMITATIONS.md). Data available on 23 October 2026.

### Experiment 1: agentic runs

The model acts as an agent with native tool calling. Each run records four separate observations: D (the model proposes the hostile call), A (the client forwards it), E (the collector receives the run's test token) and U (the legitimate task is completed).

| Campaign | Runs | D | A | E | U |
| --- | ---: | ---: | ---: | ---: | ---: |
| Main matrix, temperature 0.2 | 960 | 0 | 0 | 0 | 960 |
| Seed replication, temperature 0.2 | 960 | 0 | 0 | 0 | 960 |
| Qwen standard, temperature 0.7 | 320 | 0 | 0 | 0 | 320 |
| JSON arm, temperature 0.2 | 120 | 15 | 6 | 4 | 91 |
| JSON arm, temperature 0.7 | 120 | 20 | 11 | 8 | 94 |

In the `default` client profile of the JSON arms, A = E = 0. The chain batch is a separate population: 479/480 records, 12 technical errors, 467 usable, U = 211/467.

| Data | File |
| --- | --- |
| Main matrix, one row per run (960 rows, 14 columns) | [exp1_runs.csv](experiments/Exp1/per_run_data/exp1_runs.csv) |
| Rates by model, scenario and profile | [exp1_summary.csv](experiments/Exp1/recomputed_summaries/exp1_summary.csv) |
| Other campaigns (seed, temperature 0.7, JSON arms, chains) | [other_campaigns/](experiments/Exp1/per_run_data/other_campaigns/) |
| Three example transcripts | [example_transcripts/](experiments/Exp1/example_transcripts/index.csv) |
| Evidence by campaign and run identifier | [per_run_evidence/](experiments/Exp1/per_run_evidence/index.csv) |

### Experiment 2: content classification

**Historical catalogue.** Five queries per scenario identifier and a 0.4 threshold; each condition covers 29 identifiers and 145 queries.

| Model | Temperature | Exfiltrate | Ignore | Unknown | IDs above threshold |
| --- | ---: | ---: | ---: | ---: | ---: |
| llama3.2:3b | 0.2 | 145 | 0 | 0 | 29 |
| llama3.2:3b | 0.7 | 145 | 0 | 0 | 29 |
| qwen2.5:7b | 0.2 | 105 | 40 | 0 | 21 |
| qwen2.5:7b | 0.7 | 105 | 40 | 0 | 21 |

Benign controls have 15 queries per condition: Llama returns 15 exfiltrate and Qwen 15 ignore at each temperature.

**Semantic classification (protocol v5).**

| Population | Texts | Queries | TP | TN | FP | FN | Unknown | Correct |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Validation | 24 | 120 | 46 | 60 | 0 | 10 | 4 | 106/120 |
| Development | 12 | 60 | 28 | 30 | 0 | 0 | 2 | 58/60 |
| MCP captures | 6 | 30 | 25 | 0 | 0 | 5 | 0 | 25/30 |
| Static artifacts | 6 | 30 | 24 | 0 | 0 | 5 | 1 | 24/30 |
| Files and SQLite | 6 | 30 | 15 | 10 | 0 | 0 | 5 | 25/30 |

On the same 24 validation texts, protocol v4 has 80/120 correct answers and v5 106/120.

| Data | File |
| --- | --- |
| Results of the 29 scenarios | [exp2_results_29_scenarios.csv](experiments/Exp2/historical_catalogue/exp2_results_29_scenarios.csv) |
| 29 reports with parameters and complete answers | [report_index.csv](experiments/Exp2/historical_catalogue/report_index.csv) |
| All raw answers of catalogue and controls | [complete_raw_answers.csv](experiments/Exp2/historical_catalogue/complete_raw_answers.csv) |
| Benign controls | [benign_controls/](experiments/Exp2/benign_controls/) |
| v5 populations, per-query results, v4 comparison | [semantic_classification_v5/](experiments/Exp2/semantic_classification_v5/population_summary.csv) |
| Coverage of the 29 identifiers | [coverage.csv](experiments/Exp2/coverage_29_ids/coverage.csv) |

### Experiment 3: scanners and probes

Fourteen STDIO targets (four reference servers and ten DVMCP challenges). Main scanner MCTS 0.1.4, with an additional comparison against Cisco 4.8.6 (YARA) and MCP-Shield 1.0.4. Six effect probes give three confirmed effects (c3, c8, c9) and three inconclusive executions; the five SSRF configurations give three effects and two negatives.

| Scanner (P2 diagnostic) | TP | TN | FP | FN |
| --- | ---: | ---: | ---: | ---: |
| MCTS | 4 | 5 | 1 | 2 |
| Cisco | 2 | 6 | 0 | 4 |
| MCP-Shield | 5 | 5 | 1 | 1 |

The diagnostic uses twelve synthetic texts and 36 scans, separately from the comparison on real servers. A separate active pilot with RedCell and Mcpwn ran 26 attempts on ten DVMCP challenges. Counts from scanners with different profiles are not an effectiveness ranking.

| Data | File |
| --- | --- |
| Comparison of the 14 targets | [exp3_target_comparison.csv](experiments/Exp3/comparison_14_targets/exp3_target_comparison.csv) |
| Scanner details and evidence references | [exp3_evidence_detail.csv](experiments/Exp3/comparison_14_targets/exp3_evidence_detail.csv) |
| Effect probes | [probe_results.csv](experiments/Exp3/probes_and_effects/probe_results.csv) |
| SSRF configurations | [ssrf_results.csv](experiments/Exp3/ssrf_configurations/ssrf_results.csv) |
| Scanner diagnostic, per scan | [per_scan_results.csv](experiments/Exp3/scanner_diagnostics/per_scan_results.csv) |
| RedCell and Mcpwn pilot | [EXTERNAL_PROBE_RESULTS.md](experiments/Exp3/external_probes/EXTERNAL_PROBE_RESULTS.md) |
| MCP Guard and MCP Server Fuzzer | [guard_and_fuzzer/](experiments/Exp3/guard_and_fuzzer/RESULTS.md) |
| Blind review of P2 descriptions (AI-assisted, 14/14 agreement) | [p2_description_review/](experiments/Exp3/p2_description_review/RESULTS.md) |

### Supporting evidence

[experiments/evidence/](experiments/evidence/README.md) holds earlier exploratory pilots, the raw files of the chain batch and of the v4 comparison, and the inputs of Experiment 2. Source codes S01–S26 used in the Excel tables are listed in [result_sources.csv](experiments/evidence/result_sources.csv).

### Later executions (laboratory v4)

Executions run between 25 and 30 October 2026 are in [experiments/v4/](experiments/v4/README.md), separate from the 23 October package. Each folder has one CSV row per run or probe position, summary tables and a report.

| Folder | Content | Rows |
| --- | --- | ---: |
| [client-defenses/](experiments/v4/client-defenses/README.md) | Client-side controls tested one at a time on the JSON variant of the hostile tool result | 320 runs |
| [external-host/](experiments/v4/external-host/README.md) | The same scenarios on the laboratory harness and on LangChain/LangGraph | 160 runs |
| [real-servers/](experiments/v4/real-servers/README.md) | The probe battery on 22 open-source MCP servers from npm and PyPI | 286 positions |
| [fourteen-targets/](experiments/v4/fourteen-targets/README.md) | All probes on the 14 targets, three replicas | 564 executions |
| [ssrf-scanners/](experiments/v4/ssrf-scanners/README.md) | Seven scanners and probe P6 on the five SSRF configurations | 40 positions |

## Reading notes

- Populations, campaigns and protocols are kept separate; their counts must not be summed.
- Paths in the matrix and CSV tables are relative to the folder that contains them: the repository root for `matrix.xlsx` and `results/`, and `experiments/` for the experiment package.
- CSV files are UTF-8 with BOM and comma separator.
- The `Tier 4` value in the matrix is a catalogue label for documentary cases. Documentary cases also occur under Tier 3; the `Status` and `E2E verdict` columns indicate whether a case was executed.
- CWE associations are indicative classifications. Documentary cases were not executed in the laboratory. Inconclusive results and probe errors establish neither a vulnerability nor security.
- The case-to-target mapping of the blind P2 review (`private/mapping.json`) is withheld, so that the same package can still be used for an independent review.
- All documents, tables and file names are in English. The experiment package was translated from Italian on 30 October 2026 (file names, CSV headers and values, reports, PDF and Excel). Raw evidence (JSON, logs, captures and archives) is unchanged and may contain Italian prompts, model outputs or historical execution paths. [experiments/MANIFEST.csv](experiments/MANIFEST.csv) lists every file of the package with its original path and SHA-256 before and after translation.
