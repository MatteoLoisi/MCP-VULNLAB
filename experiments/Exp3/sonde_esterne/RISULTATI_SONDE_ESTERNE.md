# Pilot Exp3 — RedCell, Mcpwn e sonde locali

Eseguito il 19 ottobre 2026. Le prove mostrano che esistono scanner esterni
con verifiche attive paragonabili a P4/P5/P6. Questo pilot non sostituisce la
campagna Exp3 precedente e non conclude il confronto di tutte le capacità.

## Come funzionano e quali dati producono

Gli scanner sono client MCP: avviano il server da esaminare e comunicano
con lui tramite STDIO, cioè richieste e risposte JSON-RPC attraverso i flussi
del processo. Enumerano i tool e i loro argomenti; poi inviano input di prova
e analizzano le risposte. In questo pilot non interviene un modello LLM.

**RedCell** combina indicatori nelle descrizioni con prove attive. Per la
lettura crea un file canarino con contenuto imprevedibile e verifica se il
tool lo restituisce; per i comandi cerca il prodotto di due numeri casuali;
per SSRF attende una richiesta HTTP con un identificatore univoco su un
endpoint locale. Una segnalazione dinamica può includere il campo `witness`,
con richieste/risposte, argomenti, output e criterio che riconosce l'effetto.
Gli indicatori nelle descrizioni non dimostrano questi effetti.

**Mcpwn** invia payload agli argomenti dei tool e cerca firme nelle risposte,
come l'identità dell'utente di esecuzione. Registra anche indicatori di
protocollo, prompt e tempi. Ogni segnalazione conserva tool, argomento,
payload, categoria, severità, corrispondenze trovate e timestamp. Il suo
report non contiene lo stesso certificato JSON-RPC di RedCell. I profili
quick, RCE-only e safe-mode limitano le prove effettivamente eseguite.

I JSON e i log sono gli output originali degli strumenti. I CSV seguenti
sono riepiloghi derivati da quegli output: non sono nuove esecuzioni.

## Risultati principali

- **RedCell:** dieci challenge DVMCP, 290 chiamate di sonda, 15 segnalazioni
  su sette challenge. Dieci segnalazioni hanno una prova dinamica; cinque
  sono indicatori euristici nelle descrizioni dei tool.
- **Mcpwn:** esecuzione di comandi rilevata su c8 e c9. La scansione completa
  in safe-mode di c3 non rileva la lettura arbitraria del file dimostrata da
  RedCell e P5. Produce invece 68 segnalazioni di altre categorie, di cui
  51 basate sui tempi. Non sono 68 vulnerabilità distinte confermate.
- **Confronto diretto locale:** P5 e RedCell leggono un canarino su c3;
  P4/P5 e RedCell osservano esecuzione e lettura su c8; P4, RedCell e Mcpwn
  osservano esecuzione su c9.
- **SSRF:** RedCell e P6 raggiungono il collaboratore loopback attraverso
  lo stesso server fetch, nella stessa immagine locale.
- **Controlli:** RedCell produce sei segnalazioni sul proprio esempio
  vulnerabile e zero su quello difeso. Mcpwn, nel profilo rapido RCE, ne
  produce rispettivamente una e zero sugli stessi esempi. I conteggi dei
  due scanner riguardano selezioni differenti.

Sono conservati **26 tentativi: 22 sessioni completate nel perimetro
selezionato e quattro errori tecnici**, inclusi i tentativi successivamente
ripetuti. Una sessione completata non equivale a copertura completa del
target o di ogni sonda.

## RedCell sulle dieci challenge

| Target | Segnalazioni native | Osservazione |
|---|---:|---|
| c1 | 0 | Nessuna segnalazione sui tool esaminati |
| c2 | 2 | Istruzioni sospette nelle descrizioni di due tool |
| c3 | 1 | `read_file` restituisce il canarino esterno alla directory pubblica |
| c4 | 0 | Nessuna segnalazione sui tool esaminati |
| c5 | 1 | Descrizione sospetta di `enhanced_calculate` |
| c6 | 2 | `read_document` e `read_upload` restituiscono `/etc/passwd` tramite traversal |
| c7 | 0 | Nessuna segnalazione sui tool esaminati |
| c8 | 2 | Valutazione di espressione shell e lettura del canarino tramite `analyze_log_file` |
| c9 | 4 | Espressioni shell valutate attraverso `ping_host`, `traceroute`, `port_scan`, `network_diagnostic` |
| c10 | 3 | Due descrizioni sospette e lettura del canarino tramite `analyze_log_file` |
| **Totale** | **15** | **Sette challenge con almeno una segnalazione** |

