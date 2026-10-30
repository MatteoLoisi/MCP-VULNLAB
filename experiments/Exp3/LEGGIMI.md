# Come leggere Exp3

Il confronto richiesto usa MCTS 0.1.4 e quattordici target STDIO. [Tabella con le quattro colonne originali](confronto_14_target/exp3_confronto_target.csv). Gli allarmi MCTS sono indicatori testuali generici, non effetti dimostrati. Il [dettaglio strutturato](confronto_14_target/exp3_dettaglio_evidenze.csv) include anche Cisco 4.8.6 YARA e MCP-Shield 1.0.4 senza Claude. I percorsi nei CSV sono relativi alla radice del dossier.

Le sonde hanno tre effetti confermati e tre prove non certificabili. c2 e c10 hanno comunque indicatori P2 testuali: il limite riguarda le rispettive sonde di effetto. R1 ha una copertura incompleta. I negativi sono limitati ai controlli dichiarati.

Le cinque configurazioni SSRF sono separate dal confronto metadata: tre effetti osservati e due negativi entro il piano. I rifiuti della policy applicativa sono distinti dai blocchi middleware. La diagnostica usa dodici descrizioni sintetiche e trentasei scansioni; le matrici sono ricostruite dalle decisioni P2 archiviate, senza nuova etichettatura indipendente.

Versioni, licenza open source e stato della manutenzione sono documentati nello snapshot del 17 ottobre 2026; nessun controllo online corrente è dichiarato. Tutti i raw e i log estratti hanno una voce nell’indice delle evidenze.

Per le cinque configurazioni SSRF, Cisco e MCP-Shield non sono stati eseguiti; anche RedCell e Mcpwn non hanno misure su quei cinque prodotti/versioni. MCTS è presente solo come controllo dei metadati, senza esito SSRF. La [copertura per scanner](configurazioni_ssrf/LEGGIMI.md) distingue non eseguito e non applicabile. Il callback RedCell riguarda un fetch locale separato.

Le altre campagne di scanner esterni sono [RedCell/Mcpwn](sonde_esterne/RISULTATI_SONDE_ESTERNE.md) e [MCP Guard/fuzzer](guard_e_fuzzer/RISULTATI.md). La [revisione cieca delle descrizioni](revisione_descrizioni_P2/RISULTATI.md) è assistita da AI e non misura nuovi effetti.

| Strumento | Target effettivamente esaminati | Tipo di risultato |
|---|---|---|
| MCTS 0.1.4 | 14 STDIO; cinque configurazioni SSRF solo come controllo dei metadati | Indicatori testuali; nessun controllo di effetto SSRF |
| Cisco 4.8.6, YARA | 14 STDIO | Indicatori testuali |
| MCP-Shield 1.0.4 | 14 STDIO | Indicatori testuali |
| RedCell 0.1.0 | Dieci DVMCP, due controlli e un fetch locale | Indicatori testuali e prove attive di lettura, comandi e callback |
| Mcpwn 1.0 | c3, c8, c9 e due controlli, con profili differenti | Firme di esecuzione e indicatori di tempi, protocollo e prompt |
| MCP Guard 2.0.0 | c3, c8 e c9; tentativo Docker ufficiale su c8 incompleto | Analisi statica e prove dinamiche in container esterno |
| MCP Server Fuzzer 0.7.0 | c2, R1-filesystem, R2-memory, R3-sequential-thinking | Robustezza del protocollo, input accettati e riflessioni |

La diagnostica comune sui dodici testi sintetici riguarda MCTS, Cisco e
MCP-Shield. Le altre campagne hanno campioni e profili diversi: i conteggi
non sono confronti a parità di condizioni. La nostra P6 è la sola misura
di effetto presente su tutti e cinque i prodotti/versioni della matrice SSRF.
