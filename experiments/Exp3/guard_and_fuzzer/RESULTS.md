# Exp3 — MCP Guard and MCP Fuzzer (2026-10-20)

## Scope

Separate check of the two tools suggested to close the Exp3 gaps:

- MCP Guard 2.0.0 for static indicators and dynamic P4/P5 proof in a sandbox;
- MCP Server Fuzzer 0.7.0 for protocol and tool robustness (P14).

This campaign does not change the P2 ground truth or the MCTS effects already recorded. A static finding or an input accepted by the fuzzer is not a demonstrated vulnerability.

## Pins and environment

| Component | Pin |
|---|---|
| MCP Guard | `e782de38d98219047c4dab3232395d8636c678c3`, version 2.0.0 |
| MCP Server Fuzzer | `mcp-fuzzer==0.7.0`, commit `98c5d388f4edba9744bebb1f2dc72e2b537ee3b9` |
| DVMCP | commit `79734c19f5104cd11486c90926d245560f53befa` |
| Python | 3.13.3 |
| Docker | 29.9.1 |
| Node/npm | 24.18.0 / 12.0.1 |
| uv | 0.12.1 |

Targets c3/c8/c9 copied from DVMCP and verified against `environment/dvmcp-sources.json` (not included in this package):

| Target | `server.py` SHA-256 | Expected role |
|---|---|---|
| c3 | `dc16ad40aeb20c33d222c5461080be1745cce0a859efd8701a1c9529db15d633` | P5 path traversal |
| c8 | `21ae92e1c04121b2d20aada88af2f988da485a7c73f60f22c0bb7e96e431778b` | P4 RCE |
| c9 | `81cdc1a6632844400ac7f9c969fe299b5e66e4bc300eb592392a83da0534b76b` | P4 command injection |

## MCP Guard — static analysis

Clean scan of `server.py` only, without the adapter:

| Target | Findings | Severity | Sink |
|---|---:|---|---|
| c3 | 2 | high | `server.py:94`, `server.py:99` — `open()` with a controllable filename |
| c8 | 3 | 1 critical, 2 high | `server.py:110` shell taint; `server.py:24` hardcoded credential; `server.py:140` path taint |
| c9 | 5 | 4 critical, 1 high | `server.py:55`, `88`, `127`, `189` shell taint; `server.py:230` path taint |

The same targets including the adapter produce an additional `MCPG-PY-PATH-TAINT` finding on `dvmcp_stdio.py:151`; it is a false positive caused by the adapter (`__file__` used to load the module), so it is excluded from the clean counts.

The c8 credential is the example AWS key `AKIAIOSFODNN7EXAMPLE`: it is a hardcoded secret, not a verified real credential.

The shell sinks of c8/c9 and the filesystem sinks of c3 are consistent with the P4/P5 effects already observed, but they remain static indicators.

## MCP Guard — dynamic proof

### Official Docker sandbox: incomplete

Attempt on c8:

```text
mcp-guard targets/c8 --allow-execute --sandbox docker \
  --entrypoint "python dvmcp_stdio.py ." --no-static --no-deps \
  --probe-budget 20 --handshake-timeout 15 --timeout 60 \
  --format json --fail-on none
```

Outcome: `dynamic.ran=false`, handshake failed after 93.5 s. The generated docker command contains:

```text
C:\Users\ML\AppData\Local\Temp\mcpguard-venvs\...\Scripts\python.exe dvmcp_stdio.py .
```

inside `python:3.11-alpine`. The dependencies were installed in the Windows venv, not in the container. The host interpreter path does not exist in the container, so the target does not start. The official Docker sandbox remains `incomplete`; c3 and c9 were not retried in that mode.

### Proof in an external sandbox: complete

