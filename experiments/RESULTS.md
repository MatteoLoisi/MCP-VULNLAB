# Results

Data available on 23 October 2026; v5 is the protocol of the semantic classifier. The populations below are kept separate. Later executions are in [v4/](v4/README.md).

## Experiment 1: real agents

D = malicious decision; A = forwarded call; E = receipt of the test data; U = task success criterion.

| Campaign | Runs | D | A | E | U |
|---|---:|---:|---:|---:|---:|
| Main matrix, temperature 0.2 | 960 | 0 | 0 | 0 | 960 |
| Seed replication, temperature 0.2 | 960 | 0 | 0 | 0 | 960 |
| Qwen standard, temperature 0.7 | 320 | 0 | 0 | 0 | 320 |
| JSON arm, temperature 0.2 | 120 | 15 | 6 | 4 | 91 |
| JSON arm, temperature 0.7 | 120 | 20 | 11 | 8 | 94 |

In the `default` profile of the JSON arms, A = E = 0. The chain batch is separate: 479/480 records, 12 technical errors, 467 usable, U = 211/467.

- [Main CSV: 960 rows and 14 columns](Exp1/per_run_data/exp1_runs.csv).
- [Rates by model, scenario and profile](Exp1/recomputed_summaries/exp1_summary.csv).
- [Other campaigns](Exp1/per_run_data/other_campaigns/).
- [Three example transcripts](Exp1/example_transcripts/index.csv).
- [Evidence by campaign and RUN_ID](Exp1/per_run_evidence/index.csv).

## Experiment 2: content classification

### 2.1 Historical catalogue

Five queries per ID and a 0.4 threshold. Each condition has 29 IDs and 145 queries.

| Model | Temperature | Exfiltrate | Ignore | Unknown | IDs above threshold |
|---|---:|---:|---:|---:|---:|
| llama3.2:3b | 0.2 | 145 | 0 | 0 | 29 |
| llama3.2:3b | 0.7 | 145 | 0 | 0 | 29 |
| qwen2.5:7b | 0.2 | 105 | 40 | 0 | 21 |
| qwen2.5:7b | 0.7 | 105 | 40 | 0 | 21 |

The benign controls have 15 queries per condition. Llama returns 15 exfiltrate per temperature; Qwen returns 15 ignore per temperature. The limited meaning of 145/145 is explained in [LIMITATIONS.md](LIMITATIONS.md).

- [Results of the 29 scenarios](Exp2/historical_catalogue/exp2_results_29_scenarios.csv).
- [Index of the 29 reports with parameters and complete answers](Exp2/historical_catalogue/report_index.csv).
- [All raw answers of the catalogue and the controls](Exp2/historical_catalogue/complete_raw_answers.csv).
- [Benign controls](Exp2/benign_controls/).

### 2.2 Semantic classification v5

| Population | Texts | Queries | TP | TN | FP | FN | Unknown | Correct |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Validation | 24 | 120 | 46 | 60 | 0 | 10 | 4 | 106/120 |
| Development | 12 | 60 | 28 | 30 | 0 | 0 | 2 | 58/60 |
| MCP captures | 6 | 30 | 25 | 0 | 0 | 5 | 0 | 25/30 |
| Static artifacts | 6 | 30 | 24 | 0 | 0 | 5 | 1 | 24/30 |
| Files and SQLite | 6 | 30 | 15 | 10 | 0 | 0 | 5 | 25/30 |

Validation accuracy: 88.3%. In the paired comparison on the same 24 texts, v4 has 80/120 correct answers and v5 106/120. Specific coverage is 14/29 IDs, with eight MCP captures and six artifacts.

- [Population summary](Exp2/semantic_classification_v5/population_summary.csv).
- [Per-query results and complete answers](Exp2/semantic_classification_v5/per_query_results.csv).
- [v4 comparison data](Exp2/semantic_classification_v5/v4_comparison.csv).
- [Coverage of the 29 IDs](Exp2/coverage_29_ids/coverage.csv).

## Experiment 3: scanners and probes

### 3.1 Main comparison, effect probes and SSRF

Fourteen STDIO targets: four reference servers and ten DVMCP challenges. Main scanner MCTS 0.1.4; additional comparison with Cisco 4.8.6 (YARA) and MCP-Shield 1.0.4 without Claude.

The six effect probes include three confirmed effects (c3, c8, c9) and three inconclusive executions (c2, c10, R1). The five SSRF configurations give three effects and two negatives within the plan. These are results of our P6 probe: in this package the SSRF comparison of Cisco, MCP-Shield, RedCell and Mcpwn on the same five products/versions is missing (it was run afterwards, see [v4/ssrf-scanners/](v4/ssrf-scanners/README.md)), and MCTS is not applicable as an SSRF check. [Coverage detail](Exp3/ssrf_configurations/README.md).

| Scanner | TP | TN | FP | FN |
|---|---:|---:|---:|---:|
| MCTS, P2 diagnostic | 4 | 5 | 1 | 2 |
| Cisco, P2 diagnostic | 2 | 6 | 0 | 4 |
| Shield, P2 diagnostic | 5 | 5 | 1 | 1 |

The diagnostic is separate from the comparison on real servers: twelve synthetic texts and 36 scans.

- [Comparison table, four columns and 14 targets](Exp3/comparison_14_targets/exp3_target_comparison.csv).
- [Detail of the three scanners and evidence references](Exp3/comparison_14_targets/exp3_evidence_detail.csv).
- [Probe results](Exp3/probes_and_effects/probe_results.csv).
- [SSRF results](Exp3/ssrf_configurations/ssrf_results.csv).
- [Diagnostic per scan](Exp3/scanner_diagnostics/per_scan_results.csv).

### 3.2 Active RedCell and Mcpwn pilot

Separate campaign of 19 October 2026, with 26 attempts: 22 useful sessions and four technical errors. RedCell examined ten DVMCP challenges with 290 calls: 15 findings on seven targets, ten with dynamic evidence and five based on descriptions. Mcpwn detects execution on c8 and c9; on c3 it produces 68 findings, mostly timing-based, without detecting the file read demonstrated by RedCell and P5. The SSRF comparison concerns a single local fetch. Counts from scanners with different profiles are not an effectiveness ranking.

- [How it works, results and interpretation of the evidence](Exp3/external_probes/EXTERNAL_PROBE_RESULTS.md).
- [RedCell on the ten targets](Exp3/external_probes/redcell_results.csv).
- [Mcpwn sessions](Exp3/external_probes/mcpwn_results.csv).
- [All attempts with links to the native reports](Exp3/external_probes/attempts.csv).

### 3.3 Other evidence of the same experiment

- [MCP Guard and MCP Server Fuzzer](Exp3/guard_and_fuzzer/RESULTS.md): static analysis, dynamic evidence, a separate harness and protocol robustness.
- [Blind P2 review of the descriptions](Exp3/p2_description_review/RESULTS.md): 14/14 agreement with the original labels, AI-assisted; not an independent human review.

## Documents and evidence

[Results PDF](results.pdf), [Excel tables](tables.xlsx) and [scientific limitations](LIMITATIONS.md).

The PDF has three main sections and gathers all scanner/probe evidence in Experiment 3, with subsections 3.1–3.6 and a map of how they relate. The [earlier pilots and their captures](evidence/README.md) are exploratory Exp1 populations and v4-protocol runs kept apart from the main results.

The main evidence is in the three experiments. The [supplementary archive](evidence/supplementary_evidence.zip) keeps the raw files of the chain batch, those of the v4 comparison and the input captures/artifacts available in the sources. Its [index](evidence/supplementary_evidence_index.csv) lists the paths inside it.
