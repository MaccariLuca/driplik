# Driplik

Audit automatico per siti e-commerce — dati reali da Google PageSpeed Insights, interpretati da AI.

---

## Cosa fa

Incolla l'URL di un negozio online e ricevi in 30 secondi un report su:

- Performance (Core Web Vitals reali misurati da Google)
- UX & Design
- Checkout
- SEO (con fix specifici per tag HTML)
- Fiducia & Sicurezza

I dati vengono misurati da Google PageSpeed Insights e poi interpretati da Claude (Anthropic) per tradurli in consigli concreti per chi gestisce il negozio.

---

## Struttura del progetto

```
driplik/
├── index.html      # App audit (frontend React + chiamate API)
├── proxy.php           # Proxy lato server (protegge la chiave API Anthropic)
└── coming-soon.html    # Pagina di manutenzione animata
```

---

## Stack tecnico

| Componente | Tecnologia |
|---|---|
| Frontend | React 18 (via CDN, no build step) |
| Dati performance | Google PageSpeed Insights API v5 |
| Analisi AI | Anthropic API (claude-sonnet-4-6) |
| Backend proxy | PHP + cURL |
| Hosting | Aruba Hosting Linux |
| Font | IBM Plex Sans + IBM Plex Mono |

---

## Come funziona il flusso

```
Browser
  |
  |-- GET --> Google PageSpeed API (dati reali del sito)
  |
  |-- POST --> proxy.php (stesso dominio)
                  |
                  |-- POST --> Anthropic API (chiave segreta lato server)
                                  |
                               Claude genera il report JSON
```

Il proxy PHP risolve due problemi:
1. **Sicurezza** — la chiave API Anthropic non è mai esposta nel browser
2. **CORS** — i browser bloccano le chiamate cross-domain, il proxy le aggira

---

## Setup locale

### Prerequisiti

- PHP 7.4+ con cURL abilitato
- Una chiave API Anthropic → [console.anthropic.com](https://console.anthropic.com)
- Una chiave API Google PageSpeed → [Google Cloud Console](https://console.cloud.google.com)

### Installazione

```bash
git clone https://github.com/tuo-username/driplik.git
cd driplik
```

Apri `proxy.php` e inserisci la tua chiave API:

```php
define('ANTHROPIC_API_KEY', 'sk-ant-...');  // inserisci qui
```

Apri `index.html` e inserisci la tua chiave PageSpeed (cerca `&key=`):

```js
+ '&key=LA_TUA_CHIAVE_GOOGLE'
```

Avvia un server PHP locale:

```bash
php -S localhost:8000
```

Apri `http://localhost:8000/index.html`

---

## Deploy su hosting condiviso (Aruba / cPanel)

1. Carica tutti i file via FTP nella cartella `public_html`
2. Inserisci le chiavi API in `proxy.php`
3. Verifica che `proxy.php` sia nella stessa cartella di `index.html`
4. Testa aprendo `tuodominio.com/proxy.php` — deve rispondere con `{"error":"Metodo non consentito"}`

---

## Variabili da configurare

| File | Variabile | Dove ottenerla |
|---|---|---|
| `proxy.php` | `ANTHROPIC_API_KEY` | [console.anthropic.com](https://console.anthropic.com) |
| `index.html` | `&key=` nell'URL PageSpeed | [console.cloud.google.com](https://console.cloud.google.com) |

> **Attenzione:** non committare mai le chiavi API reali. Usa il segnaposto e inserisci le chiavi direttamente sul server.

---

## Roadmap

- [ ] Rate limiting anti-bot sul proxy
- [ ] Privacy policy e cookie banner GDPR
- [ ] Download report in PDF
- [ ] Versione desktop (attualmente solo analisi mobile)
- [ ] Autenticazione utenti e storico delle analisi
- [ ] Integrazione Cloudflare Turnstile (anti-spam)

---

## Licenza

MIT — uso libero con attribuzione.

---

