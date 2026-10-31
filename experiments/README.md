# Experiment package

Results, tables and experimental evidence of the three MCP-VulnLab experiments, as available on 23 October 2026, plus the later executions in [v4/](v4/README.md).

1. Read [RESULTS.md](RESULTS.md) for the summary of the three experiments and the links to the data files.
2. See [LIMITATIONS.md](LIMITATIONS.md) for the conclusions the data can and cannot support.
3. Open [results.pdf](results.pdf) and [tables.xlsx](tables.xlsx) for the formatted tables.

| Folder | Content |
|---|---|
| Exp1 | Per-run CSVs, D/A/E/U rates, three example transcripts and evidence per RUN_ID |
| Exp2 | Catalogue of the 29 scenarios, 29 complete reports, benign controls and semantic classification v5 |
| Exp3 | Comparison on 14 targets, scanners, probes, SSRF and the active RedCell/Mcpwn pilot, with evidence |
| evidence | Supplementary raw files, Exp2 inputs and the results of earlier pilots in separate archives |
| v4 | Executions of 25–30 October 2026 (client defences, external host, real servers, 14-target campaign, SSRF scanners) |

Main data files:

| Content | File |
|---|---|
| Exp1: one row per run with 14 columns | [exp1_runs.csv](Exp1/per_run_data/exp1_runs.csv) |
| Exp1: rates by model, scenario and profile | [exp1_summary.csv](Exp1/recomputed_summaries/exp1_summary.csv) |
| Exp1: three transcripts and evidence by RUN_ID | [Examples](Exp1/example_transcripts/index.csv), [run evidence](Exp1/per_run_evidence/index.csv) |
| Exp2: counts of the 29 scenarios | [exp2_results_29_scenarios.csv](Exp2/historical_catalogue/exp2_results_29_scenarios.csv) |
| Exp2: parameters and five complete answers in each report | [Index of the 29 reports](Exp2/historical_catalogue/report_index.csv) |
| Exp3: the 14 targets with four comparison columns | [exp3_target_comparison.csv](Exp3/comparison_14_targets/exp3_target_comparison.csv) |
| Exp3: five SSRF configurations and status per scanner | [ssrf_results.csv](Exp3/ssrf_configurations/ssrf_results.csv) |

CSV files are UTF-8 with BOM and comma separator; paths in the cells are relative to this folder. Different campaigns and protocols have separate files.

D = malicious decision, A = forwarded call, E = receipt of the test data, U = task success criterion. Raw means a complete original record. Unknown means an answer that cannot be classified.

The files called validation inside Exp2 are experimental results of the classifier. Source codes S01–S26 used in the Excel workbook refer to the [source index](evidence/result_sources.csv). In the PDF the data paths are given under each table. For the chain batch and the v4 comparison, the raw files are in the supplementary archive, whose index lists the paths inside it.

The active RedCell/Mcpwn pilot has a [dedicated explanation](Exp3/external_probes/EXTERNAL_PROBE_RESULTS.md), CSVs and native reports. It is a separate campaign: the main workbook keeps the tables of the historical comparison. The PDF is a single document with three sections, one per experiment; all scanner and probe campaigns are in subsections 3.1–3.6, with an initial map of how the evidence fits together.

Also included are [MCP Guard and the fuzzer](Exp3/guard_and_fuzzer/RESULTS.md), the [blind P2 review](Exp3/p2_description_review/RESULTS.md) and the [earlier pilots](evidence/README.md). They are distinct populations. In the derived CSVs the `*_file` columns point to files of this package; `raw_archive` and `raw_member` identify compressed raw files. The native references kept next to these fields give the original paths.

## Language and integrity

The package was translated into English on 30 October 2026: documents, CSV headers and values, folder and file names, the PDF and the workbook. Raw evidence — JSON, JSONL, logs, HTML reports and the two archives — is unchanged: prompts, tool texts and model answers recorded in Italian are the data the experiments actually used. Columns that hold raw model or tool text (`raw_response_full`, `evidence`, `source_result`) and paths that name members of the archives are also unchanged. [MANIFEST.csv](MANIFEST.csv) maps every file to its path and SHA-256 in the 23 October package and states whether it was renamed only or translated.