Le prove di lettura includono canarini imprevedibili e, su c6, firme del
file di sistema. L'oracolo di esecuzione di RedCell richiede il prodotto di
due numeri casuali, assente dal payload come risultato già calcolato.
I certificati contengono richieste e risposte JSON-RPC.

L'esecuzione osservata su un tool che offre esplicitamente una shell, come
quello di c8, dimostra una capacità esposta; da sola non dimostra un bypass
di autorizzazione. Anche per i tool che leggono liberamente log, attribuire
una violazione di isolamento richiede un confine di accesso dichiarato.
Per c3 tale confine è la directory pubblica della challenge; su c6 il
payload esce dalle directory dei documenti e degli upload.

Su c9 alcuni comandi diagnostici di base mancano nell'immagine o falliscono.
L'espressione shell aggiunta viene comunque valutata. Queste prove non
certificano il funzionamento dei comandi diagnostici né un'escalation dei
privilegi: il container usa l'utente 1001.

Gli esiti senza segnalazioni di c1/c4/c7 non significano che quei server
siano sicuri. RedCell qui enumera e chiama tool: non esegue l'equivalente
completo di P11/P15 sui contenuti delle risorse o sull'esposizione di token.

## Mcpwn: risultati e profili

| Sessione selezionata | Profilo | Report nativo | Interpretazione |
|---|---|---|---|
| c8, `mcpwn-c8-retry30` | safe-mode, RCE-only, timeout 30 s | 10 segnalazioni: 6 RCE, 2 blind timing, 2 timing | Risultati multipli sullo stesso tool; non dieci vulnerabilità indipendenti |
| c9, `mcpwn-c9-quick` | safe-mode, quick, RCE-only | 1 RCE su `ping_host` | Output di `id` dell'utente del container |
| c3, `mcpwn-c3-full` | safe-mode, timeout 15 s | 68: 1 state reset, 10 capability, 51 timing, 6 prompt indicators | Nessuna rilevazione FILE_READ/path traversal nel report |
| esempio vulnerabile | safe-mode, quick, RCE-only | 1 RCE | Controllo positivo del solo profilo RCE |
| esempio difeso | safe-mode, quick, RCE-only | 0 | Controllo negativo del solo profilo RCE |

La scansione estesa di c9 è terminata con **timeout 240 s**, senza report
finale. Il log resta disponibile; il risultato selezionato per c9 è il
pilot rapido completo. Il primo avvio rapido di c8 ha fallito la discovery,
ma Mcpwn ha restituito exit code 0 e un report vuoto: quel tentativo è
classificato come errore, non come server sicuro.

Nella revisione fissata di Mcpwn, la selezione ordinaria di payload degli
argomenti privilegia `command_injection`; il controllo separato di traversal
opera sulle risorse. La lettura attraverso un argomento filename di c3 non
ha quindi ottenuto una rilevazione equivalente a P5 in questa esecuzione.
Gli indicatori di prompt injection rappresentano risposte testuali
sospette: non è stato eseguito un modello per dimostrare che le seguisse.
I segnali temporali e di protocollo richiedono una validazione separata.

## Repliche delle sonde locali

| Target | Sonda locale | Risultato osservato |
|---|---|---|
| c3 | P5 | Canarino restituito da `read_file`; arresto dopo la prima lettura positiva, sweep incompleto |
| c8 | P4 | Marker eseguito tramite `execute_shell_command.command` |
| c8 | P5 | Canarino restituito da `analyze_log_file`; copertura nativa 13/13 unità |
| c9 | P4 | Marker eseguito tramite `ping_host.host` |
| c9 | P5 | Nessuna lettura confermata; sweep incompleto, tetto 60 tentativi, 18/25 unità |
| fetch locale | P6 | Token del collaboratore restituito da `fetch`; 1/1 unità |

P2 è stata inclusa nelle tre repliche DVMCP c3/c8/c9 e non ha trovato
istruzioni sospette in quelle descrizioni. Non è stata ripetuta qui sui
dieci target: i precedenti esiti P2 di c2/c5/c10 appartengono alla vecchia
campagna e non sono aggiunti alle nuove misure.

Queste repliche usano la CLI nativa `real_target_check.py`, con transcript
conservato e marker P4 predefinito `V2P4MARKER`. Non si attribuisce loro il
contratto `exp3-probe-worker-v1` della campagna precedente. Una verifica
separata ha correlato le risposte ai rispettivi ID di richiesta e ritrovato
i canarini di c3/c8 nelle risposte dei tool.

## SSRF: cosa è stato effettivamente provato

