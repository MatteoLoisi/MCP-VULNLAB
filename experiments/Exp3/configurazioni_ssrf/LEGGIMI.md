# Cinque configurazioni SSRF: cosa è stato misurato

La campagna del 20 ottobre 2026 usa cinque configurazioni reali di
webresearch/open-websearch. La nostra sonda P6 ha prodotto tre effetti
SSRF locali e due negativi entro il piano, ciascuno con 30 richieste
respinte dalla policy applicativa.

| Strumento | Esecuzione sulle cinque configurazioni | Esito SSRF disponibile |
|---|---|---|
| P6, nostra sonda | Sì, su tutte e cinque | Tre effetti osservati e due negativi entro il piano |
| MCTS 0.1.4 | Sì, come controllo dei metadati | Non applicabile: il controllo usato non misura SSRF |
| Cisco 4.8.6, YARA | Non eseguita su queste cinque configurazioni | Non misurato |
| MCP-Shield 1.0.4 | Non eseguita su queste cinque configurazioni | Non misurato |
| RedCell 0.1.0 | Non eseguita su queste cinque configurazioni | Non misurato; esiste un callback positivo su un fetch locale separato |
| Mcpwn 1.0 | Non eseguita su queste cinque configurazioni | Non misurato |
| MCP Guard 2.0.0 | Non eseguita su queste cinque configurazioni | Non misurato |
| MCP Server Fuzzer 0.7.0 | Non eseguita su queste cinque configurazioni | Non misurato |

**Non eseguito** indica che manca una misura, senza affermare che lo
strumento sia incapace di effettuare quel controllo. **Non applicabile**
indica che il controllo effettivamente usato non produce un esito SSRF.
Nessuna delle due voci equivale a "nessuna vulnerabilità rilevata".

Il CSV [risultati_ssrf.csv](risultati_ssrf.csv) contiene una colonna distinta
per ogni scanner. La precedente colonna generica `scanner_ssrf` si riferiva
solo a MCTS ed è ora denominata `mcts_ssrf`. I risultati P6, i contatori e
i collegamenti ai raw conservano i valori precedenti. I percorsi nelle
celle sono relativi alla radice RISULTATI_V3.

Il confronto Cisco/MCP-Shield sui 14 target STDIO è nella cartella
[confronto_14_target](../confronto_14_target/). Il pilot RedCell/Mcpwn del
19 ottobre è documentato [separatamente](../sonde_esterne/RISULTATI_SONDE_ESTERNE.md).
Il fetch locale di quel pilot non è uno dei cinque prodotti/versioni
di questa matrice: la sua prova non può riempire queste celle.

Resta quindi non misurato il confronto SSRF degli scanner esterni sugli
stessi cinque prodotti/versioni. Questa integrazione esplicita la copertura
esistente e non introduce nuove esecuzioni.

I cinque report originali del controllo MCTS sui metadati sono nella cartella [controllo_metadati_mcts](controllo_metadati_mcts/); la colonna `mcts_metadata_report` del CSV li collega ai rispettivi target. Gli eventuali allarmi di quei report non costituiscono rilevazioni SSRF.
