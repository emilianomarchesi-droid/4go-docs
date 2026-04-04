# Prompt base — Progetti Claude.ai per operatori 4&GO

> Copia questo prompt nel campo **"Istruzioni progetto"** di ogni progetto Claude.ai.
> Allega il file `catalogo-pacchetti.md` con i pacchetti aggiornati da DB.
> Aggiungi in fondo la **riga personalizzata** specifica per ogni operatore.

---

## PROMPT BASE (istruzioni progetto — unico progetto, 3 chat distinte)

```
Sei l'assistente AI interno di 4&GO FourTravel, agenzia viaggi di Senago (Milano).

AGENZIA:
- Via Alessandro Volta 331 – 20030 Senago (MI)
- Tel: 02 9108 4274 | WhatsApp: +39 366 260 8567
- info@fourgo.it | fourgo.it
- Affiliata Geo Travel Network (Welcome Geo)
- Partner ufficiali: Alpitour, Francorosso, Turisanda, Veratour, Bravo Club, Costa Crociere, MSC Crociere
- Specializzata in viaggi su misura, safari, viaggi di nozze, tour culturali, Fly&Drive, crociere

TEAM:
- Massimiliano Schilirò — Titolare
- Alessia — Consulente Viaggi
- Inga — Consulente Viaggi
- Emiliano — Sviluppatore del sito (non opera in agenzia, contattare solo per problemi tecnici)

TUO RUOLO:
- Aiutare nella gestione delle richieste clienti (email, WhatsApp, telefono)
- Redigere preventivi, email, risposte clienti in modo caldo e professionale
- Suggerire itinerari e soluzioni di viaggio dal catalogo allegato
- Supportare la pianificazione operativa quotidiana
- Rispondere a domande sui pacchetti con informazioni precise su itinerario, inclusi/esclusi, mappa

CATALOGO: vedi file allegato con i pacchetti aggiornati

STRUTTURA PACCHETTO (come leggere il catalogo):
Ogni pacchetto contiene:
- title, subtitle, destination, durationDays/Nights, priceFrom
- itinerary: array di giorni con titolo (formato "Città - Attività") e descrizione dettagliata
- mapCities: città per giorno separate da virgola (usate per la mappa interattiva del sito)
- included/excluded: cosa è compreso o meno
- whyChoose: 5 motivi per scegliere il viaggio
- availability: date e periodi disponibili
- transport, meals, supplements, notes

REGOLE OPERATIVE:
- Parla sempre in italiano salvo diversa indicazione
- Non inventare prezzi o disponibilità — usa solo i dati del catalogo allegato
- Se non hai l'informazione, di' "verifico con il team"
- Tono: caldo, professionale, esperto di viaggi — mai robotico
- Non condividere dati personali dei clienti tra conversazioni diverse
- Per prezzi speciali, sconti o decisioni commerciali importanti: coinvolgi Massimiliano
- I prezzi nel catalogo sono "a partire da" — il prezzo finale dipende da periodo e composizione gruppo

RISPOSTA AI CLIENTI:
- Ogni risposta finisce sempre con UNA sola domanda per mantenere il dialogo
- Raccoglie progressivamente: destinazione → periodo → persone → budget
- Non conferma mai prezzi o disponibilità in modo definitivo
- Se il cliente vuole prenotare: "Passo la tua richiesta a [operatore] che ti contatta entro oggi"
- Per domande sull'itinerario: usa i dati precisi dal campo itinerary del pacchetto
- Per domande sulla mappa: le città visibili nella mappa sono quelle in mapCities, una per giorno

ESCALATION TECNICA A EMILIANO:
Se riscontri un bug, un errore nel sito o hai bisogno di una modifica tecnica,
NON scrivere "contatta Emiliano" in modo generico.
Genera invece questo messaggio strutturato pronto da incollare nella chat con Emiliano su claude.ai:

---
🛠️ SEGNALAZIONE TECNICA 4GO

👤 Operatore: [nome]
📍 Pagina/sezione: [es. /admin/pacchetti → modifica pacchetto ID xyz]
🔴 Problema: [descrizione precisa di cosa è successo]
✅ Comportamento atteso: [cosa dovrebbe fare invece]
🔁 Frequenza: [sempre / a volte / una volta sola]
📱 Dispositivo/browser: [es. iPhone Safari / Windows Chrome]
🏷️ Tipo: [Bug | Modifica | Nuova funzionalità]
📝 Note extra: [tutto ciò che può aiutare a capire il problema]
---

Emiliano ha accesso diretto al codice e al repo — con queste info risolve senza avanti-e-indietro.
```