RedCell ha ricevuto un callback HTTP con nonce da `fetch` su
`127.0.0.1:<porta>`. P6, nella replica separata dello stesso target, ha
ottenuto il token del proprio endpoint interno. Entrambi i processi
scanner/target condividono il namespace di rete del rispettivo container.

La rete esterna è disabilitata. Non sono stati contattati metadata cloud,
servizi di terzi o la rete dell'host. La frase del report nativo RedCell
secondo cui il server raggiungerebbe anche `169.254.169.254` è una sua
generalizzazione, **non una misura di questo pilot**.

Il target è il modulo presente nell'immagine locale
`mcp-probe-fetch:20261001`, identificato da image ID e hash dei sorgenti in
`ambiente/fetch-environment.json`. Non è stata verificata l'identità
con una specifica release upstream. Non è stata completata la matrice dei
cinque target SSRF originariamente esclusi da Exp3.

## Ambiente, controlli e limiti

- RedCell `0.1.0`, commit `c033c02e80912a1e74490f3d388b666ebb66275c`.
- Mcpwn `1.0`, commit `2efd0273e71ccd285f398221189f59c5e4078feb`.
- DVMCP commit `79734c19f5104cd11486c90926d245560f53befa`, variante
  `server.py`, con lo shim STDIO già presente e immutato.
- Runtime Linux, Python 3.11.16, MCP SDK 1.29.1. Container non privilegiati,
  filesystem di base e sorgenti in sola lettura, directory temporanee,
  nessuna rete esterna, limiti di CPU/memoria/processi.
- Le **14 evidenze con certificato RedCell** hanno superato il verificatore
  offline dello stesso progetto: dieci DVMCP, tre dei controlli e una SSRF.
  Le otto segnalazioni euristiche restano prive di quella prova. La verifica
  controlla la coerenza del certificato, non è una revisione umana cieca né
  un replay indipendente dell'attacco.

Non si stimano precision, recall o FPR generali. Non si confrontano i
conteggi grezzi di scanner con categorie, oracoli e profili diversi. Ogni
caso ha una sola esecuzione utile, oltre ai tentativi tecnici conservati;
non sono state misurate stabilità statistica o catene con modelli LLM.
La campagna Linux resta separata dalle precedenti misure Windows.

Snyk Agent Scan non è stato eseguito: il suo protocollo di analisi usa
un'API remota. Questa è una scelta di perimetro del pilot locale, non
l'affermazione che lo strumento sia incapace di analizzare questi metadati.
Non sono state eseguite nuove analisi SAST/fuzzing estese di MCTS né un
confronto automatico con MCP Inspector.

Restano da pianificare il confronto completo a parità di classi e target,
le repliche, la validazione delle segnalazioni di Mcpwn, la copertura di
tutti i target SSRF e l'eventuale confronto remoto dei metadati. Le nuove
misure non sono state aggiunte automaticamente alla matrice Excel esistente.

## File da consultare

| File | Contenuto |
|---|---|
| [risultati_redcell.csv](risultati_redcell.csv) | Dieci target DVMCP: segnalazioni, prove dinamiche, indicatori e report originale |
| [risultati_mcpwn.csv](risultati_mcpwn.csv) | Cinque sessioni utili: profilo e conteggi per tipo, inclusi i controlli |
| [tentativi.csv](tentativi.csv) | Tutti i 26 tentativi, inclusi quattro errori tecnici; riferimenti ai report |
| [versioni.csv](versioni.csv) | Versioni e commit degli strumenti e del target |
| [runs](runs/) | Report JSON, HTML dove prodotto, log, condizioni di avvio e stato di ogni tentativo |
| [ambiente](ambiente/) | Runtime e identificazione delle immagini locali |

I percorsi nelle celle CSV sono relativi alla cartella principale RISULTATI_V3.
I report nativi mantengono i propri percorsi e testi originali. Severità e
confidence sono giudizi degli strumenti, non una certificazione indipendente.
I quattro errori tecnici non vanno contati come esiti negativi. Gli HTML
conservati sono quelli prodotti da RedCell; nel pilot Mcpwn ha prodotto JSON.

Questo pilot del 19 ottobre resta distinto dalla tabella principale sui
14 target e dalle tre prove storiche non conclusive. Non risolve
retroattivamente quelle acquisizioni. I risultati del pilot sono nei CSV
di questa cartella; l'Excel principale riporta il confronto storico.

Documentazione primaria delle revisioni usate:
[RedCell](https://github.com/Sahilo6/redcell/blob/c033c02e80912a1e74490f3d388b666ebb66275c/README.md),
[Mcpwn](https://github.com/Teycir/Mcpwn/blob/2efd0273e71ccd285f398221189f59c5e4078feb/README.md).
