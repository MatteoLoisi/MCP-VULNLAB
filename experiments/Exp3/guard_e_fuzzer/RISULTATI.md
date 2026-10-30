# Exp3 — MCP Guard e MCP Fuzzer (2026-10-20)

## Scopo

Verifica separata dei due strumenti suggeriti per chiudere i gap dell’Exp3:

- MCP Guard 2.0.0 per indicatori statici e prova dinamica P4/P5 in sandbox;
- MCP Server Fuzzer 0.7.0 per robustezza di protocollo e tool (P14).

Questa campagna non modifica il ground truth P2 né gli effetti MCTS già registrati. Un finding statico o un input accettato dal fuzzer non è una vulnerabilità dimostrata.

## Pin e ambiente

| Componente | Pin |
|---|---|
| MCP Guard | `e782de38d98219047c4dab3232395d8636c678c3`, versione 2.0.0 |
| MCP Server Fuzzer | `mcp-fuzzer==0.7.0`, commit `98c5d388f4edba9744bebb1f2dc72e2b537ee3b9` |
| DVMCP | commit `79734c19f5104cd11486c90926d245560f53befa` |
| Python | 3.13.3 |
| Docker | 29.9.1 |
| Node/npm | 24.18.0 / 12.0.1 |
| uv | 0.12.1 |

Target c3/c8/c9 copiati da DVMCP e verificati contro `environment/dvmcp-sources.json`:

| Target | `server.py` SHA-256 | Ruolo atteso |
|---|---|---|
| c3 | `dc16ad40aeb20c33d222c5461080be1745cce0a859efd8701a1c9529db15d633` | P5 path traversal |
| c8 | `21ae92e1c04121b2d20aada88af2f988da485a7c73f60f22c0bb7e96e431778b` | P4 RCE |
| c9 | `81cdc1a6632844400ac7f9c969fe299b5e66e4bc300eb592392a83da0534b76b` | P4 command injection |

## MCP Guard — analisi statica

Scansione pulita del solo `server.py`, senza adapter:

| Target | Finding | Severità | Sink |
|---|---:|---|---|
| c3 | 2 | high | `server.py:94`, `server.py:99` — `open()` con filename controllabile |
| c8 | 3 | 1 critical, 2 high | `server.py:110` shell taint; `server.py:24` credenziale hardcoded; `server.py:140` path taint |
| c9 | 5 | 4 critical, 1 high | `server.py:55`, `88`, `127`, `189` shell taint; `server.py:230` path taint |

Gli stessi target completi di adapter producono un finding aggiuntivo `MCPG-PY-PATH-TAINT` su `dvmcp_stdio.py:151`; è un falso positivo dell’adapter (`__file__` usato per caricare il modulo), quindi è escluso dai conteggi puliti.

La credenziale c8 è la chiave AWS di esempio `AKIAIOSFODNN7EXAMPLE`: è un segreto hardcoded, non una credenziale reale verificata.

I sink shell di c8/c9 e i sink filesystem di c3 sono coerenti con gli effetti P4/P5 già osservati, ma restano indicatori statici.

## MCP Guard — prova dinamica

### Sandbox Docker ufficiale: incomplete

Tentativo su c8:

```text
mcp-guard targets/c8 --allow-execute --sandbox docker \
  --entrypoint "python dvmcp_stdio.py ." --no-static --no-deps \
  --probe-budget 20 --handshake-timeout 15 --timeout 60 \
  --format json --fail-on none
```

Esito: `dynamic.ran=false`, handshake fallito dopo 93.5 s. Il comando docker generato contiene:

```text
C:\Users\ML\AppData\Local\Temp\mcpguard-venvs\...\Scripts\python.exe dvmcp_stdio.py .
```

dentro `python:3.11-alpine`. Le dipendenze sono state installate nella venv Windows, non nel container. Il percorso dell’interprete host non esiste nel container, quindi il target non parte. La sandbox Docker ufficiale resta `incomplete`; c3 e c9 non sono stati ritentati in quella modalità.

### Prova in sandbox esterno: completo

