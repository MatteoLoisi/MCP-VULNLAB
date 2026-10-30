# Probes on popular open-source MCP servers

Request: apply the probes to 30–50 popular open-source MCP servers.

## Selection

Criteria fixed before looking at any finding: package present in the npm or PyPI registry, open-source licence, no mandatory credential or external service, at least 300 weekly downloads on the catalogue date (29 October 2026). The ten DVMCP challenges stay a separate synthetic group; the nine servers already in the probe battery (four reference servers, fetch, git, time, commands, desktop-commander) are not counted among the new ones.

| Stage | Servers | Source |
| --- | ---: | --- |
| Candidates | 44 | [candidates.csv](candidates.csv) |
| Selected | 31 | `selected` column |
| Installed | 29 | `installed` column |
| Started | 23 | [handshake.csv](handshake.csv) |
| Probed (handshake and complete listings) | **22** | [probe_results.csv](probe_results.csv) |

22 new servers plus the 9 of the battery give **31 servers probed**. Why the others stop: two installations timed out on PyPI (`mcp-server-motherduck`, `excel-mcp-server`); three packages are broken by an unpinned `mcp` SDK 2.x dependency (`mcp-server-sqlite`, `mcp-server-duckdb`, `mcp-server-tree-sitter`); two fail because of the isolation choices (`mcp-server-sqlite-npx` needs a native binding skipped by `--ignore-scripts`, `mcp-server-qdrant` downloads a model at start-up without network); one needs a Docker socket (`mcp-server-docker`); one declares a capability and answers `Method not found` (`@tokenizin/mcp-npx-fetch`). Three servers write non-JSON-RPC text on stdout at start-up (`mcp-server-code-runner`, `@agent-infra/mcp-server-filesystem`, `@sylphlab/pdf-reader-mcp`).

## Method

One container per server, without network, read-only root, a synthetic canary outside the exposed folder and a loopback collaborator for SSRF. Black-box profile with the opt-in probes P6, P13 and P14; P5 with up to 4096 attempts. P0 and P7 are excluded (they need network for Inspector and the judge model), P12 does not apply to STDIO: 13 probes per server, 286 positions.

## Results (single campaign, 29–30 October)

| Status | Positions |
| --- | ---: |
| info | 137 |
| secure | 111 |
| skipped | 32 |
| error | 4 |
| **vulnerable** | **2** |

- **`markitdown-mcp` 0.0.1a8:** P5 reads the canary outside the exposed folder through `convert_to_markdown(uri=file://…)`, and P6 reaches the internal collaborator through the same tool (SSRF). Both are stable in three separate repetitions ([markitdown_repetitions.csv](markitdown_repetitions.csv)). The tool description and the package documentation declare that the tool accepts any `http:`, `https:`, `file:` or `data:` URI and can read any file the server's user can access: this is a documented risky capability, relevant when an agent exposed to untrusted content drives the tool, not a hidden vulnerability.
- **The four `error` positions** are all P5: on `server-puppeteer` and `mcp-webresearch` the browser is missing in the container; on `mcp-mermaid` P5 tried conventional argument names on a tool without a path argument; on `pdf-reader-mcp` the path sits in a nested field (`sources[].path`) that this engine version did not probe.
- P2 finds no hostile instruction in any of the 22 servers' descriptions. A server without `vulnerable` positions is not certified secure: no effect was observed on the surface reached.
- A full battery takes 9–37 s per server (median 31 s); the outlier is `@executeautomation/playwright-mcp-server` (922 s, P5 timeouts).

## Files

| File | Content |
| --- | --- |
| [candidates.csv](candidates.csv) | The 44 candidates with category, licence, repository, weekly downloads and the selection funnel |
| [handshake.csv](handshake.csv) | Start-up result, protocol revision and listing counts for each installed server |
| [probe_results.csv](probe_results.csv) | One row per server × probe: finding, status, evidence, duration |
| [run_times.csv](run_times.csv) | Wall time of each server's battery |
| [markitdown_repetitions.csv](markitdown_repetitions.csv) | The three repetitions of the two `markitdown-mcp` effects |

## Limitations

One replica for positions without an effect; black-box only. After this campaign the engine was improved (30 October): P5 no longer invents argument names on complete schemas and probes nested fields, P6 fills the other required arguments. These results were produced with the engine as it was on 29–30 October and were not re-run.
