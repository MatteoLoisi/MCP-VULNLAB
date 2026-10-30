# Quantitative comparison with related work

Measurable characteristics of MCP-VulnLab and four related benchmarks.

Values for the other projects are those reported in the cited papers, accessed in October 2026. *n.r.* marks a characteristic that the paper does not report.

| | InjecAgent [1] | MCPTox [2] | MCPSecBench [3] | MCP Pitfall Lab [4] | MCP-VulnLab |
|---|---|---|---|---|---|
| **Test set** | 1,054 cases | 1,348 malicious cases | 17 attacks, 15 runs each | 3 scenarios, 12 payload templates; 2,579 runs | 136 scenarios (120 executable, 16 documentation-only) |
| **Attack classes** | 2 (direct harm, data stealing) | 1 (tool poisoning); 10 risk categories, 3 templates | 17 attacks on 4 surfaces (client, protocol, server, host) | 6 pitfall classes, 5 attack vectors | 84 scenario types in 4 risk classes; all 10 OWASP MCP categories |
| **MCP-specific** | No | Yes | Yes | Yes | Yes |
| **Transports** | n.a. | n.r. | Local, Streamable HTTP, SSE | In-process | STDIO, Streamable HTTP, legacy SSE |
| **Systems under test** | 17 user and 62 attacker tools, with simulated responses | 45 live MCP servers, 353 tools | 3 host platforms (Claude, OpenAI, Cursor) | 6 server variants (baseline and hardened) | 13 laboratory services; 14 external STDIO servers and 5 SSRF product configurations |
| **Models** | 30 agents | 20 agents | Models of the 3 platforms | 4 | 1 (`llama3.2:3b`) in the catalogue campaign; 3 local models in the later version |
| **Success criterion** | Next agent action is the attacker tool call | Agent chooses the call induced by the poisoned tool | LLM-based judge | Validators on traces and persisted state | Expected status compared with evidence; in the catalogue campaign, model tests use a classification decision |
| **Effect verified at the recipient** | No | No | n.r. | In trace or state | Yes: collector record and network capture (in the catalogue campaign the transfer of model tests is performed by the harness; in the later version by the agent) |
| **Automation** | Automatic parsing; cases generated and manually refined | Automatic | Automatic, GUI harness | Automatic runner and validators | Automatic runner and harness, with expected outcomes |
| **Security references** | None | None | 1 CVE cited | OWASP as background | 72 CWE, 13 MITRE ATLAS techniques, 10 OWASP MCP and 6 OWASP LLM categories, 11 CVEs |

## Reading the comparison

MCP-VulnLab's added value lies in three areas:

- the breadth of its catalog across service, protocol, and model surfaces;
- its systematic mapping to security references ([catalogue-taxonomy.md](catalogue-taxonomy.md));
- verifying effects at the recipient rather than inferring them from the model's output.

The other benchmarks evaluate many more models. InjecAgent and MCPTox also measure an agent's deviation from a user task, whereas the catalogue campaign uses a single model and a classification task. The later version of the laboratory ([laboratory-v4.md](laboratory-v4.md)) addresses this difference. Comparisons across studies must also account for differences in model versions, tool permissions, attack objectives, and success criteria.

## Sources

1. Q. Zhan, Z. Liang, Z. Ying, D. Kang. *InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated Large Language Model Agents*. Findings of ACL 2024. <https://arxiv.org/abs/2403.02691>
2. Z. Wang et al. *MCPTox: A Benchmark for Tool Poisoning Attack on Real-World MCP Servers*. arXiv:2508.14925. <https://arxiv.org/abs/2508.14925>
3. Y. Yang et al. *MCPSecBench: A Systematic Security Benchmark and Playground for Testing Model Context Protocols*. arXiv:2508.13220v3. <https://arxiv.org/abs/2508.13220v3>
4. R. Hao, Z. Tan. *MCP Pitfall Lab: Exposing Developer Pitfalls in MCP Tool Server Security under Multi-Vector Attacks*. arXiv:2604.21477v2. <https://arxiv.org/abs/2604.21477v2>

MCP-VulnLab counts come from [`results/catalogue-136.csv`](../results/catalogue-136.csv) and from the other tables in [`results/`](../results/) and [`matrix.xlsx`](../matrix.xlsx).