Per eseguire i probe senza esporre l’host è stato costruito un container
esterno con MCP Guard 2.0.0 pinnato (`guard-container/Dockerfile`,
`python:3.13-slim`). Il container usa `--network none`, `--read-only`,
`/tmp` tmpfs, utente 1000, cap drop e `no-new-privileges`. Dentro il container
Guard usa `--sandbox none`: l’isolamento è fornito dal container esterno,
non dalla sandbox Docker ufficiale. Questa distinzione è obbligatoria nel
report: la modalità Docker nativa resta `incomplete`.

| Target | Probe | Finding | Esito |
|---|---:|---:|---|
| c3 | 8 | 1 high | `read_file` legge un file canary in `/tmp` fuori dalla root: P5 path traversal dimostrato. |
| c8 | 8 | 1 critical, 1 high | `execute_shell_command` esegue il canary shell (P4); `analyze_log_file` legge un canary in `/tmp` (P5). |
| c9 | 8 | 0 | Nessun finding: Guard non dimostra l’injection su `ping_host`. È un falso negativo rispetto all’harness, non un esito “secure”. |

I probe sono 8 per target, handshake riuscito (`server_info.is_mcp_server=true`).
Lo stage `dependencies` fallisce su `/.cache` read-only, ma la venv dinamica
installa `mcp==1.28.0` dai wheel locali (`python_install_mode=wheels-only`) e
il target parte. I JSON sono in `guard-container/*-dynamic.json`.

## Harness Docker locale per P4/P5

Per non lasciare scoperti gli effetti, è stato usato un harness separato, non
MCP Guard: un client MCP stdio (`harness/effect_probe.py`) e il target nello
stesso container `python:3.13-slim` con `mcp==1.28.0`. Il container usa
`--network none`, `--read-only`, `/tmp` tmpfs, utente 1000, cap drop e limiti
di memoria/PID. Le immagini sono costruite dai Dockerfile `harness/Dockerfile.*`.

| Target | P | Tool e input | Effetto osservato |
|---|---|---|---|
| c3 | P5 | `read_file` con `../../tmp/dvmcp_challenge3/private/system_credentials.txt` | Restituisce `SYSTEM CREDENTIALS - HIGHLY RESTRICTED` e la API key privata: lettura arbitraria fuori dalla directory pubblica. |
| c8 | P4 | `execute_shell_command` con `echo MCPGUARD_DYNAMIC_C8_20261020` | Output `MCPGUARD_DYNAMIC_C8_20261020`: comando shell eseguito. |
| c9 | P4 | `ping_host` con host `127.0.0.1; echo MCPGUARD_DYNAMIC_C9_20261020`, count 1 | Output `MCPGUARD_DYNAMIC_C9_20261020`: injection eseguita. `ping` non è presente nell’immagine, ma il comando iniettato dopo `;` viene eseguito comunque. |

Tutti e tre hanno `isError=false` e restituiscono il marker/effetto atteso.
Queste righe sono `demonstrated_effect` e riproducono in container gli effetti
già registrati da MCTS; non modificano il ground truth P2 e non sono un esito
di MCP Guard. La sandbox Docker ufficiale di Guard resta `incomplete`; la
prova Guard in sandbox esterno è separata e ha mancato c9.

Immagini: c3 `sha256:f4f7d40c...`, c8 `sha256:5e46ca10...`, c9
`sha256:b501917f...` (ID completi in `raw/docker-effect/verification.json`).

## MCP Server Fuzzer — pilot c2, R1, R2, R3

Target c2 (DVMCP challenge 2), R1-filesystem, R2-memory e R3-sequential-thinking (`@modelcontextprotocol/*@1.0.0`), stdio, `--enable-safety-system --no-network`, fs-root in temp:

| Target | Modalità | Run | Tool | Finding | Crash | Eccezioni | Safety blocked |
|---|---|---:|---:|---:|---:|---:|---:|
| c2 | protocol, aggressive | 53 | 0 | 23 accepted malformed | 0 | 0 | 0 |
| c2 | tools, realistic | 4 | 2 | 4 accepted malformed | 0 | 0 | 0 |
| R2-memory | tools, realistic | 18 | 9 | 14 accepted malformed, 5 injection reflection | 0 | 0 | 0 |
| R1-filesystem | tools, realistic | 28 | 14 | 4 accepted malformed, 1 injection reflection | 0 | 0 | 0 |
| R3-sequential-thinking | tools, realistic | 2 | 1 | 1 injection reflection | 0 | 0 | 0 |

