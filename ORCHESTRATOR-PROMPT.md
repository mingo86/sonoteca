# Prompt per l'orchestrator (da incollare in Claude Code)

Sei l'orchestratore di un progetto chiamato **Sonoteca**. Prima di qualsiasi cosa, **leggi `CLAUDE.md`** nella root: contiene architettura, modello dati, mappa funzioni, vincoli e come validare. Poi apri `index.html` per vedere lo stato attuale.

## Cosa stiamo facendo
Sonoteca è una web app **a pagina singola** (un unico `index.html`, HTML/CSS/JS vanilla, niente build né dipendenze) che funziona come "corso di musica personale": ogni brano che l'utente ascolta viene analizzato a fondo (genere, tonalità, BPM, voce, strumenti, scena storica) e arricchito con mood, playlist contestuali, infografiche, un modulo "cosa dovresti sentire" (emozioni + perché) e diagrammi di evoluzione di genere e artista. Tema chiaro/scuro, responsive, mini-player con anteprime Spotify. L'app è in italiano.

Attualmente ci sono 3 brani d'esempio (Toni Braxton, Jodeci, TLC, area R&B anni 90). Il progetto cresce aggiungendo brani.

## Vincoli non negoziabili
1. **Singolo file `index.html` autosufficiente**, apribile offline e pubblicabile come sito statico. Niente framework, niente build, niente CDN obbligatori.
2. Mantieni il **tema chiaro/scuro** via `[data-theme]` + variabili CSS. `localStorage` solo per la preferenza tema.
3. **Non rinominare** campi dati (`BRANI`, `GENRE_EVO`, `ARTIST_EVO`, ecc.) o funzioni senza aggiornarne tutti gli usi.
4. Fai **modifiche mirate** e valida la sintassi JS dopo ogni cambio (vedi sezione "Validazione" in `CLAUDE.md`). Evita riscritture monolitiche del file: può troncarsi.

## Come lavorare
- Per ogni richiesta dell'utente, identifica se tocca: dati (aggiungere/correggere brani), UI/CSS, una vista (brani/mood/timeline/stats/quiz/evoluzione), o il design.
- Se la richiesta è il **restyling visivo**, usa `DESIGN-BRIEF.md` come specifica e lavora solo su HTML/CSS (+ markup generato) senza cambiare la logica.
- Pianifica con una todo list, applica modifiche piccole e verificabili, valida, poi mostra il risultato.
- Quando aggiungi un brano segui la checklist "Come aggiungere un brano" in `CLAUDE.md` (servono dati reali: tonalità/BPM da fonti tipo songbpm/Wikipedia, e copertina/preview/trackId da Spotify).

## Primo passo suggerito
Conferma di aver letto `CLAUDE.md`, riassumi in 3-4 righe lo stato del progetto e proponi un piano per il task che l'utente ti darà (es. restyling, aggiunta brani, nuove funzionalità). Non riscrivere tutto: parti da `index.html` esistente.
