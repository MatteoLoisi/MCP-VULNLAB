# Risultati del progetto V3

Questa è la cartella da consegnare. Contiene risultati, tabelle e prove sperimentali.

1. Leggi [RISULTATI.md](RISULTATI.md) per la sintesi dei tre esperimenti e i collegamenti ai file richiesti.
2. Consulta [LIMITI.md](LIMITI.md) per sapere quali conclusioni i dati consentono di sostenere.
3. Apri [risultati.pdf](risultati.pdf) e [tabelle.xlsx](tabelle.xlsx) per le tabelle già impaginate.

| Cartella | Contenuto |
|---|---|
| Exp1 | CSV delle run, percentuali D/A/E/U, tre transcript ed evidenze per RUN_ID |
| Exp2 | Catalogo dei 29 scenari, 29 report completi, controlli benigni e classificazione semantica v5 |
| Exp3 | Confronto di 14 target, scanner, sonde, SSRF e pilot attivo RedCell/Mcpwn con prove |
| Evidenze | Raw integrativi, input Exp2 e risultati dei pilot precedenti in un archivio separato |

Le consegne richieste si trovano qui:

| Richiesta | File |
|---|---|
| Exp1: una riga per run e le 14 colonne richieste | [exp1_runs.csv](Exp1/dati_per_run/exp1_runs.csv) |
| Exp1: percentuali per modello, scenario e profilo | [exp1_summary.csv](Exp1/riepiloghi_ricalcolati/exp1_summary.csv) |
| Exp1: tre transcript e prove per RUN_ID | [Esempi](Exp1/transcript_esemplari/indice.csv), [prove delle run](Exp1/evidenze_per_run/indice.csv) |
| Exp2: conteggi dei 29 scenari | [exp2_risultati_29_scenari.csv](Exp2/catalogo_storico/exp2_risultati_29_scenari.csv) |
| Exp2: parametri e cinque risposte complete in ogni report | [Indice dei 29 report](Exp2/catalogo_storico/indice_report.csv) |
| Exp3: tabella dei 14 target con le quattro colonne richieste | [exp3_confronto_target.csv](Exp3/confronto_14_target/exp3_confronto_target.csv) |
| Exp3: cinque configurazioni SSRF e stato per scanner | [risultati_ssrf.csv](Exp3/configurazioni_ssrf/risultati_ssrf.csv) |

I CSV sono UTF-8 con BOM e separatore virgola. In Excel italiano usare **Dati → Da testo/CSV**, scegliere UTF-8 e delimitatore virgola. I percorsi nelle celle sono relativi alla cartella principale. Le campagne e i protocolli differenti hanno file separati.

D = decisione malevola, A = chiamata inoltrata, E = ricezione dei dati di prova, U = criterio di riuscita del task. Raw indica un dato originale completo. Unknown indica una risposta non classificabile.

I file che si chiamano validazione dentro Exp2 sono risultati sperimentali del classificatore, inclusi nella consegna. Il pacchetto non contiene cartelle di verifica del dossier, piani, checklist o script di controllo.

I codici fonte S01-S26 usati nell’Excel rimandano all’[indice delle fonti](Evidenze/fonti_dei_risultati.csv). Nel PDF i percorsi dei dati sono riportati direttamente sotto le tabelle. Per il lotto catene e il confronto v4, i raw sono nell’archivio integrativo: i percorsi originali sono elencati nel relativo indice.

Il pilot attivo RedCell/Mcpwn ha una [spiegazione dedicata](Exp3/sonde_esterne/RISULTATI_SONDE_ESTERNE.md), CSV e report nativi. È una campagna separata: l’Excel principale conserva le tabelle del confronto storico. Il PDF è un documento unitario con tre sezioni: Esperimento 1, Esperimento 2 ed Esperimento 3. Tutte le campagne scanner e sonde sono nelle sottosezioni 3.1-3.6, con una mappa iniziale che chiarisce come si collegano le prove.

Sono inclusi anche [MCP Guard e fuzzer](Exp3/guard_e_fuzzer/RISULTATI.md), la [revisione cieca P2](Exp3/revisione_descrizioni_P2/RISULTATI.md) e i [pilot precedenti](Evidenze/LEGGIMI.md). Sono popolazioni distinte. Nei CSV derivati le colonne `*_file` indicano file della consegna; `raw_archive` e `raw_member` identificano i raw compressi. I riferimenti nativi conservati accanto a questi campi descrivono i percorsi originali.
