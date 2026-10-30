# P5 on Filesystem: complete sweep, 25 October 2026

P5 tests access outside the allowed directory through tool arguments. A synthetic canary file is placed outside the allowed root, then every path-like argument of every tool is called with traversal inputs. The finding is `vulnerable` only if the canary content comes back.

## Target

| Field | Value |
| --- | --- |
| Package | `@modelcontextprotocol/server-filesystem` |
| Version | `1.0.0`, pinned in the command and reported by the server (`target_identity.version`) |
| npm integrity | `sha512-2fN6qhpsiPSBAe0ev1qDPk4sCWdULCZQTuYc4ZY7+BDWQ1XCxLB+7dqStF2ZflgG4G/9vTwQsjxh7pjFqrrpcg==` |
| Tarball | <https://registry.npmjs.org/@modelcontextprotocol/server-filesystem/-/server-filesystem-1.0.0.tgz> |
| Command | `npx -y @modelcontextprotocol/server-filesystem@1.0.0 <allowed root>` |
| Transport | STDIO |

## Result

| Measure | Value |
| --- | --- |
| Finding | **secure**: no canary content returned |
| Planned / covered / missing units | **17 / 17 / 0** (tool–argument combinations) |
| Calls to the target | 187 (declared limit 4096; sweep complete) |
| Tool refusals (`tool_error`) | 166 |
| Delivered without effect | 21 (18 on `list_allowed_directories`, 3 on `read_multiple_files`) |
| Undelivered payloads / transport errors | 0 / 0 |

The 17 units are: `directory_tree` (`excludePatterns`, `path`), `get_file_info` (`path`), `list_allowed_directories` (`directory`, `file`, `filename`, `name`, `path`, `uri`), `list_directory` (`path`), `list_directory_with_sizes` (`path`), `read_file` (`path`), `read_media_file` (`path`), `read_multiple_files` (`paths`), `read_text_file` (`path`), `search_files` (`excludePatterns`, `path`).

## Limits

- `list_allowed_directories` declares no input properties: P5 uses conventional argument names, which the tool ignores. This is opaque coverage, not a defence.
- `read_multiple_files` returns the refusal as text with `isError=false`: it is counted as delivered and refused, not as an effect.
- A `secure` result applies to this protocol and this canary, not to every possible path of the server.

## Files

| File | Content |
| --- | --- |
| [original_report.md](original_report.md) | Original sweep report (translated from Italian) |
| [audit/filesystem_p5.csv](audit/filesystem_p5.csv) | One row per call: arguments, response, error, status, hash |
| [audit/audit.json](audit/audit.json) | Sweep summary |
| [probes/battery-npm-filesystem.json](probes/battery-npm-filesystem.json) | Probe report |
| [probes/attempts/acd01232676f408b92b65422fbbdd0fa/](probes/attempts/acd01232676f408b92b65422fbbdd0fa/report.json) | Attempt context, report, status and full protocol capture |

Local paths in the original files refer to the machine that ran the sweep.