To run the probes without exposing the host, an external container was built
with MCP Guard 2.0.0 pinned (`guard-container/Dockerfile`, `python:3.13-slim`;
the Dockerfile is not included in this package). The container uses
`--network none`, `--read-only`, `/tmp` tmpfs, user 1000, cap drop and
`no-new-privileges`. Inside the container Guard uses `--sandbox none`:
isolation is provided by the external container, not by the official Docker
sandbox. This distinction is mandatory in the report: the native Docker mode
remains `incomplete`.

| Target | Probes | Findings | Outcome |
|---|---:|---:|---|
| c3 | 8 | 1 high | `read_file` reads a canary file in `/tmp` outside the root: P5 path traversal demonstrated. |
| c8 | 8 | 1 critical, 1 high | `execute_shell_command` runs the shell canary (P4); `analyze_log_file` reads a canary in `/tmp` (P5). |
| c9 | 8 | 0 | No finding: Guard does not demonstrate the injection on `ping_host`. This is a false negative with respect to the harness, not a "secure" outcome. |

There are 8 probes per target and the handshake succeeded (`server_info.is_mcp_server=true`).
The `dependencies` stage fails on the read-only `/.cache`, but the dynamic venv
installs `mcp==1.28.0` from the local wheels (`python_install_mode=wheels-only`)
and the target starts. The JSON files are in `guard-container/*-dynamic.json`.

## Local Docker harness for P4/P5

So that the effects are not left uncovered, a separate harness, not MCP Guard,
was used: an MCP stdio client (`harness/effect_probe.py`) and the target in the
same `python:3.13-slim` container with `mcp==1.28.0`. The container uses
`--network none`, `--read-only`, `/tmp` tmpfs, user 1000, cap drop and
memory/PID limits. The images are built from the `harness/Dockerfile.*`
Dockerfiles (the harness sources are not included in this package).

| Target | P | Tool and input | Observed effect |
|---|---|---|---|
| c3 | P5 | `read_file` with `../../tmp/dvmcp_challenge3/private/system_credentials.txt` | Returns `SYSTEM CREDENTIALS - HIGHLY RESTRICTED` and the private API key: arbitrary read outside the public directory. |
| c8 | P4 | `execute_shell_command` with `echo MCPGUARD_DYNAMIC_C8_20261020` | Output `MCPGUARD_DYNAMIC_C8_20261020`: shell command executed. |
| c9 | P4 | `ping_host` with host `127.0.0.1; echo MCPGUARD_DYNAMIC_C9_20261020`, count 1 | Output `MCPGUARD_DYNAMIC_C9_20261020`: injection executed. `ping` is not present in the image, but the command injected after `;` is executed anyway. |

All three have `isError=false` and return the expected marker/effect.
These rows are `demonstrated_effect` and reproduce in a container the effects
already recorded by MCTS; they do not change the P2 ground truth and are not an
MCP Guard outcome. Guard's official Docker sandbox remains `incomplete`; the
Guard proof in the external sandbox is separate and missed c9.

Images: c3 `sha256:f4f7d40c...`, c8 `sha256:5e46ca10...`, c9
`sha256:b501917f...` (the full IDs are recorded in `raw/docker-effect/verification.json`, which is not included in this package).

## MCP Server Fuzzer — pilot c2, R1, R2, R3

Targets c2 (DVMCP challenge 2), R1-filesystem, R2-memory and R3-sequential-thinking (`@modelcontextprotocol/*@1.0.0`), stdio, `--enable-safety-system --no-network`, fs-root in temp:

| Target | Mode | Runs | Tools | Findings | Crashes | Exceptions | Safety blocked |
|---|---|---:|---:|---:|---:|---:|---:|
| c2 | protocol, aggressive | 53 | 0 | 23 accepted malformed | 0 | 0 | 0 |
| c2 | tools, realistic | 4 | 2 | 4 accepted malformed | 0 | 0 | 0 |
| R2-memory | tools, realistic | 18 | 9 | 14 accepted malformed, 5 injection reflection | 0 | 0 | 0 |
| R1-filesystem | tools, realistic | 28 | 14 | 4 accepted malformed, 1 injection reflection | 0 | 0 | 0 |
| R3-sequential-thinking | tools, realistic | 2 | 1 | 1 injection reflection | 0 | 0 | 0 |

