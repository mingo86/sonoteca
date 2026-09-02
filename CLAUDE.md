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
- **Temi**: variabili CSS in `:root` (chiaro crema, default) e `[data-theme="dark"]` (navy). Il toggle salva la scelta in `localStorage` (`sonoteca-theme-v2`). I preferiti stanno in `localStorage` (`sonoteca-fav`, array di trackId). Non usare localStorage per altro.
- **Font**: EB Garamond (titoli) e Inter (testo) sono incorporati in base64 nei due `@font-face` all'inizio di `<style>`. Devono restare DENTRO `<style>` e la prima riga del file deve restare `<!doctype html>` (una versione precedente li aveva inseriti dentro il doctype: pagina in quirks mode e font mai caricati).
- **Navigazione**: sidebar con `.navbtn[data-tab]`; `switchTab(t)` mostra/nasconde le `<section id="tab-...">`. Su mobile (<780px) la sidebar diventa barra in basso.
- **Dati**: array JS dentro lo script (`BRANI`, `LEZIONI`, `GLOSSARIO`, `INFO`, `GENRE_EVO`, `ARTIST_EVO`, più `LYRICS` e `STEMMAP` indicizzati per trackId, e `GENRE_FAMILIES`/`GENRE_GRAD` per famiglie e colori di genere).
- **Stato dei dati (set 2026)**: ~507 brani. Solo una manciata (i primi dell'array, con `preview`, `produttore`, `playlist` valorizzati) sono analisi curate a mano; gli altri ~500 vengono da import in blocco dalle playlist Spotify, con testi generati da template (`oneline` tipo "Nu disco in…", frasi con "Strumentalità 0.72") e spesso senza cover, preview, produttore, playlist e struttura. Tenerne conto: le funzioni che dipendono da `preview` (quiz audio, play-along) funzionano solo sui brani curati.
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
  ascolta:[...], simili:[...], tags:[...],
  camelot:"8A"    // presente sui brani importati in blocco
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
- `renderCostellazione()` — grafo a forze su canvas. `renderMixFor()` — vista Mix (compatibilità Camelot/BPM). `paInit()` — play-along sull'anteprima. `toggleFav()/favSet()` — preferiti.

## Come aggiungere un brano (task ricorrente)
1. Recupera dati reali: tonalità/BPM/produttore (fonti tipo songbpm, tunebat, Wikipedia) e copertina/preview/trackId (da Spotify).
2. Aggiungi un oggetto **in cima** all'array `BRANI` con TUTTI i campi sopra (rispetta i nomi).
3. Facoltativo: crea la scheda `schede/AAAA-MM-GG_Artista_Titolo.md` (segui `schede/_TEMPLATE.md`).
4. Se l'artista è nuovo, aggiungi una voce a `ARTIST_EVO`.
5. Glossario/lezioni si espandono se compaiono termini nuovi.

## Validazione (niente test runner)
C'è `_smoke.js` in cartella (non versionato: `.gitignore` esclude `_*`) che stubba il DOM e chiama le funzioni principali: `node _smoke.js`. Come minimo controlla che lo script sia sintatticamente valido prima di salvare, es. estrai il blocco `<script>` ed esegui con stub del DOM in Node:
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
GitHub Pages dal branch `main` del repo `mingo86/sonoteca`: https://mingo86.github.io/sonoteca/. Dopo ogni modifica approvata: commit + push (il sito si aggiorna da solo in 1-2 minuti). Per provare in locale: `python -m http.server 8099 --directory Corso-Musica` dalla cartella padre (vedi `.claude/launch.json`).

In cartella ci sono anche `index.redesign.html`, `_index.bak.html` e decine di `_*.json` (output della pipeline di import da Spotify, non versionati): sono residui, non vanno usati come sorgente.

## Idee / roadmap (priorità da valutazione del 2 set 2026)
1. Layout mobile: sotto 780px la sidebar deve diventare barra in basso (oggi resta un pannello a tutto schermo sopra il contenuto).
2. Rendering progressivo della griglia (oggi 507 card in un colpo, ~17k nodi DOM).
3. Campo `livello: "curato" | "auto"` sui brani, badge e filtro; testi automatici etichettati come tali.
4. Estrarre i dati in file separati (`data/brani.js`, `data/evo.js`) caricati con `<script src>`, più script di validazione (campi obbligatori, duplicati, genere in tassonomia).
5. Normalizzare i generi (266 stringhe distinte) sulle famiglie di `GENRE_FAMILIES`; separare gli artisti multipli ("A, B") così le voci `ARTIST_EVO` si agganciano.
6. Recuperare cover e anteprime mancanti (API pubbliche Deezer/iTunes).
7. Poi: copertine nelle playlist consigliate; esempi cliccabili nei diagrammi evoluzione; emozione dominante come chip sulle card; mappa del mondo per le scene regionali.
