# MCP-VulnLab Results

Supporting material for the thesis experiments on Model Context Protocol (MCP) security.

## Contents

- [matrix.xlsx](matrix.xlsx): the main test matrix, classifications and result references. The **Legend** sheet explains the codes and verdicts.
- [results/](results/): result tables in CSV format.
- [evidence/](evidence/): original reports, runtime records and network captures, grouped by experiment.

## Result tables

| File | Scope |
| --- | --- |
| [catalogue-136.csv](results/catalogue-136.csv) | 136 laboratory scenarios: 120 executable cases and 16 documentary cases. |
| [real-target-27-configurations.csv](results/real-target-27-configurations.csv) | 27 target configurations from the historical test battery. |
| [thesis-external-14.csv](results/thesis-external-14.csv) | 14 selected external target reports. |
| [thesis-p4-followups-3.csv](results/thesis-p4-followups-3.csv) | Three additional P4 runs. |
| [ssrf-5-configurations.csv](results/ssrf-5-configurations.csv) | Five SSRF product/version configurations. |

These tables cover different campaigns and configurations. Their counts are not a combined total of unique targets.

## Reading the results

Start with the matrix and follow its file references to inspect the supporting evidence. Paths in the matrix and CSV tables are relative to this repository root.

**Tier labels:** The thesis uses Tiers 1, 2 and 3. The `Tier 4` value retained in the matrix is a catalogue label used only for documentary cases, not a fourth tier in the thesis. Documentary cases also occur under Tier 3; the `Status` and `E2E verdict` columns indicate whether a case was executed.

CWE associations are indicative classifications. Documentary cases were not executed in the laboratory. Inconclusive results and probe errors do not establish either a vulnerability or security.

## Language and original evidence

The matrix, CSV tables and file names are in English. Original reports are preserved unchanged and may contain Italian messages or historical execution paths.
