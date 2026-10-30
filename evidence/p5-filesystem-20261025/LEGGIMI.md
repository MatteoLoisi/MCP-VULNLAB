# P5 su Filesystem — sweep congelato del 25 ottobre 2026

Un tentativo completo su `R1-filesystem`, protocollo nativo P5, con canary sintetico fuori dalla root ammessa.

## Esecuzione

- Comando: `python tools/exp3/run_probes.py --only R1-filesystem --probes P5 --allowed-root "$LOCALAPPDATA/Temp/mcptargets/fsroot" --p5-max-attempts 4096 --out output/r6-p5-filesystem-20261025-01/probes`
- Tentativo: `acd01232676f408b92b65422fbbdd0fa`
- Finding: **secure** — traversal via tool non riuscito (187 tentativi su 17 argomenti path-like, 1 tool schema-opachi; prefisso cercato: 'EXP3_CANARY_acd01232676f408b92b65422fbbdd0fa_0dd5a50b330541baa2391812e50d512b')
- Unità pianificate 17, coperte 17, eseguite 17, mancanti **0**
- Chiamate al target: 187; tetto dichiarato 4096; sweep `complete=True`
- Risposte non consegnate al corpo del tool: 0; errori di trasporto: 0
- Tool con schema opaco (argomenti convenzionali): 1

## Canary

- Percorso: `D:\docker_proget\V4\output\r6-p5-filesystem-20261025-01\probes\attempts\acd01232676f408b92b65422fbbdd0fa\canary.txt`
- SHA-256: `f47b7fbece31db86b8ee9c5bdf8bb9e6c1cd3ac710654a6934a3af14aa2c24ec`
- Root ammessa: `C:\Users\ML\AppData\Local\Temp\mcptargets\fsroot`; fuori root: **True**; read-back: **True**

## Esiti per caso

| Esito | Chiamate |
|---|---:|
| `delivered_no_effect` | 21 |
| `tool_error` | 166 |

`tool_error` = il tool ha girato e ha rifiutato (access denied/ENOENT); `delivered_no_effect` = chiamata consegnata senza canary nella risposta (18 su `list_allowed_directories`, che ignora i nomi convenzionali, e 3 su `read_multiple_files`, che riporta il rifiuto per-file come testo con `isError=false`); nessun `traversal_hit`.

## Unità sondate

| Tool | Argomento |
|---|---|
| `directory_tree` | `excludePatterns` |
| `directory_tree` | `path` |
| `get_file_info` | `path` |
| `list_allowed_directories` | `directory` |
| `list_allowed_directories` | `file` |
| `list_allowed_directories` | `filename` |
| `list_allowed_directories` | `name` |
| `list_allowed_directories` | `path` |
| `list_allowed_directories` | `uri` |
| `list_directory` | `path` |
| `list_directory_with_sizes` | `path` |
| `read_file` | `path` |
| `read_media_file` | `path` |
| `read_multiple_files` | `paths` |
| `read_text_file` | `path` |
| `search_files` | `excludePatterns` |
| `search_files` | `path` |

## Limiti dichiarati

- Il caso `list_allowed_directories` non ha proprietà nello schema: P5 usa i nomi convenzionali e il tool li ignora. È copertura dichiarata come `opaque`, non una difesa.
- `read_multiple_files` restituisce `../canary.txt: Error - Access denied ...` come testo con `isError=false`: l'audit lo conta come consegnato e rifiutato dal tool, non come effetto.
- Le forme `../`, `..\`, `%2e%2e%2f` a profondità 1–3, i path assoluti e i `file://` sono quelli generati dal protocollo nativo; il caso è congelato dal codice e dagli hash dei sorgenti, non da una lista scritta a mano.
- **Ripresa dei soli casi mancanti non implementata:** se il tetto interrompe lo sweep, il finding resta `incomplete` e serve un nuovo tentativo con tetto dichiarato più alto, non una ripresa selettiva. Qui non è servita: 0 mancanti.
- Un `secure` vale per questo protocollo e questo canary, non per ogni possibile percorso del server.

## File

| File | SHA-256 |
|---|---|
| `audit/audit.json` | `d15620d85134f59a09029d7411c1cfc1c4e0bd42d8c59719d19cd7e4c17a5a72` |
| `audit/filesystem_p5.csv` | `821b9b799ed3adece176889992e974a0c42255c6930a20b352376465871c71bb` |
| `probes/battery-npm-filesystem.json` | `5fc3715916d78fbc150ba77289b048a125ac6d038718960c94921f92f180fadf` |
| `probes/battery-npm-filesystem.status.json` | `7114eefbe54858566aebe3d75c2cba29f0b7062284c277567a0c6f0fa81075be` |

Il tentativo completo (contesto, cattura, report, stato, log, canary) è in `probes/attempts/`; `audit/filesystem_p5.csv` contiene una riga per chiamata.
