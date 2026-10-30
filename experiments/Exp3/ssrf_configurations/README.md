# Five SSRF configurations: what was measured

The campaign of 20 October 2026 uses five real configurations of webresearch/open-websearch. Our P6 probe produced three local SSRF effects and two negatives within the plan, each with 30 requests rejected by the application policy.

| Tool | Run on the five configurations | SSRF outcome available |
|---|---|---|
| P6, our probe | Yes, on all five | Three observed effects and two negatives within the plan |
| MCTS 0.1.4 | Yes, as a metadata check | Not applicable: the check used does not measure SSRF |
| Cisco 4.8.6, YARA | Not run on these five configurations | Not measured |
| MCP-Shield 1.0.4 | Not run on these five configurations | Not measured |
| RedCell 0.1.0 | Not run on these five configurations | Not measured; a positive callback exists on a separate local fetch |
| Mcpwn 1.0 | Not run on these five configurations | Not measured |
| MCP Guard 2.0.0 | Not run on these five configurations | Not measured |
| MCP Server Fuzzer 0.7.0 | Not run on these five configurations | Not measured |

**Not executed** means that a measurement is missing, without claiming that the tool cannot perform that check. **Not applicable** means that the check actually used produces no SSRF outcome. Neither equals "no vulnerability detected".

The CSV [ssrf_results.csv](ssrf_results.csv) has a separate column for each scanner. The earlier generic column `scanner_ssrf` referred only to MCTS and is now named `mcts_ssrf`. The P6 results, the counters and the links to the raw files keep their previous values. Paths in the cells are relative to the experiments folder.

The Cisco/MCP-Shield comparison on the 14 STDIO targets is in [comparison_14_targets](../comparison_14_targets/). The RedCell/Mcpwn pilot of 19 October is documented [separately](../external_probes/EXTERNAL_PROBE_RESULTS.md). The local fetch of that pilot is not one of the five products/versions of this matrix: its evidence cannot fill these cells.

In this package the SSRF comparison of the external scanners on the same five products/versions therefore remains unmeasured; it was run afterwards and is published in [../../v4/ssrf-scanners/](../../v4/ssrf-scanners/README.md). This section makes the existing coverage explicit and introduces no new executions.

The five original reports of the MCTS metadata check are in [mcts_metadata_check](mcts_metadata_check/); the `mcts_metadata_report` column of the CSV links them to their targets. Any alarms in those reports are not SSRF detections.
