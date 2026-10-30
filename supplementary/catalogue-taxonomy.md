# Scenario catalog: taxonomy and security references

This note groups the 136 scenarios of
[`results/catalogue-136.csv`](../results/catalogue-136.csv) into four risk classes
plus the control cases, and lists for each scenario its tier, CWE weaknesses,
MITRE ATLAS techniques (release v2026.09), OWASP MCP Top 10:2025 and OWASP LLM
Top 10:2026 categories, and the outcome of the catalog run.

A reference expresses an interpretive correspondence between the scenario and the
external definition; `—` means that no sufficiently specific relationship was
identified. Tier 4 is the catalog label for documentation-only cases.

## Taxonomy by tier

| Class | T1 | T2 | T3 | T4 | Total | Executable |
|---|---:|---:|---:|---:|---:|---:|
| Control and reference cases | 2 | 0 | 0 | 0 | 2 | 2 |
| Injection and manipulation of model context | 13 | 14 | 5 | 2 | 34 | 31 |
| Misconfiguration, authentication and transport | 13 | 17 | 2 | 1 | 33 | 32 |
| Privilege, access control and execution | 11 | 17 | 6 | 3 | 37 | 32 |
| Data exposure, audit and supply chain | 8 | 10 | 6 | 6 | 30 | 23 |
| **Total** | 47 | 58 | 19 | 12 | **136** | 120 |

## Coverage of the security references

| Reference | Scenarios with a link | Share |
|---|---:|---:|
| CWE | 129 / 136 | 95% |
| MITRE ATLAS | 29 / 136 | 21% |
| OWASP MCP Top 10:2025 | 120 / 136 | 88% |
| OWASP LLM Top 10:2026 | 34 / 136 | 25% |

## Scenarios

CWE: primary weakness first, additional ones after the semicolon.

### Control and reference cases (2)

| ID | Scenario | Tier | CWE | MITRE ATLAS | OWASP MCP | OWASP LLM | Outcome |
|---|---|:-:|---|---|---|---|---|
| `TCMCP--1` | Infrastructure baseline | 1 | — | — | — | — | baseline_ok |
| `TCMCP-000` | Legitimate exfiltration (control) | 1 | — | — | — | — | legit_ok |

### Injection and manipulation of model context (34)

