# Exp3 pilot — RedCell, Mcpwn and local probes

Run on 19 October 2026. The evidence shows that there are external scanners
with active checks comparable to P4/P5/P6. This pilot does not replace the
earlier Exp3 campaign and does not settle the comparison of all capabilities.

## How they work and what data they produce

The scanners are MCP clients: they start the server under examination and
communicate with it over STDIO, i.e. JSON-RPC requests and responses through
the process streams. They enumerate the tools and their arguments; then they
send test inputs and analyse the responses. No LLM is involved in this pilot.

**RedCell** combines indicators in the descriptions with active proofs. For
reads it creates a canary file with unpredictable content and checks whether
the tool returns it; for commands it looks for the product of two random
numbers; for SSRF it waits for an HTTP request carrying a unique identifier on
a local endpoint. A dynamic finding can include the `witness` field, with
requests/responses, arguments, output and the criterion that recognises the
effect. Indicators in the descriptions do not demonstrate these effects.

**Mcpwn** sends payloads to the tool arguments and looks for signatures in the
responses, such as the identity of the executing user. It also records
protocol, prompt and timing indicators. Each finding keeps tool, argument,
payload, category, severity, matches found and timestamp. Its report does not
contain the same JSON-RPC certificate as RedCell. The quick, RCE-only and
safe-mode profiles limit the checks actually performed.

The JSON files and logs are the original outputs of the tools. The following
CSVs are summaries derived from those outputs: they are not new executions.

## Main results

- **RedCell:** ten DVMCP challenges, 290 probe calls, 15 findings on seven
  challenges. Ten findings have dynamic evidence; five are heuristic
  indicators in the tool descriptions.
- **Mcpwn:** command execution detected on c8 and c9. The complete safe-mode
  scan of c3 does not detect the arbitrary file read demonstrated by RedCell
  and P5. Instead it produces 68 findings of other categories, 51 of them
  timing-based. They are not 68 distinct confirmed vulnerabilities.
- **Direct local comparison:** P5 and RedCell read a canary on c3; P4/P5 and
  RedCell observe execution and a read on c8; P4, RedCell and Mcpwn observe
  execution on c9.
- **SSRF:** RedCell and P6 reach the loopback collaborator through the same
  fetch server, in the same local image.
- **Controls:** RedCell produces six findings on its own vulnerable example
  and zero on the defended one. Mcpwn, in the quick RCE profile, produces one
  and zero respectively on the same examples. The counts of the two scanners
  refer to different selections.

**26 attempts are kept: 22 sessions completed within the selected scope and
four technical errors**, including the attempts that were later repeated. A
completed session does not amount to complete coverage of the target or of
every probe.

## RedCell on the ten challenges

| Target | Native findings | Observation |
|---|---:|---|
| c1 | 0 | No finding on the tools examined |
| c2 | 2 | Suspicious instructions in the descriptions of two tools |
| c3 | 1 | `read_file` returns the canary outside the public directory |
| c4 | 0 | No finding on the tools examined |
| c5 | 1 | Suspicious description of `enhanced_calculate` |
| c6 | 2 | `read_document` and `read_upload` return `/etc/passwd` through traversal |
| c7 | 0 | No finding on the tools examined |
| c8 | 2 | Evaluation of a shell expression and canary read through `analyze_log_file` |
| c9 | 4 | Shell expressions evaluated through `ping_host`, `traceroute`, `port_scan`, `network_diagnostic` |
| c10 | 3 | Two suspicious descriptions and canary read through `analyze_log_file` |
| **Total** | **15** | **Seven challenges with at least one finding** |

The read proofs include unpredictable canaries and, on c6, signatures of the
system file. RedCell's execution oracle requires the product of two random
numbers, which is not present in the payload as a precomputed result.
The certificates contain JSON-RPC requests and responses.

Execution observed on a tool that explicitly offers a shell, such as the one
of c8, demonstrates an exposed capability; on its own it does not demonstrate
an authorisation bypass. Likewise, for tools that freely read logs,
attributing an isolation violation requires a declared access boundary.
For c3 that boundary is the public directory of the challenge; on c6 the
payload leaves the document and upload directories.

On c9 some basic diagnostic commands are missing from the image or fail.
The appended shell expression is evaluated anyway. These proofs do not
certify that the diagnostic commands work, nor a privilege escalation: the
container uses user 1001.

The outcomes without findings for c1/c4/c7 do not mean that those servers
are secure. Here RedCell enumerates and calls tools: it does not perform the
full equivalent of P11/P15 on resource contents or on token exposure.

## Mcpwn: results and profiles

| Selected session | Profile | Native report | Interpretation |
|---|---|---|---|
| c8, `mcpwn-c8-retry30` | safe-mode, RCE-only, timeout 30 s | 10 findings: 6 RCE, 2 blind timing, 2 timing | Multiple results on the same tool; not ten independent vulnerabilities |
| c9, `mcpwn-c9-quick` | safe-mode, quick, RCE-only | 1 RCE on `ping_host` | Output of `id` for the container user |
| c3, `mcpwn-c3-full` | safe-mode, timeout 15 s | 68: 1 state reset, 10 capability, 51 timing, 6 prompt indicators | No FILE_READ/path traversal detection in the report |
| vulnerable example | safe-mode, quick, RCE-only | 1 RCE | Positive control of the RCE profile only |
| defended example | safe-mode, quick, RCE-only | 0 | Negative control of the RCE profile only |

The extended scan of c9 ended with a **240 s timeout**, without a final
report. The log is still available; the result selected for c9 is the
complete quick pilot. The first quick start of c8 failed discovery, but
Mcpwn returned exit code 0 and an empty report: that attempt is classified
as an error, not as a secure server.

