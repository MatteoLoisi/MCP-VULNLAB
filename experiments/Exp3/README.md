# How to read Exp3

The comparison uses MCTS 0.1.4 and fourteen STDIO targets. [Table with the four original columns](comparison_14_targets/exp3_target_comparison.csv). MCTS alarms are generic textual indicators, not demonstrated effects. The [structured detail](comparison_14_targets/exp3_evidence_detail.csv) also includes Cisco 4.8.6 YARA and MCP-Shield 1.0.4 without Claude. Paths in the CSVs are relative to the experiments folder.

The probes give three confirmed effects and three results that cannot be certified. c2 and c10 still have P2 textual indicators: the limit concerns their effect probes. R1 has incomplete coverage. Negatives are limited to the declared checks.

The five SSRF configurations are separate from the metadata comparison: three observed effects and two negatives within the plan. Rejections by the application policy are distinct from middleware blocks. The diagnostic uses twelve synthetic descriptions and thirty-six scans; the matrices are rebuilt from the archived P2 decisions, without new independent labelling.

Versions, open-source licence and maintenance status are documented in the snapshot of 17 October 2026; no current online check is claimed. Every extracted raw file and log has an entry in the evidence index.

In this package, Cisco and MCP-Shield were not run on the five SSRF configurations, and RedCell and Mcpwn have no measurements on those five products/versions either. MCTS is present only as a metadata check, without an SSRF outcome. The [coverage by scanner](ssrf_configurations/README.md) distinguishes not executed from not applicable. The RedCell callback concerns a separate local fetch. The later run of seven scanners on the five configurations is in [../v4/ssrf-scanners/](../v4/ssrf-scanners/README.md).

The other campaigns with external scanners are [RedCell/Mcpwn](external_probes/EXTERNAL_PROBE_RESULTS.md) and [MCP Guard/fuzzer](guard_and_fuzzer/RESULTS.md). The [blind review of the descriptions](p2_description_review/RESULTS.md) is AI-assisted and measures no new effects.

| Tool | Targets actually examined | Type of result |
|---|---|---|
| MCTS 0.1.4 | 14 STDIO; five SSRF configurations as a metadata check only | Textual indicators; no SSRF effect check |
| Cisco 4.8.6, YARA | 14 STDIO | Textual indicators |
| MCP-Shield 1.0.4 | 14 STDIO | Textual indicators |
| RedCell 0.1.0 | Ten DVMCP, two controls and a local fetch | Textual indicators and active proofs of reads, commands and callbacks |
| Mcpwn 1.0 | c3, c8, c9 and two controls, with different profiles | Execution signatures and timing, protocol and prompt indicators |
| MCP Guard 2.0.0 | c3, c8 and c9; official Docker attempt on c8 incomplete | Static analysis and dynamic proofs in an external container |
| MCP Server Fuzzer 0.7.0 | c2, R1-filesystem, R2-memory, R3-sequential-thinking | Protocol robustness, accepted inputs and reflections |

The common diagnostic on the twelve synthetic texts concerns MCTS, Cisco and MCP-Shield. The other campaigns have different samples and profiles: the counts are not like-for-like comparisons. In this package our P6 is the only effect measurement present on all five products/versions of the SSRF matrix.
