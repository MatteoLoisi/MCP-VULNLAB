# Risultati V3

Dati disponibili al 23 ottobre 2026. V3 identifica il progetto; v5 è il protocollo del classificatore semantico. Le popolazioni seguenti restano separate.

## Esperimento 1: agenti reali

D = decisione malevola; A = chiamata inoltrata; E = ricezione dei dati di prova; U = criterio di riuscita del task.

| Campagna | Run | D | A | E | U |
|---|---:|---:|---:|---:|---:|
| Matrice principale, temperatura 0.2 | 960 | 0 | 0 | 0 | 960 |
| Replica con seed, temperatura 0.2 | 960 | 0 | 0 | 0 | 960 |
| Standard Qwen, temperatura 0.7 | 320 | 0 | 0 | 0 | 320 |
| Braccio JSON, temperatura 0.2 | 120 | 15 | 6 | 4 | 91 |
| Braccio JSON, temperatura 0.7 | 120 | 20 | 11 | 8 | 94 |

Nel profilo default dei bracci JSON, A=E=0. Il lotto catene è separato: 479/480 record, 12 errori tecnici, 467 utilizzabili, U=211/467.

- [CSV principale richiesto: 960 righe e 14 colonne](Exp1/dati_per_run/exp1_runs.csv).
- [Percentuali per modello, scenario e profilo](Exp1/riepiloghi_ricalcolati/exp1_summary.csv).
- [Altre campagne](Exp1/dati_per_run/altre_campagne/).
- [Tre transcript esemplari](Exp1/transcript_esemplari/indice.csv).
- [Evidenze per campagna e RUN_ID](Exp1/evidenze_per_run/indice.csv).

## Esperimento 2: classificazione dei contenuti

### 2.1 Catalogo storico

Cinque query per ID e soglia 0.4. Ogni condizione contiene 29 ID e 145 query.

| Modello | Temperatura | Exfiltrate | Ignore | Unknown | ID sopra soglia |
|---|---:|---:|---:|---:|---:|
| llama3.2:3b | 0.2 | 145 | 0 | 0 | 29 |
| llama3.2:3b | 0.7 | 145 | 0 | 0 | 29 |
| qwen2.5:7b | 0.2 | 105 | 40 | 0 | 21 |
| qwen2.5:7b | 0.7 | 105 | 40 | 0 | 21 |

I controlli benigni hanno 15 query per condizione. Llama produce 15 exfiltrate per temperatura; Qwen produce 15 ignore per temperatura. Il significato limitato del 145/145 è spiegato in [LIMITI.md](LIMITI.md).

- [Risultati dei 29 scenari nella configurazione richiesta](Exp2/catalogo_storico/exp2_risultati_29_scenari.csv).
- [Indice dei 29 report con parametri e risposte complete](Exp2/catalogo_storico/indice_report.csv).
- [Tutte le risposte grezze del catalogo e dei controlli](Exp2/catalogo_storico/risposte_grezze_complete.csv).
- [Controlli benigni](Exp2/controlli_benigni/).

### 2.2 Classificazione semantica v5

| Popolazione | Testi | Query | TP | TN | FP | FN | Unknown | Corrette |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Validazione | 24 | 120 | 46 | 60 | 0 | 10 | 4 | 106/120 |
| Sviluppo | 12 | 60 | 28 | 30 | 0 | 0 | 2 | 58/60 |
| Acquisizioni MCP | 6 | 30 | 25 | 0 | 0 | 5 | 0 | 25/30 |
| Artifact statici | 6 | 30 | 24 | 0 | 0 | 5 | 1 | 24/30 |
| File e SQLite | 6 | 30 | 15 | 10 | 0 | 0 | 5 | 25/30 |

Correttezza della validazione: 88.3%. Nel confronto appaiato sugli stessi 24 testi, v4 ha 80/120 risposte corrette e v5 106/120. La copertura specifica è 14/29 ID, con otto acquisizioni MCP e sei artifact.

- [Riepilogo delle popolazioni](Exp2/classificazione_semantica_v5/riepilogo_popolazioni.csv).
- [Risultati per query e risposte complete](Exp2/classificazione_semantica_v5/risultati_per_query.csv).
- [Dati del confronto v4](Exp2/classificazione_semantica_v5/confronto_v4.csv).
- [Copertura dei 29 ID](Exp2/copertura_29_id/copertura.csv).