---

## SETUP PROGETTO

1. Vai su [claude.ai](https://claude.ai) → **Progetti** → **Nuovo progetto**
2. Nome: `4GO FourTravel`
3. **Istruzioni progetto** → incolla il PROMPT BASE sopra (uguale per tutti)
4. **Aggiungi file** → carica `prompt-agenzia.md` (questo file)
5. Crea **3 chat** dentro il progetto, una per operatore:
   - Chat `Massimo` — prima cosa da scrivere: *"Sono Massimiliano, titolare. Ho pieno accesso a prezzi, fornitori e decisioni commerciali."*
   - Chat `Alessia` — *"Sono Alessia, consulente viaggi. Per prezzi speciali coinvolgo Massimiliano."*
   - Chat `Inga` — *"Sono Inga, consulente viaggi. Per prezzi speciali coinvolgo Massimiliano."*
6. Claude impara il contesto di ciascun operatore e lo mantiene nelle rispettive chat

---

## SITO WEB fourgo.it

Il sito è sviluppato e manutenuto da Emiliano su Next.js 15 + Prisma + Neon + Vercel Pro.

### Funzionalità principali

**Catalogo e pacchetti**
- Ogni pacchetto ha una pagina pubblica con descrizione, itinerario filtrable per città, mappa interattiva Google Maps
- La mappa mostra un checkpoint numerato per ogni giorno dell'itinerario (coordinate salvate nel DB)
- I checkpoint sono geocodificati server-side al salvataggio — mai nel browser

**Import pacchetti da PDF**
Flusso AI per creare un nuovo pacchetto:
1. Operatore carica PDF in Admin → AI Pacchetti
2. **Claude Sonnet** estrae: metadata (titolo, prezzo, durata, inclusi/esclusi), itinerario completo con descrizioni dettagliate, luoghi geografici per giorno
3. **Claude Haiku** estrae: città per la mappa (una per giorno) e dati volo se presenti
4. **Google Maps API server-side** geocodifica immediatamente tutte le città → coordinate salvate nel DB
5. **Telegram** notifica l'operatore che ha caricato con report: titolo, prezzo, città geocodificate, avvisi su dati mancanti
6. Operatore verifica il report, apre l'admin, controlla e pubblica

**Formato itinerario**
Il titolo di ogni giorno segue il formato `"Città - Attività principale"`.
La prima parola è sempre la città geografica del giorno — usata dal Telegram bot per rispondere a domande precise.

**Campo mapCities**
Stringa di città separate da virgola, una per giorno (es: `Siviglia, Siviglia, Granada, Córdoba, Malaga`).
Ogni salvataggio del pacchetto rigenera automaticamente le coordinate nel DB tramite Google Maps API.

**Email AI** — info@fourgo.it gestita da Claude Haiku
- Risponde automaticamente in IT/EN/FR/DE/ES
- Escalation a massimo@fourgo.it con label criticità (🔥/🟡/🟢) dopo 4 messaggi o parole chiave di acquisto
- Non conferma mai prezzi o disponibilità

**Bot Telegram @FourGoTravelBot** — assistenza post-vendita
- Accede all'itinerario completo del pacchetto acquistato
- Risponde a domande su giorni specifici, attrazioni, mappa del percorso
- Emergenze: cliente seleziona operatore → notifica + 3×5min retry

**Prenotazione online**
- Pagamento Stripe (carta) + PayPal
- Conferma automatica con bookingCode, link tracking, info Telegram bot

**Admin panel** /admin
- Accesso solo con NextAuth + MFA TOTP
- Import PDF, gestione pacchetti, blog, email AI, calendario, newsletter, social, analytics
- Monitor attività operatori: visite pagine e azioni tracciate per Massimo/Alessia/Inga

**Telegram Chat ID operatori**
Ogni operatore ha il proprio Chat ID Telegram salvato nel profilo admin (Admin → Utenti).
Usato per notifiche personalizzate: import pacchetto, emergenze, segnalazioni.
Per ottenere il proprio ID: aprire @userinfobot su Telegram.

Per segnalazioni tecniche usa il formato strutturato indicato sopra.

---

---

## SESSIONE 2026-04-02 — Sistema Preventivi e Multi Hotel

### Sistema Preventivi (PRV)

**Creazione preventivo** — Admin → AI Generator Preventivi → 4 modalità:
- **Importa PDF**: carica preventivo tour operator → Haiku estrae voci e prezzo totale
- **Genera con AI**: Haiku genera voci realistiche dalla destinazione
- **Manuale**: form libero
- **⚡ Multi Hotel**: carica N PDF hotel → Haiku estrae dati+stelle+pros/cons+rating

**Campi preventivo**: nome/email/telefono cliente, destinazione, data partenza/rientro, acconto %, metodo pagamento (Carta/PayPal/Entrambi/Bonifico/Contanti/Bancomat/Assegno), voci servizi, extra con metodo per-voce, note, gammaUrl.

**Codice**: `PRV-YYYY-XXXXX`

**Flusso PRV:**
1. Operatore crea e verifica il preventivo
2. Clicca "Invia al cliente" → email HTML Outlook-safe con `&euro;`, tabella servizi, acconto, bottone conferma
3. Cliente clicca "Confermo" → sistema genera Stripe checkout session + PayPal → email con link pagamento
4. Stripe webhook intercetta → conversione automatica PRV → 4GO (ManualBooking CONFIRMED + Package BOZZA)
5. Gamma generato automaticamente alla conversione

**Archivia PRV**: bottone 📁 nell'header di ogni card → PRV scompare dalla lista attiva; badge "📁 Archiviati (N)" per visualizzarli.

**Gamma PRV**: bottone "Genera Gamma" → brochure con prezzi e date → apre Gamma in nuova tab e salva link.

---

### Sistema Multi Hotel (PKG)

**Codice**: `PKG-YYYY-XXXXX`

**Flusso PKG:**
1. Carica PDF hotel (drag & drop o click) → Haiku estrae: nome, stelle (cerca su web se mancano), indirizzo, camera, pensione, prezzo, volo, pros, cons, rating (posizione/comfort/qualità/prezzo)
2. Prima pagina di ogni PDF → JPEG → upload Vercel Blob in `4go/pkg-photos/PKG-XXX/hotel-N.jpg`
3. Operatore verifica la pagina `/scegli-pacchetto?code=PKG-XXX` con foto, tabella comparativa, valutazioni, pros/cons
4. Genera Gamma → presentazione con: cover, cosa è incluso, N slide hotel con foto PDF, slide classifica, ultima slide contatti
5. Invia email → tabella hotel con foto Blob + bottone "Vedi le opzioni" + bottone "Guarda la presentazione"
6. Cliente sceglie hotel → status CONFIRMED → link scelta e "Copia link" spariscono
7. Cliente paga → Stripe webhook → auto-conversione PKG → 4GO + pulizia Blob

**Blob cleanup automatico**: al delete PKG o alla scelta del cliente → cartella `4go/pkg-photos/PKG-XXX/` eliminata. Bottone "Pulizia Blob" in admin per cartelle orfane.

**Archivia PKG**: bottone 📁 Archivia su ogni card.

**Pagina /scegli-pacchetto**: sotto layout pubblico con Navbar/Footer del sito, CSS variables tema, foto hotel `objectFit:contain`, tabella comparativa, cerchi score SVG, barre rating, badge "⭐ TOP", bottone "Scelgo questo hotel".

---

### Logo e Gamma

**Logo trasparente**: `public/logo-4go-transparent.png` — PNG con alpha, sfondo rimosso. Usato in tutti i prompt Gamma al posto di `logo-4go-hd.png` (che aveva sfondo nero).

**Ultima slide Gamma**: sfondo NERO, logo centrato max 40% larghezza tutto visibile, testo bianco. Istruzione esplicita nel prompt.

**Polling Gamma**: client-side ogni 3s max 90s via `/api/ai/gamma-poll`. Usa `status === 'completed'` e fallback `https://gamma.app/docs/${id}`.

---

### Struttura cartelle Aruba (info@fourgo.it)

Le email classificate vengono spostate automaticamente via IMAP in:
- `4GO/Spam` → email spam
- `4GO/Scalati` → escalation (cliente pronto all'acquisto)
- `4GO/Gestiti` → POTENTIAL e MANAGED
- `4GO/Chiusi` → thread chiusi
- `4GO/Cortesia` → complimenti/ringraziamenti

**Nota**: le cartelle devono esistere su Aruba (create manualmente una tantum). ✅ Fatto il 2026-04-02.

---

*Ultimo aggiornamento: 2026-04-02*

---

## SESSIONE 2026-04-03 — Documenti Telegram, Bug Fix Build, Emergenze

### Build fix critici

**send-documents/route.ts** — 3 errori sintassi che bloccavano il build:
1. Template literal innestato in `getFirmaAgenzia` dentro `encodeURIComponent()` → SWC chiudeva anticipatamente il template
2. Ternario malformato in `htmlBody` con `}` extra prima di `: ''}`
3. `}` mancante per chiudere la funzione `getFirmaAgenzia`

**Risultato**: tutti i deploy successivi alla sessione 4GO-7 fallivano. Fix applicato nel commit `6328ada`.

---

### Sistema Documenti Telegram (TelegramDocument)

**Flusso completo post-vendita** — Admin → Social AI → tab Telegram:

**Upload documento:**
1. Operatore carica PDF/TXT per la prenotazione del cliente
2. Server salva subito il documento nel DB (senza mappa)
3. **Notifica immediata** `📎 Documento ricevuto ⏳` agli operatori Telegram
4. Estrazione testo + generazione mappa semantica
5. **Notifica finale** `📄 Documento strutturato — N sezioni`

**Estrazione testo (priorità):**
1. `pdf-parse` (nativo, veloce) — se testo ≥ 300 char/pagina → OK
2. Se pdf-parse crasha (`DOMMatrix is not defined` — Vercel Node.js non ha API browser) → **Vision OCR** automatico
3. **Vision OCR**: raw fetch all'API Anthropic con modello `claude-sonnet-4-6` (no beta header per Claude 4.x) — estrae testo da pagine scansionate/immagine

**Strutturazione (semanticMap):**
- `callHaikuForMap()`: Haiku con `max_tokens: 8192`, testo fino a 80.000 char
- Retry automatico con testo dimezzato se JSON troncato/invalido
- Risultato: `{docType, subject, sections: [{category, label, keywords, content}]}`

**Campo botOnly:**
- `true` = solo contesto AI bot, mai inviato come allegato email al cliente
- Auto-true per file `.txt` e `.docx`
- PDF originali: `botOnly=false` (allegabili), TXT estratti: `botOnly=true`
- `send-documents` filtra `botOnly: false` per non inviare doc bot-only al cliente

**Limiti Vercel:**
- Request body max 4.5MB → PDF > 3.2MB: base64 inviato per OCR ma fileUrl non salvato in DB (flag `_largeFile`)
- `maxDuration = 300s` (max Vercel Pro) per tg-documents route
- Client timeout: 280s per upload e remap

**Bottone 🔄 Rigenera mappa:**
- PATCH `remap: true` → ri-estrae testo da `fileUrl` se `extractedText` è vuoto
- Poi rigenera `semanticMap`
- Risposta include `remapResult: {hasText, hasMap, chars, error}`

**UI card documento:**
- Badge `📝 Xk car.` (testo estratto) / `📝 No testo` (PDF scansionato o vuoto)
- Badge `🗂 N sez.` (mappa OK) / `⚠️ No mappa` (fallita)
- Toggle `🤖 Solo bot` / `📎 Allegabile`
- Bottone `+ Prenotazione` con edit inline per associare bookingCode dopo upload
- Tab Telegram si apre automaticamente da `/admin/social?uploadBooking=4GO-...`

**GET documenti slim:** restituisce `extractedText` come lunghezza numerica (non il contenuto) per evitare payload enormi.

**Bot Telegram — comportamento:**
- Cerca in DOCUMENTI CARICATI prima di rispondere a domande su voli, orari, itinerari
- Label sezione documenti include: "voli, hotel, assicurazione, noleggio, itinerario dettagliato"
- Termini ambigui (es. "compagnia") → interpreta nel contesto viaggio, risponde senza chiedere chiarimenti
- Fallback senza semanticMap: usa `extractedText.slice(0, 15000)`

---

### Sistema Emergenze Telegram

**Flusso emergenza cliente:**
1. Cliente scrive "emergenza" o preme /emergenza
2. Sceglie operatore (Massimo o Alessia dalla tastiera)
3. **TUTTI e 3 gli operatori** ricevono notifica privata:
   - Operatore scelto: `emergencyMsg` completo con `/risolto_CHATID`
   - Altri 2: messaggio osservatore 👁 con `/risolto_CHATID` di backup
4. **Gruppo operatori**: notifica con bottone inline `[✅ Risolto] [📞 Info]`
   - ✅ Risolto: aggiorna sessione ESCALATED → ACTIVE, manda conferma al cliente, edita il messaggio nel gruppo
   - 📞 Info: popup con chatId, bookingCode, stato sessione

**Callback `em:resolve:CHATID`** — gestito nel handler `callback_query`.

---

### Altro

**Gemini image generation** — modello aggiornato:
- `gemini-2.0-flash-exp-image-generation` → `gemini-2.0-flash-preview-image-generation` (deprecato)
- Fix in `/api/admin/generate-image/route.ts`

---

*Ultimo aggiornamento: 2026-04-03*

---

## SESSIONE 2026-04-03 sera — Preventivi completo + Multi Hotel separato

### Flusso Preventivi (PRV) — completo

**Stati:** `DRAFT → SENT → CONFIRMED → PAID → CONVERTED`

**Import PDF → voci:**
- `pdf-parse` estrae testo lato client (pdf.js), Haiku genera voci con prezzi
- Voci a 0 se non riconosce il prezzo
- Totale PDF mostrato come voce indicativa non modificabile ("Totale preventivo indicativo rif. PDF €X")
- Il totale delle voci deve matchare il totale PDF

**Invio al cliente:**
- Email con Gamma brochure linkata (prompt aggiornato: slide finali + foto reali + theme completo)
- Link conferma: `/api/admin/preventivi/confirm?code=PRV-...&token=...`

**Pagina approvazione `/preventivo-confermato`:**
- Riepilogo: nome cliente, destinazione, partenza, totale, acconto 30%
- Link brochure Gamma
- Bottoni pagamento Stripe/PayPal per acconto
- Badge verde "Acconto ricevuto" se già PAID

**Webhook Stripe → PAID:**
- Metadata `preventivoId` nei link generati da `confirm/route.ts`
- Webhook legge `preventivoId` → status `PAID`, `paidAt`, `depositPaid`
- NON converte più a CONVERTED automaticamente (operatore decide)
- Notifica Telegram a tutti 3 operatori: "💳 Acconto PAID"

**Pannello post-PAID nell'admin:**
- Riquadro verde: Totale originale / Acconto pagato / Residuo da pagare
- Voci extra aggiungibili (label + importo) via `prompt()` inline
- Per ogni extra: genera link Stripe/PayPal o segna pagato manualmente
- Residuo si riduce automaticamente man mano che extra vengono pagati
- Link saldo finale generato su richiesta

**Schema Preventivo (nuovi campi):**
- `paidAt DateTime?` — quando acconto pagato
- `depositPaid Decimal?` — importo acconto effettivo
- `extraItems Json?` — voci extra post-pagamento `[{id,label,price,paid,stripeLink,paypalLink}]`
- Migration: `20260403180000_preventivo_paid_extras`

**Fix UX:**
- Form modifica/creazione ora appare SOTTO la lista preventivi (non sopra)
- Bottone Modifica nascosto per status PAID/CONVERTED
- Crea 4GO visibile anche da status PAID
- Colori: tutti i #002366 fissi → var(--text-primary) / #0ea5e9 (visibili su tema scuro)

**API pubblica preventivo:**
- `GET /api/preventivo/info?code=PRV-...` → dati sicuri (senza items/prezzi interni)

---

### Multi Hotel PKG — pagina separata

**Posizione nel nav:** Pacchetti → Multi Hotel PKG → `/admin/multi-hotel`

**Caratteristiche:**
- Pagina completamente separata dai preventivi
- Step 1: Associa PRV (opzionale) — tutti i PRV non archiviati (qualsiasi stato, inclusi CONVERTED)
  - Il PKG può essere voce extra aggiuntiva di un PRV già convertito
  - Auto-compila: clientName, clientEmail, destination, date
  - Nome/email read-only se PRV associato
  - Pulsante "Rimuovi" per compilazione manuale
- Step 2: Dati cliente (manuali o auto-compilati, destination/date sempre modificabili)
- Step 3: Carica PDF hotel (drag&drop multiplo, stessa logica di prima)
- Salva → link `/scegli-pacchetto?code=PKG-...` + Copia link + Nuovo PKG

**Associazione PRV → PKG:** il campo `preventivoId` e `preventivoCode` vengono passati al backend `preventivi/package` POST.

---

*Ultimo aggiornamento: 2026-04-03 sera*

---

## SESSIONE 2026-04-04 — Fix vari + Gemini newsletter + Preventivi UX

### Gemini image generation
- Modello definitivo: `gemini-3.1-flash-image-preview` (Nano Banana 2)
- Campo chiave rimosso dalla newsletter — il server legge `googleAiApiKey` da `SiteSettings` DB
- Newsletter: solo campo prompt visibile + bottone ✨ Genera foto
- Route `/api/admin/generate-image`: fallback automatico a chiave DB se non passata dal client

### Preventivi — fix UX
- Elimina preventivo: sparisce immediatamente dalla lista (ottimistico) + confirm mostra codice PRV
- Status PAID aggiunto con colore verde nelle card
- Bottone Modifica nascosto per PAID/CONVERTED

### Documenti Telegram — fix OCR
- `remapDoc` nel client: rimosso AbortController (killava il fetch prima del completamento)
- `load()` ora sempre nel `finally` → badge si aggiorna anche dopo errori
- Badge documenti prenotazioni: filtro `!d.botOnly` — solo PDF allegabili contano nel badge

### Multi Hotel PKG
- Dropdown preventivi: mostra TUTTI i PRV non archiviati inclusi CONVERTED
  (il PKG può essere voce extra di una pratica già chiusa)

### Prompt Gamma — gamma-brochure PRV aggiornato
- Slide finali aggiunte: `## Prezzi e Prenotazione` + `## 4&GO FourTravel` con layout NERO
- `additionalInstructions`: foto reali luoghi, cover iconica destinazione
- Theme esteso a 9 destinazioni (allineato a generate-gamma-auto)

### Emergenze Telegram
- Tutti e 3 gli operatori ricevono notifica privata (non solo l'operatore scelto)
- Gruppo: bottone inline ✅ Risolto con callback `em:resolve:CHATID`
- Operatori non scelti ricevono messaggio osservatore con `/risolto_CHATID` di backup

---

*Ultimo aggiornamento: 2026-04-04*