| ID | Scenario | Tier | CWE | MITRE ATLAS | OWASP MCP | OWASP LLM | Outcome |
|---|---|:-:|---|---|---|---|---|
| `TCMCP-001` | Tool poisoning via description | 1 | CWE-1427 | AML.T0110.000 | MCP03 | LLM01 | vulnerable |
| `TCMCP-002` | Tool poisoning via tool name | 1 | CWE-1427 | AML.T0110.000 | MCP03 | LLM01 | vulnerable |
| `TCMCP-003` | Direct prompt injection | 1 | CWE-1427 | AML.T0051.000 | MCP06 | LLM01 | vulnerable |
| `TCMCP-004` | Tool argument poisoning | 1 | CWE-1427 | — | MCP03 | LLM01 | vulnerable |
| `TCMCP-005` | Tool result poisoning | 1 | CWE-1427 | AML.T0110.002 | MCP03 | LLM01 | vulnerable |
| `TCMCP-006` | Chain poisoning (multi-tool) | 1 | CWE-1427; CWE-841 | AML.T0051.001 | MCP03 | LLM01 | vulnerable |
| `TCMCP-007` | Indirect PI via file | 1 | CWE-1427 | AML.T0099, AML.T0051.001 | MCP06 | LLM01 | vulnerable |
| `TCMCP-008` | Indirect PI via database | 1 | CWE-1427 | AML.T0099, AML.T0051.001 | MCP06 | LLM01 | vulnerable |
| `TCMCP-009` | Unicode invisible characters | 1 | CWE-1427; CWE-176 | AML.T0110.000, AML.T0068 | MCP03 | LLM01 | vulnerable |
| `TCMCP-010` | Agentic flow demo | 1 | — | — | — | — | agentic_ok |
| `TCMCP-011` | Base64 hidden instructions | 1 | CWE-1427 | AML.T0110.000, AML.T0068 | MCP03 | LLM01 | vulnerable |
| `TCMCP-012` | Whitespace obfuscated injection | 1 | CWE-1427 | AML.T0110.000, AML.T0068 | MCP03 | LLM01 | vulnerable |
| `TCMCP-013` | Rug pull — behavioral change | 2 | CWE-754; CWE-841 | AML.T0110.001 | MCP03 | LLM01 | vulnerable |
| `TCMCP-014` | Rug pull — metadata update | 2 | CWE-754; CWE-841 | AML.T0110.000 | MCP03 | LLM01 | vulnerable |
| `TCMCP-015` | Multi-tool chain poisoning | 2 | CWE-1427; CWE-841 | AML.T0051.001 | MCP03 | LLM01 | vulnerable |
| `TCMCP-016` | Shadow tool via name collision | 2 | CWE-706 | AML.T0110.001 | MCP03 | — | vulnerable |
| `TCMCP-017` | Cross-server conflict injection | 2 | CWE-706; CWE-436 | AML.T0110.001 | MCP03 | — | vulnerable |
| `TCMCP-018` | JSON-RPC batch injection | 2 | CWE-20 | — | MCP07 | — | vulnerable |
| `TCMCP-019` | Notification-based injection | 1 | CWE-1427; CWE-349 | AML.T0051.001 | MCP06 | LLM01 | vulnerable |
| `TCMCP-020` | Shadow server (fake server) | 2 | CWE-290 | AML.T0110.000 | MCP09 | — | vulnerable |
| `TCMCP-021` | ETDI signature bypass | 3 | CWE-1427 | AML.T0110.000 | MCP03 | — | vulnerable |
| `TCMCP-022` | Typosquatting server name | 3 | — | — | MCP09 | LLM04 | vulnerable |
| `TCMCP-023` | Marketplace poisoning | 3 | CWE-829 | AML.T0115.002, AML.T0010.005 | MCP04 | LLM04 | vulnerable |
| `TCMCP-024` | Zero-click mcp.json (CVE-2026-30615) | 4 | CWE-77 | — | MCP06, MCP09 | LLM01 | documented-only |
| `TCMCP-204-TIER2` | Command injection realistic — network_ping | 2 | CWE-78; CWE-77 | — | MCP05 | — | vulnerable |
| `TCMCP-205-TIER2` | Command injection realistic — check_port | 2 | CWE-78; CWE-77 | — | MCP05 | — | vulnerable |
| `TCMCP-206-TIER2` | Command injection realistic — fetch_system_info | 2 | CWE-78; CWE-77 | — | MCP05 | — | vulnerable |
| `TCMCP-207-TIER2` | Command injection realistic — read_log_file | 2 | CWE-78; CWE-77, CWE-22 | — | MCP05 | — | vulnerable |
| `TCMCP-208-TIER2` | Command injection realistic — execute_command | 2 | CWE-749; CWE-78 | — | MCP05 | — | vulnerable |
| `TCMCP-323` | Spear phishing via AI agent | 4 | — | AML.T0052 | MCP04 | — | documented-only |
| `NEW-TCMCP-001` | Elicitation credential phishing | 2 | CWE-522; CWE-359 | — | MCP01 | — | vulnerable |
| `NEW-TCMCP-007` | TAG-block approval gap | 2 | CWE-1427; CWE-176 | AML.T0110.000, AML.T0068 | MCP03 | LLM01 | vulnerable |
| `NEW-TCMCP-008` | Phantom task injection (doc only) | 3 | CWE-441; CWE-287 | — | MCP06 | — | documented-only |
| `NEW-TCMCP-025` | Post-approval prompt mismatch | 3 | CWE-1427; CWE-841 | — | MCP06 | LLM01 | vulnerable |

### Misconfiguration, authentication and transport (33)

