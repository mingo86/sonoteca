# CLAUDE.md — Sonoteca

Contesto per lavorare su questo progetto con Claude Code.

## Cos'è
**Sonoteca**: web app a pagina singola, un "corso di musica personale". Ogni brano ascoltato viene analizzato (genere, tonalità, BPM, voce, strumenti, scena) e arricchito con mood, playlist contestuali, infografiche, emozioni e diagrammi di evoluzione di genere/artista. Lingua: italiano.

## File
- **`index.html`** — l'intera app (HTML + CSS + JS vanilla in un unico file). È l'unico file che conta per il deploy.
- `schede/*.md` — analisi dei brani in Markdown (contenuto sorgente / archivio leggibile).
- `glossario.md`, `lezioni/*.md` — contenuti testuali di riferimento.
- `DESIGN-BRIEF.md` — brief per il restyling visivo.

## Architettura di `index.html`
- **Nessun framework, nessuna build, nessuna dipendenza esterna.** Solo HTML/CSS/JS vanilla. Le copertine sono URL remoti di Spotify (`i.scdn.co`); le anteprime audio sono mp3 di Spotify (`p.scdn.co`).
- **Temi**: variabili CSS in `:root` (scuro, default) e `[data-theme="light"]` (chiaro). Il toggle salva la scelta in `localStorage` (`sonoteca-theme`). NON usare localStorage per altro.
- **Navigazione**: sidebar con `.navbtn[data-tab]`; `switchTab(t)` mostra/nasconde le `<section id="tab-...">`. Su mobile (<780px) la sidebar diventa barra in basso.
- **Dati**: array JS dentro lo script (`BRANI`, `LEZIONI`, `GLOSSARIO`, `INFO`, `GENRE_EVO`, `ARTIST_EVO`).
- **Render**: tutto è generato via template literal in funzioni `render*`/`*HTML`.

### Modello dati — oggetto in `BRANI`
```js
{
  titolo, artista, data, fonte,
  cover,        // URL i.scdn.co (o "" => fallback iniziali)
  trackId,      // id Spotify per il link traccia
  preview,      // URL mp3 30s (o "" => niente play)
  oneline, genere, epoca, key, bpm, metro, durata, produttore,
  bpmNum,       // numero, per il quadrante BPM
  metriche:{Energia, Ballabilità, Intimità, Oscurità}, // 0-100
  struttura:[["Intro",8],...],   // anatomia, somma ~100
  strumentiList:["Voce","Batteria",...],
  emozioni:[{ic,emo,val,col,perche}], // "cosa dovresti sentire"
  voce, strumenti, armonia, scena,    // testi analisi
  mood:[...], contesto,                // mood + paragrafo atmosfera
  playlist:[["Titolo","Artista"],...], // consigliati (link ricerca Spotify)
  ascolta:[...], simili:[...], tags:[...]
}
```
`GENRE_EVO` = {nome, radici[], timeline[], mondo[]}. `ARTIST_EVO` = { "Nome":{influenze[], eredita[], fasi[[anno,desc]], nota} }.

### Mappa funzioni principali
- `render()` — griglia brani con filtri. `openSheet(i)` — modal scheda. `closeModal()`.
- `infografica(b)` — gauge BPM + badge tonalità + metriche + anatomia + strumenti.
- `emozioniHTML(b)` — modulo "Cosa dovresti sentire".
- `genreEvoHTML(b)` / `artistEvoHTML(name)` / `toggleEvo(k)` — pannelli evoluzione nella scheda.
- `renderMood/renderTimeline/renderStats/renderLezioni/renderGlossario/renderInfo`.
- `newQuiz()/answer()/qWrap()` — ear-training.
- `playPreview(i)/togglePlay()` — mini-player. `setTheme(t)/toggleTheme()` — temi.

## Come aggiungere un brano (task ricorrente)
1. Recupera dati reali: tonalità/BPM/produttore (fonti tipo songbpm, tunebat, Wikipedia) e copertina/preview/trackId (da Spotify).
2. Aggiungi un oggetto **in cima** all'array `BRANI` con TUTTI i campi sopra (rispetta i nomi).
3. Facoltativo: crea la scheda `schede/AAAA-MM-GG_Artista_Titolo.md` (segui `schede/_TEMPLATE.md`).
4. Se l'artista è nuovo, aggiungi una voce a `ARTIST_EVO`.
5. Glossario/lezioni si espandono se compaiono termini nuovi.

## Validazione (niente test runner)
Controlla che lo script sia sintatticamente valido prima di salvare, es. estrai il blocco `<script>` ed esegui con stub del DOM in Node:
```bash
node -e "const h=require('fs').readFileSync('index.html','utf8');new Function(h.match(/<script>([\s\S]*?)<\/script>/)[1]); console.log('parse OK')"
```
(Per un test runtime più completo, stubbare `document`/`Audio` e chiamare `openSheet(0)`, `renderStats()`, ecc.)

## Vincoli (importante)
- Deve restare **un singolo `index.html` autosufficiente**, apribile offline e pubblicabile come pagina statica.
- **Niente build, niente framework, niente CDN obbligatori.**
- Mantenere temi chiaro/scuro via `[data-theme]` e le variabili CSS.
- Non rinominare i campi dati né le funzioni senza aggiornare tutti gli usi.
- Il file può troncarsi se scritto in un colpo solo troppo grande: preferire modifiche mirate.

## Deploy
È un file statico. Opzioni: trascinarlo su Netlify Drop; oppure copiarlo nella web root di un VPS (nginx) — vedi note del progetto. Su Vercel funziona come sito statico.

## Idee / roadmap
Copertine nelle playlist consigliate; esempi cliccabili (ascolto) dentro i diagrammi evoluzione; emozione dominante come chip sulle card; più brani in archivio; mappa del mondo per le scene regionali; restyling visivo (vedi `DESIGN-BRIEF.md`).