Il client roots di MCP Fuzzer 0.7.0 sostituisce le directory passate al
server con la propria working directory. La run R1 valida è quindi stata
eseguita con cwd `C:/Users/ML/AppData/Local/Temp/mcptargets/fuzz-r1-root`;
le prime run, che vedevano il progetto come root, sono in
`raw/mcp-fuzzer/superseded/` e non sono usate come evidenza.

Dettaglio c2: `get_company_data` 2/2 e `search_company_database` 2/2 con input `' OR '1'='1`; il server risponde senza errore e riflette la stringa. Non c’è accesso a un database né una differenza di risultato che provi SQL injection: è un caso di input malformato accettato, non una vulnerabilità.

Dettaglio R2: 9 tool, 18 run. Il marker `<script>` viene memorizzato e restituito da `read_graph`/`create_entities`; il server non ha una UI HTML, quindi non è XSS. I 5 `injection_reflection` sono un’euristica del fuzzer, non una prova di esecuzione. Il tasso “handled correctly” è 16.7% e non è un tasso di vulnerabilità.

Dettaglio R1: 14 tool, 28 run, 5 finding, 0 crash: 2 `accepted_malformed` su `read_multiple_files` con path invalidi (access denied/ENOENT), 2 su `list_allowed_directories` con campi extra ignorati e 1 `injection_reflection` su `read_multiple_files` (marker `<script>` riflesso in un errore, nessuna UI HTML). La run usa `fuzz-r1-root` come cwd/root; nessun file del progetto è nel perimetro.

Dettaglio R3: 1 tool, 2 run, 1 `injection_reflection`: `sequentialthinking` restituisce il testo di ragionamento, comportamento atteso per un server di pensiero sequenziale; 0 crash.

Triage R1: la chiamata diretta a `list_allowed_directories` restituisce solo
la directory configurata, senza contenuto di file. Il flag MCP-Shield
`Sensitive file access: access file` è quindi un falso positivo euristico,
non un accesso sensibile; è una `negative_check`, non una revisione
indipendente. Dettaglio in `raw/r1-triage/list-allowed.json`.

Il fuzzer ha etichettato i risultati come “Vulnerabilities Found”, ma le categorie osservate sono `accepted_malformed` e `injection_reflection`: nel report vanno trattate come robustezza P14, non come prova di exploit. R1, R2 e R3, target puliti di riferimento, producono comunque finding (riflessioni e input accettati): il dato mostra l’alta sensibilità/l’etichettatura aggressiva dello strumento. Il pilot copre 4 target, non i 14 dello sweep.

## Interpretazione

- MCP Guard statico aggiunge indicatori coerenti con P4 (c8/c9) e P5 (c3); non è una prova dinamica.
- La sandbox Docker ufficiale di MCP Guard resta `incomplete` per il bug della venv host.
- La prova MCP Guard in container esterno dimostra P5 su c3, P4+P5 su c8 e non trova c9: 0 finding non significa “secure”. La sandbox usata è esterna, non quella Docker ufficiale.
- L’harness Docker separato dimostra gli effetti P5 (c3) e P4 (c8/c9), ma non è MCP Guard e non va presentato come prova dello scanner; su c9 compensa il falso negativo di Guard.
- Il fuzzer su c2/R1/R2/R3 non produce crash, eccezioni o blocchi di sicurezza; produce input malformati accettati e riflessioni. Su R1/R2/R3 puliti le riflessioni sono attese e non sono XSS.
- Questi strumenti non sostituiscono gli effetti MCTS o il ground truth P2; nessuna metrica di precisione/recall va calcolata da questa campagna.

## Dati consegnati

[Risultati per strumento, target e modalità](risultati.csv).
[Indice dei report e log originali](prove.csv). I percorsi nelle celle
sono relativi alla radice RISULTATI_V3; i membri originali indicano la
provenienza. I JSON di Guard sono nelle cartelle raw e guard-container.
Le prove dell'harness locale restano distinte dagli esiti di Guard.
I risultati del fuzzer riguardano robustezza e riflessioni, non exploit
confermati. I sorgenti e gli script di esecuzione non sono nella consegna.
