# Limitations of the results

These points state how far the conclusions supported by the data go. They are not a list of technical checks.

## 1. Seed of the main matrix

In the 960 original runs the seed was not requested. A separate replication of 960 runs with 20 seeds per cell exists; it does not reconstruct the seeds of the first runs. [Main data](Exp1/per_run_data/exp1_runs.csv), [replication](Exp1/per_run_data/other_campaigns/seed_replication.csv).

## 2. Incomplete chain batch and historical provenance

479 of the 480 planned records are available. Twelve have technical errors: 467 usable observations remain, with U = 211/467. One run is lost. The historical summary also declares four documentary discrepancies and a ledger of attempts reconstructed afterwards; the current documentation alone does not prove the original integrity. [Observations](Exp1/per_run_data/other_campaigns/chain_observations.csv), [historical closure](Exp1/chain_closure.json).

## 3. The historical 145/145 does not measure accuracy

The 29 IDs of the historical catalogue reuse seven texts. Llama returns exfiltrate on the 15 benign controls per temperature as well. The value 145/145 therefore describes the decisions of the historical protocol and does not show that all 29 MCP mechanisms are vulnerable. The v5 semantic results follow a different protocol. [Catalogue](Exp2/historical_catalogue/exp2_results_29_scenarios.csv), [benign controls](Exp2/benign_controls/benign_llama32_temperature02.csv).

## 4. Specific v5 coverage: 14 of 29 IDs

14 IDs are measured: eight through MCP captures and six through static artifacts. Four captures are missing and eleven mechanisms have no specific validation. Classifying a text does not, by itself, show that an agent performs an action or exfiltrates data. [Full coverage of the 29 IDs](Exp2/coverage_29_ids/coverage.csv).

## 5. Three inconclusive historical effect probes

These outcomes belong to the campaign before the RedCell/Mcpwn pilot of 19 October. The pilot has its own captures and profiles and does not replace those reports.

- **c2 and c10, P4:** nine attempts each; the reports say secure but lack specific evidence sufficient to confirm that verdict. Reports and captures are present. Neither a certain vulnerability nor a demonstrated security can be inferred.
- **R1-filesystem, P5:** the limit of 60 attempts was reached covering 3 of the 17 planned units. The result is incomplete; the complete sweep (17/17, secure) is in [evidence/p5-filesystem-20261025/](../evidence/p5-filesystem-20261025/README.md).

The P2 textual indicators on c2 and c10 remain distinct from effect evidence. The confirmed effects on c3, c8 and c9 remain valid. [Results of the six probes](Exp3/probes_and_effects/probe_results.csv).

## 6. Temperature and utility comparisons

The comparisons between the JSON arms at temperature 0.2 and 0.7 are descriptive: runtime, content and pretext change as well. They do not isolate the effect of temperature alone. U checks for an expected string, not the general semantic quality of the answer. Main campaigns, replications and pilots are separate populations. [Exp1 summaries](Exp1/recomputed_summaries/).

## 7. Repetitions and local labels

Five queries on the same text are not five independent examples. The v5 validation has 24 texts and 120 queries. Labels are local and AI-assisted, without independent human validation. The 270 v5 queries belong to five populations and are not a single benchmark. [v5 results](Exp2/semantic_classification_v5/population_summary.csv).

## 8. Scope of the scanner comparison

MCTS's generic textual alarms do not demonstrate execution or exfiltration. The additional diagnostic uses twelve synthetic descriptions and 36 scans, built with knowledge of the rule families: it does not support a general ranking of the scanners. Information on their maintenance is as archived on 17 October 2026. [Comparison on the targets](Exp3/comparison_14_targets/exp3_target_comparison.csv), [diagnostic](Exp3/scanner_diagnostics/confusion_matrices.csv).

## 9. SSRF negatives within the plan

The two negative configurations have 30 attempts and 30 rejections by the application policy each, with zero middleware blocks. This does not show a universal absence of SSRF. The SSRF comparison with MCTS is not applicable because the equivalent check is missing. In this package Cisco and MCP-Shield were not run on these five configurations, and RedCell and Mcpwn have no measurements on the same five products/versions; the RedCell callback of the separate pilot does not fill this gap. The later comparison of seven scanners on the five configurations is in [v4/ssrf-scanners/](v4/ssrf-scanners/README.md). [Coverage by scanner](Exp3/ssrf_configurations/README.md), [five SSRF configurations](Exp3/ssrf_configurations/ssrf_results.csv).

## 10. Optional chain extension not run

The 320 optional runs of the chain extension were not executed. They are distinct from the 320 standard Qwen runs at temperature 0.7, which were completed. [Qwen 0.7 standard runs](Exp1/per_run_data/other_campaigns/qwen_temperature07.csv).

## 11. Active RedCell and Mcpwn pilot

The pilot compares tools with different profiles and one useful execution per case, plus the technical attempts kept. The 15 RedCell findings on the ten DVMCP challenges include ten dynamic proofs and five indicators in descriptions. The 68 Mcpwn findings on c3 are not 68 independent confirmed vulnerabilities: 51 are timing-based. Outcomes without alarms do not demonstrate general security; a tool that intentionally exposes a shell does not demonstrate a bypass by itself. The SSRF test concerns a single local fetch server with a loopback callback, without access to cloud metadata or external network. No general precision, recall or FPR is estimated. Four technical errors are kept and distinguished from negatives. [Pilot results and evidence](Exp3/external_probes/EXTERNAL_PROBE_RESULTS.md).

## 12. MCP Guard, fuzzer and blind P2 review

One Guard attempt in the official Docker sandbox is incomplete. The useful evidence uses an external container: it detects the read on c3 and the execution/read on c8, but not the injection on c9 demonstrated by the separate harness. The static analyses remain indicators. The fuzzer covers four targets and produces accepted malformed inputs and reflections, without crashes: these are not confirmed exploits. The P2 agreement of 14/14 is an AI-assisted blind replication, not an independent human ground truth. Guard and the fuzzer were not run on the five SSRF configurations. [Guard/fuzzer results](Exp3/guard_and_fuzzer/RESULTS.md), [P2 results](Exp3/p2_description_review/RESULTS.md).
