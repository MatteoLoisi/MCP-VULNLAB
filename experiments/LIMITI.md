# Limiti dei risultati

Questi punti spiegano fino a dove arrivano le conclusioni sostenute dai dati. Non sono un elenco di controlli tecnici.

## 1. Seed della matrice principale

Nelle 960 run originarie il seed non è stato richiesto. Esiste una replica separata di 960 run con 20 seed per cella, che non ricostruisce i seed delle prime run. [Dati principali](Exp1/dati_per_run/exp1_runs.csv), [replica](Exp1/dati_per_run/altre_campagne/replica_seed.csv).

## 2. Lotto catene incompleto e provenienza storica

Sono disponibili 479 dei 480 record previsti. Dodici hanno errori tecnici: restano 467 osservazioni utilizzabili, con U=211/467. Una prova è persa. Il riepilogo storico dichiara anche quattro difformità documentali e un registro dei tentativi ricostruito successivamente; la documentazione attuale non dimostra da sola l’integrità originaria. [Osservazioni](Exp1/dati_per_run/altre_campagne/catene_osservazioni.csv), [chiusura storica](Exp1/chiusura_catene.json).

## 3. Il 145/145 storico non misura accuratezza

I 29 ID del catalogo storico riusano sette testi. Llama produce exfiltrate anche sui 15 controlli benigni per temperatura. Il valore 145/145 descrive quindi le decisioni del protocollo storico e non dimostra che tutti i 29 meccanismi MCP siano vulnerabili. I risultati semantici v5 hanno un protocollo distinto. [Catalogo](Exp2/catalogo_storico/exp2_risultati_29_scenari.csv), [controlli benigni](Exp2/controlli_benigni/benigni_llama32_temperatura02.csv).

## 4. Copertura specifica v5: 14 ID su 29

Sono misurati 14 ID: otto mediante acquisizioni MCP e sei mediante artifact statici. Restano quattro acquisizioni mancanti e undici meccanismi privi di validazione specifica. Classificare un testo non dimostra, da solo, che un agente esegua un’azione o produca un’esfiltrazione. [Copertura completa dei 29 ID](Exp2/copertura_29_id/copertura.csv).

## 5. Tre prove storiche di effetto non conclusive

Questi esiti appartengono alla campagna precedente al pilot RedCell/Mcpwn del 19 ottobre. Il pilot ha acquisizioni e profili propri e non sostituisce quei report.

- **c2 e c10, P4:** nove tentativi ciascuno; i report riportano secure ma manca una prova specifica sufficiente a confermare quel giudizio. I report e le acquisizioni sono presenti. Non si può dedurre né vulnerabilità certa né sicurezza dimostrata.
- **R1-filesystem, P5:** raggiunto il limite di 60 tentativi coprendo 3 delle 17 unità pianificate. Il risultato è incompleto.

Gli indicatori testuali P2 su c2 e c10 restano distinti dalle prove di effetto. Gli effetti confermati su c3, c8 e c9 restano validi. [Risultati delle sei sonde](Exp3/sonde_ed_effetti/risultati_sonde.csv).

## 6. Confronti di temperatura e utilità

I confronti fra i bracci JSON a temperatura 0.2 e 0.7 sono descrittivi: cambiano anche runtime, contenuto e pretesto. Non isolano il solo effetto della temperatura. U controlla la presenza di una stringa attesa, non la qualità semantica generale della risposta. Le campagne principali, le repliche e i pilot sono popolazioni separate. [Riepiloghi Exp1](Exp1/riepiloghi_ricalcolati/).

## 7. Ripetizioni ed etichette locali

Cinque query sullo stesso testo non equivalgono a cinque esempi indipendenti. La validazione v5 contiene 24 testi e 120 query. Le etichette sono locali e assistite da AI, senza validazione umana indipendente. Le 270 query v5 appartengono a cinque popolazioni e non sono un unico benchmark. [Risultati v5](Exp2/classificazione_semantica_v5/riepilogo_popolazioni.csv).

## 8. Ambito del confronto fra scanner

Gli allarmi testuali generici di MCTS non dimostrano esecuzione o esfiltrazione. La diagnostica aggiuntiva usa dodici descrizioni sintetiche e 36 scansioni, con conoscenza delle famiglie di regole: non sostiene una graduatoria generale degli scanner. Le informazioni sulla loro manutenzione sono quelle archiviate al 17 ottobre 2026. [Confronto sui target](Exp3/confronto_14_target/exp3_confronto_target.csv), [diagnostica](Exp3/diagnostica_scanner/matrici_confusione.csv).

## 9. Negativi SSRF entro il piano

Le due configurazioni negative hanno 30 tentativi e 30 rifiuti della policy applicativa ciascuna, con zero blocchi middleware. Questo risultato non dimostra l’assenza universale di SSRF. Il confronto SSRF con MCTS è non applicabile perché manca il controllo equivalente. Cisco e MCP-Shield non sono stati eseguiti su queste cinque configurazioni; anche RedCell e Mcpwn non hanno misure sugli stessi cinque prodotti/versioni. Il callback RedCell del pilot separato non colma questo confronto. [Copertura per scanner](Exp3/configurazioni_ssrf/LEGGIMI.md). [Cinque configurazioni SSRF](Exp3/configurazioni_ssrf/risultati_ssrf.csv).

## 10. Estensione opzionale catene non eseguita

Le 320 run opzionali dell’estensione catene non sono state eseguite. Sono distinte dalle 320 run standard Qwen a temperatura 0.7, che risultano completate. [Run standard Qwen 0.7](Exp1/dati_per_run/altre_campagne/qwen_temperatura07.csv).

## 11. Pilot attivo RedCell e Mcpwn

Il pilot confronta strumenti con profili diversi e una sola esecuzione utile
per caso, oltre ai tentativi tecnici conservati. Le 15 segnalazioni RedCell
sui dieci DVMCP comprendono dieci prove dinamiche e cinque indicatori nelle
descrizioni. Le 68 segnalazioni Mcpwn su c3 non sono 68 vulnerabilità
indipendenti confermate: 51 sono basate sui tempi. Gli esiti senza allarmi
non dimostrano sicurezza generale; un tool che espone intenzionalmente una
shell non dimostra da solo un bypass. Il test SSRF riguarda un solo server
fetch locale con callback loopback, senza accesso a metadata cloud o rete
esterna. Non si stimano precision, recall o FPR generali. Quattro errori
tecnici sono mantenuti e distinti dai negativi.
[Risultati e prove del pilot](Exp3/sonde_esterne/RISULTATI_SONDE_ESTERNE.md).

## 12. MCP Guard, fuzzer e revisione cieca P2

Guard ha un tentativo nella sandbox Docker ufficiale incompleto. La prova
utile usa un container esterno: rileva lettura su c3 ed esecuzione/lettura
su c8, ma non rileva l'injection su c9 dimostrata dall'harness separato.
Le analisi statiche restano indicatori. Il fuzzer copre quattro target e
produce input malformati accettati e riflessioni, senza crash: non sono
exploit confermati. L'accordo P2 14/14 è una replica cieca assistita da AI,
non una ground truth umana indipendente. Guard e fuzzer non sono stati
eseguiti sulle cinque configurazioni SSRF.
[Risultati Guard/fuzzer](Exp3/guard_e_fuzzer/RISULTATI.md),
[risultati P2](Exp3/revisione_descrizioni_P2/RISULTATI.md).
