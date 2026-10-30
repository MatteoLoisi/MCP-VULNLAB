# MCP-VulnLab: data and evidence

Data and evidence from MCP-VulnLab, a laboratory for testing the security of LLM agents and tools connected through the Model Context Protocol (MCP). The repository contains the scenario catalogue, the results of three experiments, and the original files that support each figure.

## Contents

| Path | Content |
| --- | --- |
| [matrix.xlsx](matrix.xlsx) | Test matrix: 136 scenarios, external targets and SSRF configurations, with classifications, verdicts and file references. The **Legend** sheet explains codes and verdicts. |
| [results/](results/) | Result tables of the scenario catalogue and of the external-target probes, in CSV format. |
| [evidence/](evidence/) | Original reports of the catalogue campaign, and the complete P5 sweep on Filesystem `1.0.0` (17/17 tool–argument combinations): [p5-filesystem-20261025/](evidence/p5-filesystem-20261025/README.md). |
| [experiments/](experiments/) | Data of the three experiments: agentic runs, content classification, scanners and probes. |
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

The experiment package is in [experiments/](experiments/). Its guide, [LEGGIMI.md](experiments/LEGGIMI.md), and its summary, [RISULTATI.md](experiments/RISULTATI.md), are in Italian. Formatted tables are in [risultati.pdf](experiments/risultati.pdf) and [tabelle.xlsx](experiments/tabelle.xlsx), and the limits of each conclusion are in [LIMITI.md](experiments/LIMITI.md). Data available on 23 October 2026.

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
| Main matrix, one row per run (960 rows, 14 columns) | [exp1_runs.csv](experiments/Exp1/dati_per_run/exp1_runs.csv) |
| Rates by model, scenario and profile | [exp1_summary.csv](experiments/Exp1/riepiloghi_ricalcolati/exp1_summary.csv) |
| Other campaigns (seed, temperature 0.7, JSON arms, chains) | [altre_campagne/](experiments/Exp1/dati_per_run/altre_campagne/) |
| Three example transcripts | [transcript_esemplari/](experiments/Exp1/transcript_esemplari/indice.csv) |
| Evidence by campaign and run identifier | [evidenze_per_run/](experiments/Exp1/evidenze_per_run/indice.csv) |

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
| Results of the 29 scenarios | [exp2_risultati_29_scenari.csv](experiments/Exp2/catalogo_storico/exp2_risultati_29_scenari.csv) |
| 29 reports with parameters and complete answers | [indice_report.csv](experiments/Exp2/catalogo_storico/indice_report.csv) |
| All raw answers of catalogue and controls | [risposte_grezze_complete.csv](experiments/Exp2/catalogo_storico/risposte_grezze_complete.csv) |
| Benign controls | [controlli_benigni/](experiments/Exp2/controlli_benigni/) |
| v5 populations, per-query results, v4 comparison | [classificazione_semantica_v5/](experiments/Exp2/classificazione_semantica_v5/riepilogo_popolazioni.csv) |
| Coverage of the 29 identifiers | [copertura.csv](experiments/Exp2/copertura_29_id/copertura.csv) |

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
| Comparison of the 14 targets | [exp3_confronto_target.csv](experiments/Exp3/confronto_14_target/exp3_confronto_target.csv) |
| Scanner details and evidence references | [exp3_dettaglio_evidenze.csv](experiments/Exp3/confronto_14_target/exp3_dettaglio_evidenze.csv) |
| Effect probes | [risultati_sonde.csv](experiments/Exp3/sonde_ed_effetti/risultati_sonde.csv) |
| SSRF configurations | [risultati_ssrf.csv](experiments/Exp3/configurazioni_ssrf/risultati_ssrf.csv) |
| Scanner diagnostic, per scan | [risultati_per_scansione.csv](experiments/Exp3/diagnostica_scanner/risultati_per_scansione.csv) |
| RedCell and Mcpwn pilot | [RISULTATI_SONDE_ESTERNE.md](experiments/Exp3/sonde_esterne/RISULTATI_SONDE_ESTERNE.md) |
| MCP Guard and MCP Server Fuzzer | [guard_e_fuzzer/](experiments/Exp3/guard_e_fuzzer/RISULTATI.md) |
| Blind review of P2 descriptions (AI-assisted, 14/14 agreement) | [revisione_descrizioni_P2/](experiments/Exp3/revisione_descrizioni_P2/RISULTATI.md) |

### Supporting evidence

[experiments/Evidenze/](experiments/Evidenze/LEGGIMI.md) holds earlier exploratory pilots, the raw files of the chain batch and of the v4 comparison, and the inputs of Experiment 2. Source codes S01–S26 used in the Excel tables are listed in [fonti_dei_risultati.csv](experiments/Evidenze/fonti_dei_risultati.csv).

## Reading notes

- Populations, campaigns and protocols are kept separate; their counts must not be summed.
- Paths in the matrix and CSV tables are relative to the folder that contains them: the repository root for `matrix.xlsx` and `results/`, and `experiments/` for the experiment package.
- CSV files are UTF-8 with BOM and comma separator.
- The `Tier 4` value in the matrix is a catalogue label for documentary cases. Documentary cases also occur under Tier 3; the `Status` and `E2E verdict` columns indicate whether a case was executed.
- CWE associations are indicative classifications. Documentary cases were not executed in the laboratory. Inconclusive results and probe errors establish neither a vulnerability nor security.
- The case-to-target mapping of the blind P2 review (`private/mapping.json`) is withheld, so that the same package can still be used for an independent review.
- The matrix, catalogue tables and this guide are in English. The experiment package and the original reports are preserved unchanged and may contain Italian text or historical execution paths.