The roots client of MCP Fuzzer 0.7.0 replaces the directories passed to the
server with its own working directory. The valid R1 run was therefore
executed with cwd `C:/Users/ML/AppData/Local/Temp/mcptargets/fuzz-r1-root`;
the first runs, which saw the project as root, are in
`raw/mcp-fuzzer/superseded/` (not included in this package) and are not used as evidence.

c2 detail: `get_company_data` 2/2 and `search_company_database` 2/2 with input `' OR '1'='1`; the server answers without error and reflects the string. There is no database access and no difference in results that would prove SQL injection: it is a case of accepted malformed input, not a vulnerability.

R2 detail: 9 tools, 18 runs. The `<script>` marker is stored and returned by `read_graph`/`create_entities`; the server has no HTML UI, so this is not XSS. The 5 `injection_reflection` findings are a fuzzer heuristic, not a proof of execution. The "handled correctly" rate is 16.7% and is not a vulnerability rate.

R1 detail: 14 tools, 28 runs, 5 findings, 0 crashes: 2 `accepted_malformed` on `read_multiple_files` with invalid paths (access denied/ENOENT), 2 on `list_allowed_directories` with ignored extra fields and 1 `injection_reflection` on `read_multiple_files` (`<script>` marker reflected in an error, no HTML UI). The run uses `fuzz-r1-root` as cwd/root; no project file is within the perimeter.

R3 detail: 1 tool, 2 runs, 1 `injection_reflection`: `sequentialthinking` returns the reasoning text, which is the expected behaviour for a sequential-thinking server; 0 crashes.

R1 triage: the direct call to `list_allowed_directories` returns only
the configured directory, with no file content. The MCP-Shield flag
`Sensitive file access: access file` is therefore a heuristic false positive,
not a sensitive access; it is a `negative_check`, not an independent
review. Detail in `raw/r1-triage/list-allowed.json`.

The fuzzer labelled the results as "Vulnerabilities Found", but the observed categories are `accepted_malformed` and `injection_reflection`: in the report they must be treated as P14 robustness, not as proof of exploitation. R1, R2 and R3, clean reference targets, still produce findings (reflections and accepted inputs): this shows the high sensitivity and aggressive labelling of the tool. The pilot covers 4 targets, not the 14 of the sweep.

## Interpretation

- Static MCP Guard adds indicators consistent with P4 (c8/c9) and P5 (c3); it is not a dynamic proof.
- The official Docker sandbox of MCP Guard remains `incomplete` because of the host venv bug.
- The MCP Guard proof in an external container demonstrates P5 on c3 and P4+P5 on c8, and does not find c9: 0 findings does not mean "secure". The sandbox used is external, not the official Docker one.
- The separate Docker harness demonstrates the P5 (c3) and P4 (c8/c9) effects, but it is not MCP Guard and must not be presented as a proof by the scanner; on c9 it compensates for Guard's false negative.
- The fuzzer on c2/R1/R2/R3 produces no crashes, exceptions or safety blocks; it produces accepted malformed inputs and reflections. On the clean R1/R2/R3 the reflections are expected and are not XSS.
- These tools do not replace the MCTS effects or the P2 ground truth; no precision/recall metric should be computed from this campaign.

## Delivered data

[Results by tool, target and mode](results.csv).
[Index of the original reports and logs](evidence.csv). Paths in the cells
are relative to the experiments folder; the original members give the
provenance. Guard's JSON files are in the raw and guard-container folders.
The local harness evidence remains distinct from Guard's outcomes.
The fuzzer results concern robustness and reflections, not confirmed
exploits. The sources and execution scripts are not included in the package.
