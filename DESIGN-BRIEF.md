# Sonoteca — Design Brief per Claude Design

> Prompt di recap da incollare a Claude Design. Allegato: lo zip del progetto (`index.html` è il file principale, tutto il resto è contenuto/contesto).

## Cosa è
**Sonoteca** è un "corso di musica personale": una web app a pagina singola dove ogni brano che ascolto viene analizzato a fondo (genere, tonalità, BPM, voce, strumenti, scena storica) e arricchito con **mood**, **playlist contestuali**, **infografiche** e **diagrammi di evoluzione** di genere e artista. Serve a imparare la musica partendo da quello che ascolto davvero. È in italiano.

## Obiettivo del lavoro di design
Dare alla Sonoteca un'**identità visiva forte e una UI curata**, mantenendo tutte le funzionalità esistenti. Oggi il look è "Spotify-like" fatto a mano: funziona ma è grezzo. Voglio qualcosa di **bello, allegro, colorato e moderno**, leggibile e divertente da sfogliare, non un muro di testo.

## Stato attuale (cosa esiste già)
File unico **`index.html`** autosufficiente: HTML + CSS + JavaScript vanilla, **nessun framework, nessuna build, nessuna dipendenza esterna** (le copertine arrivano da URL di Spotify `i.scdn.co`). Dati dei brani in un array JS dentro la pagina.

Funzionalità attuali:
- **Brani**: griglia di card con copertina, hover con tasto play (anteprima audio 30s), filtri per genere/epoca/tonalità/mood.
- **Scheda brano (modal)**: copertina grande, pulsante Spotify, blocco "Mood & contesto", tabella tecnica, **infografiche** (quadrante BPM, badge tonalità maggiore/minore, barre di carattere energia/ballabilità/intimità/oscurità, "anatomia del brano" a segmenti, icone strumenti), analisi testuali (voce, produzione, armonia, scena), playlist consigliata, e **pannelli a scomparsa "Evoluzione"**: percorso del genere (radici → ere → diffusione nel mondo) e genealogia dell'artista (influenze → artista → eredità + fasi).
- **Mood**: card colorate per atmosfera, cliccabili per filtrare.
- **Timeline**: brani su linea del tempo per epoca.
- **Stats**: dashboard con numeri chiave e grafici a barre.
- **Quiz**: ear-training (maggiore/minore, indovina BPM, abbina scena) con anteprima audio.
- **Lezioni** e **Glossario**: contenuti testuali.
- **Mini-player** in basso con equalizzatore animato durante l'anteprima.
- **Tema chiaro/scuro** con toggle (preferenza salvata), sidebar di navigazione che su mobile diventa barra in basso. Responsive.

## Cosa chiedo a Claude Design
1. **Identità**: logo/wordmark "Sonoteca", palette coerente (oggi accenti viola/rosa/arancio/verde + verde Spotify per i pulsanti d'ascolto), set tipografico, iconografia.
2. **Restyling dei componenti** mantenendo la struttura: card brano, scheda/modal, infografiche (gauge, meter, anatomia, badge), diagrammi evoluzione (timeline a tappe colorate, genealogia a 3 colonne), mood card, dashboard, quiz, mini-player, sidebar/bottom-nav.
3. **Due temi** chiaro e scuro entrambi curati; il chiaro deve restare allegro e colorato (NON tutto bianco).
4. **Micro-interazioni e animazioni** eleganti (hover, comparse, equalizzatore, transizioni di vista).
5. **Mobile-first**: deve essere bellissimo anche su telefono (9:16).
6. **Più "infografica" che testo**: separare i concetti in moduli leggibili a colpo d'occhio.

## Vincoli tecnici (importante)
- Deve restare un **singolo file `index.html` autosufficiente**, apribile offline e pubblicabile come pagina statica (verrà messo su un VPS).
- **Niente build tool, niente framework pesanti, niente CDN obbligatori** (eventuali font/icone vanno inglobati o resi opzionali con fallback).
- **Non modificare la struttura dati né la logica JS** (array `BRANI`, `GENRE_EVO`, `ARTIST_EVO`, funzioni `render`, `openSheet`, `genreEvoHTML`, `artistEvoHTML`, `renderStats`, `renderTimeline`, `newQuiz`, `setTheme`…): lavorare su HTML/CSS e, se serve, sul markup generato, ma mantenendo nomi e comportamento.
- Mantenere il **tema chiaro/scuro via `[data-theme]`** e le variabili CSS.
- Il progetto cresce nel tempo (si aggiungono brani): il design deve scalare da 3 a decine di brani.

## Deliverable desiderati
- Un `index.html` ridisegnato (o un set di CSS/markup da integrare) pronto all'uso.
- Eventuale mini design system (palette, tipografia, componenti) come riferimento.

## Riferimenti / mood
Spotify e Apple Music per la struttura; Bandcamp/riviste musicali per il calore editoriale. Tono: **allegro, colorato, contemporaneo, premium ma giocoso**. Pubblico: una persona curiosa che vuole imparare la musica divertendosi.

---
*Allegato: `Sonoteca-progetto.zip` — contiene `index.html` (app), le schede in Markdown, glossario, lezioni e questo brief.*