| ID | Scenario | Tier | CWE | MITRE ATLAS | OWASP MCP | OWASP LLM | Outcome |
|---|---|:-:|---|---|---|---|---|
| `TCMCP-100` | No authentication on tool | 1 | CWE-287; CWE-306 | — | MCP07 | — | vulnerable |
| `TCMCP-101` | Excessive permissions | 1 | CWE-269; CWE-272 | — | MCP02 | LLM03 | vulnerable |
| `TCMCP-102` | Missing rate limiting | 1 | CWE-770; CWE-799 | — | — | — | vulnerable |
| `TCMCP-103` | Insecure infrastructure | 1 | CWE-1188; CWE-942 | — | MCP01 | — | vulnerable |
| `TCMCP-104` | Neighborjack (0.0.0.0 bind) | 1 | CWE-1327 | — | MCP07 | — | vulnerable |
| `TCMCP-105` | SSL/TLS disabled | 1 | CWE-319; CWE-311 | — | MCP01 | — | vulnerable |
| `TCMCP-106` | Verbose error disclosure | 1 | CWE-209; CWE-200 | — | — | — | vulnerable |
| `TCMCP-107` | Log injection | 1 | CWE-117; CWE-93 | — | MCP08 | — | vulnerable |
| `TCMCP-108` | Missing input validation | 1 | CWE-20; CWE-345 | — | — | — | vulnerable |
| `TCMCP-109` | Default/empty JWT secret | 1 | CWE-1394; CWE-798 | — | MCP01 | — | vulnerable |
| `TCMCP-110` | Unauthenticated SSE stream | 1 | CWE-287; CWE-306 | — | MCP07 | — | vulnerable |
| `TCMCP-111` | Combined Neighborjack and missing authentication | 1 | CWE-1327; CWE-306 | — | MCP07 | — | vulnerable |
| `TCMCP-118` | OAuth PKCE bypass | 2 | CWE-287 | — | MCP07 | — | vulnerable |
| `TCMCP-119` | JWT algorithm confusion | 2 | CWE-327; CWE-290, CWE-347 | — | MCP07 | — | vulnerable |
| `TCMCP-120` | Token replay | 2 | CWE-294 | — | MCP01 | — | vulnerable |
| `TCMCP-121` | Scope escalation | 2 | CWE-269; CWE-284 | — | MCP02 | — | vulnerable |
| `TCMCP-122` | Dynamic Client Registration abuse | 2 | CWE-287; CWE-290 | — | MCP07 | — | vulnerable |
| `TCMCP-123` | Missing Origin validation | 2 | CWE-346; CWE-942 | — | MCP07 | — | vulnerable |
| `TCMCP-124` | Missing audience binding | 2 | CWE-284; CWE-862 | — | MCP07 | — | vulnerable |
| `TCMCP-125` | WebSocket without TLS | 2 | CWE-319; CWE-311 | — | MCP01 | — | vulnerable |
| `TCMCP-126` | DNS rebinding | 3 | CWE-346 | — | MCP07 | — | vulnerable |
| `TCMCP-127` | Registry path traversal | 3 | CWE-22; CWE-73 | — | MCP02 | — | vulnerable |
| `TCMCP-128` | Unverified OAuth issuer (RFC 9207) | 2 | CWE-287; CWE-290 | — | MCP07 | — | vulnerable |
| `TCMCP-131` | Host/Origin not validated by the MCP server (CVE-2026-63118) | 2 | CWE-346; CWE-350 | — | MCP07 | — | vulnerable |
| `TCMCP-322` | BGP/CDN routing attack | 4 | CWE-284; CWE-300 | — | — | — | documented-only |
| `TCMCP-405` | DNS rebinding / network exposure advanced | 1 | CWE-346 | — | MCP07 | — | vulnerable |
| `TCMCP-501` | Open mail relay (send_email no auth) | 2 | CWE-306; CWE-287 | — | MCP07 | — | vulnerable |
| `TCMCP-503` | Information disclosure via headers | 2 | CWE-200 | — | — | — | vulnerable |
| `TCMCP-512` | LLM discovers insecure server | 2 | CWE-306; CWE-200 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-017` | Config swap name-keyed trust (CVE-2025-54136) | 2 | CWE-78; CWE-494, CWE-290, CWE-349 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-020` | Unverified capability attestations | 2 | CWE-345; CWE-863 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-023` | logLevel per-request injection | 2 | CWE-532; CWE-117 | — | MCP08 | — | vulnerable |
| `NEW-TCMCP-026` | Authorization code replay OAuth | 2 | CWE-294; CWE-287 | — | MCP07 | — | vulnerable |

### Privilege, access control and execution (37)

| ID | Scenario | Tier | CWE | MITRE ATLAS | OWASP MCP | OWASP LLM | Outcome |
|---|---|:-:|---|---|---|---|---|
| `TCMCP-024a` | WASI sandbox escape | 4 | CWE-266 | — | MCP02 | — | documented-only |
| `TCMCP-129` | Resource URI path traversal (CVE-2026-7646) | 1 | CWE-22; CWE-73 | — | MCP07 | — | vulnerable |
| `TCMCP-130` | Argument injection in argv (CVE-2026-44968) | 2 | CWE-88 | — | MCP05 | — | vulnerable |
| `TCMCP-132` | Sandbox escape via cwd/symlink (CVE-2026-50548/50549) | 2 | CWE-22; CWE-59 | — | MCP02 | — | vulnerable |
| `TCMCP-133` | Config injection via management API (CVE-2026-23744) | 2 | CWE-306; CWE-78 | — | MCP05 | — | vulnerable |
| `TCMCP-200` | Resource exhaustion | 1 | CWE-770; CWE-400 | — | — | — | vulnerable |
| `TCMCP-201` | Privilege escalation via tool | 1 | CWE-269 | — | MCP02 | — | vulnerable |
| `TCMCP-202` | Undeclared resources | 1 | CWE-285; CWE-668 | — | MCP02 | — | vulnerable |
| `TCMCP-203` | Path traversal (file read) | 1 | CWE-22; CWE-36 | — | MCP02 | — | vulnerable |
| `TCMCP-204` | Command injection (RCE) | 1 | CWE-78; CWE-77 | — | MCP05 | — | vulnerable |
| `TCMCP-205` | SSRF via proxy | 1 | CWE-918; CWE-441 | — | MCP05 | — | vulnerable |
| `TCMCP-207` | IDOR | 1 | CWE-639; CWE-425 | — | MCP07 | — | vulnerable |
| `TCMCP-212` | Privilege escalation chaining | 2 | CWE-269; CWE-841 | — | MCP02 | — | vulnerable |
| `TCMCP-213` | Confused deputy | 2 | CWE-441; CWE-346 | — | MCP02 | — | vulnerable |
| `TCMCP-214` | RBAC bypass | 2 | CWE-285; CWE-862 | — | MCP07 | — | vulnerable |
| `TCMCP-215` | Missing HITL | 2 | CWE-841 | — | MCP07 | LLM03 | vulnerable |
| `TCMCP-216` | Cross-tenant data access | 2 | CWE-269; CWE-653 | — | MCP10 | — | vulnerable |
| `TCMCP-217` | Symlink attack | 3 | CWE-61; CWE-22 | — | MCP02 | — | vulnerable |
| `TCMCP-218` | Cross-tenant isolation bypass | 3 | CWE-269; CWE-668 | — | MCP10 | — | vulnerable |
| `TCMCP-219` | Docker container escape | 4 | CWE-266 | — | MCP02 | — | documented-only |
| `TCMCP-220` | Host network access | 3 | CWE-668; CWE-419 | — | MCP02 | — | vulnerable |
| `TCMCP-221` | Secret injection via env | 3 | CWE-526; CWE-200 | — | MCP01 | — | vulnerable |
| `TCMCP-402` | Excessive agency (over-privileged tools) | 1 | CWE-269; CWE-272 | — | MCP02 | LLM03 | vulnerable |
| `TCMCP-403` | Command injection advanced (evasive) | 1 | CWE-78; CWE-77 | — | MCP05 | — | vulnerable |
| `TCMCP-500` | Exec command (no auth) | 1 | CWE-306 | — | MCP05, MCP07 | — | vulnerable |
| `TCMCP-502` | Arbitrary file write (write_file) | 2 | CWE-22; CWE-73 | — | MCP02 | — | vulnerable |
| `TCMCP-511` | LLM discovers & exploits SSRF proxy | 2 | CWE-918; CWE-441 | — | MCP05 | — | vulnerable |
| `NEW-TCMCP-004` | MRTR requestState tampering (SEP-2322) | 2 | CWE-345; CWE-294 | — | MCP02 | — | vulnerable |
| `NEW-TCMCP-005` | Subscriptions without per-URI authorization (SEP-2575) | 2 | CWE-862; CWE-863 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-006` | TrustFall folder-trust RCE (doc only) | 4 | CWE-829; CWE-78 | — | MCP04 | — | documented-only |
| `NEW-TCMCP-009` | OAuth one-click ATO / cookie injection | 2 | CWE-352; CWE-565, CWE-601 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-010` | CIMD metadata fetch SSRF (SEP-991) | 2 | CWE-918 | — | MCP05 | — | vulnerable |
| `NEW-TCMCP-012` | State handle possession (SEP-2567) | 2 | CWE-488; CWE-294 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-013` | Header routing spoofing (SEP-2243) | 2 | CWE-290; CWE-346 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-014` | Remote JSON Schema $ref → SSRF (doc only) | 3 | CWE-918 | — | MCP05 | — | documented-only |
| `NEW-TCMCP-015` | Circular JSON Schema $ref → DoS (doc only) | 3 | CWE-674; CWE-400 | — | — | — | documented-only |
| `NEW-TCMCP-018` | Ancestor injection (nested tools) | 2 | CWE-269; CWE-862 | — | MCP02 | — | vulnerable |

### Data exposure, audit and supply chain (30)

| ID | Scenario | Tier | CWE | MITRE ATLAS | OWASP MCP | OWASP LLM | Outcome |
|---|---|:-:|---|---|---|---|---|
| `TCMCP-300` | Secrets in tool output | 1 | CWE-201; CWE-200 | — | MCP01 | LLM02 | vulnerable |
| `TCMCP-301` | PII in error messages | 1 | CWE-200; CWE-209 | — | — | LLM02 | vulnerable |
| `TCMCP-302` | Sensitive data in logs | 1 | CWE-532; CWE-312 | — | MCP01 | LLM02 | vulnerable |
| `TCMCP-303` | Missing output sanitization | 1 | CWE-116; CWE-79 | — | MCP01 | LLM02 | vulnerable |
| `TCMCP-304` | Covert exfil via BCC | 3 | CWE-201; CWE-319 | AML.T0110.001, AML.T0086 | MCP01 | — | vulnerable |
| `TCMCP-309` | Missing audit telemetry | 1 | CWE-778; CWE-223 | — | MCP08 | — | vulnerable |
| `TCMCP-311` | Token leakage via SSE | 2 | CWE-200 | — | MCP01 | LLM02 | vulnerable |
| `TCMCP-312` | Audit trail tampering | 2 | CWE-778; CWE-223 | — | MCP08 | — | vulnerable |
| `TCMCP-313` | Covert exfil via batch notification | 3 | CWE-201; CWE-319 | — | MCP01 | — | vulnerable |
| `TCMCP-314` | Typosquatting supply chain | 3 | CWE-829 | AML.T0115.002, AML.T0010.005 | MCP04 | LLM04 | vulnerable |
| `TCMCP-315` | Registry poisoning | 3 | CWE-829 | AML.T0115.002, AML.T0010.005 | MCP04 | LLM04 | vulnerable |
| `TCMCP-316` | Backdoored npm/PyPI package | 4 | CWE-829 | AML.T0115.002, AML.T0010.005 | MCP04 | LLM04 | documented-only |
| `TCMCP-317` | LiteLLM MCP server creation RCE (CVE-2026-30623) | 4 | CWE-77; CWE-78 | — | MCP05 | — | documented-only |
| `TCMCP-318` | Browser SSRF via DNS (doc. only) | 4 | CWE-918; CWE-346 | — | MCP05 | — | documented-only |
| `TCMCP-319` | Zero-click IDE via HTML | 4 | CWE-79 | — | — | — | documented-only |
| `TCMCP-320` | Social engineering via registry | 4 | CWE-284; CWE-290 | AML.T0115.002, AML.T0010.005 | MCP04 | — | documented-only |
| `TCMCP-321` | Marketplace poisoning real npm | 4 | CWE-829 | AML.T0115.002, AML.T0010.005 | MCP04 | — | documented-only |
| `TCMCP-400` | System prompt leakage via jailbreak | 1 | CWE-1427; CWE-200 | AML.T0056, AML.T0054 | MCP06 | LLM08 | vulnerable |
| `TCMCP-401` | Improper output handling (XSS) | 1 | CWE-79; CWE-116 | — | — | — | vulnerable |
| `TCMCP-406` | Misinformation propagation via tool data | 1 | — | — | — | LLM07 | vulnerable |
| `TCMCP-504` | WebSocket cleartext token leak | 2 | CWE-319; CWE-311 | — | MCP01 | — | vulnerable |
| `TCMCP-513` | LLM discovers no-TLS server | 2 | CWE-319; CWE-311 | — | MCP01 | — | vulnerable |
| `NEW-TCMCP-002` | Elicitation url-mode token exfil | 2 | CWE-200; CWE-522 | — | MCP01 | — | vulnerable |
| `NEW-TCMCP-003` | Cache poisoning cross-user (SEP-2549) | 2 | CWE-488; CWE-349 | — | MCP10 | — | vulnerable |
| `NEW-TCMCP-011` | Protocol pivoting MCP→A2A (OATF-009) | 3 | CWE-441; CWE-1427 | AML.T0110.002 | MCP06 | LLM01 | vulnerable |
| `NEW-TCMCP-016` | Task cross-client read (CVE-2026-52870, doc only) | 3 | CWE-862; CWE-639 | — | MCP07 | — | documented-only |
| `NEW-TCMCP-019` | Sampling without origin authentication (SEP-2577) | 2 | CWE-346; CWE-306 | — | MCP07 | — | vulnerable |
| `NEW-TCMCP-021` | Trace context poisoning (SEP-414) | 2 | CWE-345; CWE-778 | — | MCP08 | — | vulnerable |
| `NEW-TCMCP-024` | Stale tools/list cache (SEP-2549) | 2 | — | — | — | — | vulnerable |
| `NEW-TCMCP-027` | Pre-handshake -32602 error as an oracle | 2 | CWE-209; CWE-200 | — | — | — | vulnerable |