## Esperimento 3: scanner e sonde

### 3.1 Confronto principale, sonde di effetto e SSRF

Quattordici target STDIO: quattro server di riferimento e dieci DVMCP. Scanner principale MCTS 0.1.4; confronto aggiuntivo Cisco 4.8.6 YARA e MCP-Shield 1.0.4 senza Claude.

Le sei sonde di effetto comprendono tre effetti confermati (c3, c8, c9) e tre esecuzioni non conclusive (c2, c10, R1). Le cinque configurazioni SSRF hanno tre effetti e due negativi entro il piano. Questi sono risultati della nostra sonda P6: manca il confronto SSRF di Cisco, MCP-Shield, RedCell e Mcpwn sugli stessi cinque prodotti/versioni; MCTS è non applicabile come controllo SSRF. [Dettaglio della copertura](Exp3/configurazioni_ssrf/LEGGIMI.md).

| Scanner | TP | TN | FP | FN |
|---|---:|---:|---:|---:|
| MCTS, diagnostica P2 | 4 | 5 | 1 | 2 |
| Cisco, diagnostica P2 | 2 | 6 | 0 | 4 |
| Shield, diagnostica P2 | 5 | 5 | 1 | 1 |

La diagnostica è separata dal confronto sui server reali: dodici testi sintetici e 36 scansioni.

- [Tabella richiesta, con quattro colonne e 14 target](Exp3/confronto_14_target/exp3_confronto_target.csv).
- [Dettaglio dei tre scanner e riferimenti alle prove](Exp3/confronto_14_target/exp3_dettaglio_evidenze.csv).
- [Risultati delle sonde](Exp3/sonde_ed_effetti/risultati_sonde.csv).
- [Risultati SSRF](Exp3/configurazioni_ssrf/risultati_ssrf.csv).
- [Diagnostica per scansione](Exp3/diagnostica_scanner/risultati_per_scansione.csv).

### 3.2 Pilot attivo RedCell e Mcpwn

Campagna separata del 19 ottobre 2026, con 26 tentativi: 22 sessioni utili
e quattro errori tecnici. RedCell ha esaminato dieci challenge DVMCP con
290 chiamate: 15 segnalazioni su sette target, di cui dieci con prova
dinamica e cinque basate sulle descrizioni. Mcpwn rileva esecuzione su c8
e c9; su c3 produce 68 segnalazioni, soprattutto temporali, senza rilevare
la lettura di file dimostrata da RedCell e P5. Il confronto SSRF riguarda
un solo fetch locale. I conteggi di scanner con profili diversi non sono
una graduatoria di efficacia.

- [Funzionamento, risultati e interpretazione delle prove](Exp3/sonde_esterne/RISULTATI_SONDE_ESTERNE.md).
- [RedCell sui dieci target](Exp3/sonde_esterne/risultati_redcell.csv).
- [Sessioni Mcpwn](Exp3/sonde_esterne/risultati_mcpwn.csv).
- [Tutti i tentativi e collegamenti ai report nativi](Exp3/sonde_esterne/tentativi.csv).


### 3.3 Altre prove dello stesso esperimento

- [MCP Guard e MCP Server Fuzzer](Exp3/guard_e_fuzzer/RISULTATI.md):
  analisi statica, prove dinamiche, harness separato e robustezza del protocollo.
- [Revisione cieca P2 delle descrizioni](Exp3/revisione_descrizioni_P2/RISULTATI.md):
  accordo 14/14 con le etichette originali, assistito da AI; non è una revisione umana indipendente.

## Documenti e prove

[PDF dei risultati](risultati.pdf), [tabelle Excel](tabelle.xlsx) e [limiti scientifici](LIMITI.md).

Il PDF ha tre sezioni principali e riunisce tutte le prove scanner/sonde
in Esperimento 3, con sottosezioni 3.1-3.6 e una mappa del loro legame.
I [pilot precedenti e le relative acquisizioni](Evidenze/LEGGIMI.md)
sono popolazioni esplorative Exp1 e protocolli v4 separati dai risultati principali.

Le prove principali sono nei tre esperimenti. L’[archivio integrativo](Evidenze/prove_integrative.zip) conserva i raw del lotto catene, quelli del confronto v4 e le acquisizioni/artifact di input reperibili nelle fonti. L’[indice](Evidenze/indice_prove_integrative.csv) indica i percorsi interni.