In the pinned Mcpwn revision, the ordinary selection of argument payloads
favours `command_injection`; the separate traversal check operates on
resources. The read through a filename argument of c3 therefore did not get
a detection equivalent to P5 in this execution. The prompt-injection
indicators are suspicious textual responses: no model was run to demonstrate
that it would follow them. The timing and protocol signals require separate
validation.

## Replicas of the local probes

| Target | Local probe | Observed result |
|---|---|---|
| c3 | P5 | Canary returned by `read_file`; stopped after the first positive read, incomplete sweep |
| c8 | P4 | Marker executed through `execute_shell_command.command` |
| c8 | P5 | Canary returned by `analyze_log_file`; native coverage 13/13 units |
| c9 | P4 | Marker executed through `ping_host.host` |
| c9 | P5 | No confirmed read; incomplete sweep, cap of 60 attempts, 18/25 units |
| local fetch | P6 | Collaborator token returned by `fetch`; 1/1 unit |

P2 was included in the three DVMCP replicas c3/c8/c9 and found no suspicious
instructions in those descriptions. It was not repeated here on the ten
targets: the earlier P2 outcomes of c2/c5/c10 belong to the old campaign and
are not added to the new measurements.

These replicas use the native CLI `real_target_check.py`, with the transcript
kept and the default P4 marker `V2P4MARKER`. The `exp3-probe-worker-v1`
contract of the earlier campaign is not attributed to them. A separate check
matched the responses to their request IDs and found the c3/c8 canaries in
the tool responses.

## SSRF: what was actually tested

RedCell received an HTTP callback with a nonce from `fetch` on
`127.0.0.1:<port>`. P6, in the separate replica of the same target, obtained
the token of its own internal endpoint. Both scanner/target process pairs
share the network namespace of their respective container.

The external network is disabled. No cloud metadata, third-party services or
host network were contacted. The statement in RedCell's native report that
the server would also reach `169.254.169.254` is a generalisation by the
tool, **not a measurement of this pilot**.

The target is the module present in the local image
`mcp-probe-fetch:20261001`, identified by image ID and source hashes in
`environment/fetch-environment.json`. Its identity with a specific upstream
release was not verified. The matrix of the five SSRF targets originally
excluded from Exp3 was not completed in this pilot (it was run later, see
[../../v4/ssrf-scanners/](../../v4/ssrf-scanners/README.md)).

## Environment, controls and limitations

- RedCell `0.1.0`, commit `c033c02e80912a1e74490f3d388b666ebb66275c`.
- Mcpwn `1.0`, commit `2efd0273e71ccd285f398221189f59c5e4078feb`.
- DVMCP commit `79734c19f5104cd11486c90926d245560f53befa`, `server.py`
  variant, with the STDIO shim already present and unchanged.
- Linux runtime, Python 3.11.16, MCP SDK 1.29.1. Unprivileged containers,
  read-only base filesystem and sources, temporary directories, no external
  network, CPU/memory/process limits.
- The **14 pieces of evidence with a RedCell certificate** passed the
  project's own offline verifier: ten DVMCP, three from the controls and one
  SSRF. The eight heuristic findings remain without that proof. The
  verification checks the consistency of the certificate; it is neither a
  blind human review nor an independent replay of the attack.

No general precision, recall or FPR is estimated. Raw counts of scanners
with different categories, oracles and profiles are not compared. Each case
has a single useful execution, in addition to the technical attempts kept;
statistical stability and chains with LLMs were not measured. The Linux
campaign remains separate from the earlier Windows measurements.

Snyk Agent Scan was not run: its analysis protocol uses a remote API. This
is a scope choice of the local pilot, not a claim that the tool cannot
analyse these metadata. No new extended SAST/fuzzing analyses of MCTS and no
automatic comparison with MCP Inspector were performed.

Still to be planned: the complete comparison with equal classes and targets,
replicas, validation of the Mcpwn findings, coverage of all SSRF targets and
a possible remote comparison of the metadata. The new measurements were not
added automatically to the existing Excel matrix.

## Files to consult

| File | Content |
|---|---|
| [redcell_results.csv](redcell_results.csv) | Ten DVMCP targets: findings, dynamic proofs, indicators and original report |
| [mcpwn_results.csv](mcpwn_results.csv) | Five useful sessions: profile and counts by type, including the controls |
| [attempts.csv](attempts.csv) | All 26 attempts, including four technical errors; references to the reports |
| [versions.csv](versions.csv) | Versions and commits of the tools and the target |
| [runs](runs/) | JSON reports, HTML where produced, logs, start conditions and status of each attempt |
| [environment](environment/) | Runtime and identification of the local images |

Paths in the CSV cells are relative to the experiments folder.
The native reports keep their own original paths and texts. Severity and
confidence are judgements of the tools, not an independent certification.
The four technical errors must not be counted as negative outcomes. The HTML
files kept are those produced by RedCell; in the pilot Mcpwn produced JSON.

This pilot of 19 October remains distinct from the main table on the
14 targets and from the three inconclusive historical probes. It does not
retroactively resolve those acquisitions. The pilot results are in the CSVs
of this folder; the main Excel workbook reports the historical comparison.

Primary documentation of the revisions used:
[RedCell](https://github.com/Sahilo6/redcell/blob/c033c02e80912a1e74490f3d388b666ebb66275c/README.md),
[Mcpwn](https://github.com/Teycir/Mcpwn/blob/2efd0273e71ccd285f398221189f59c5e4078feb/README.md).
