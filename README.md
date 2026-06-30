# InnoCert — website

Statische website (puur HTML/CSS/JS, geen build-stap) voor InnoCert, klaar om via GitHub te beheren en met GitHub Pages te publiceren.

## Structuur

```
index.html                  Homepage
diensten.html                Alle diensten + sectie per certificering (ISO 9001, ISO 14001, ISO 55000, NEN 2767, ISO 3834, NEN 1090, BRL 0512, BRL 0503)
over-ons.html                Over InnoCert
contact.html                 Contactformulier (nog niet gekoppeld aan een verzendservice, zie hieronder)
blog.html                    Blog-overzicht
blog-artikel-voorbeeld.html  Sjabloon voor een los blogartikel — kopieer dit bestand voor elk nieuw artikel
css/style.css                Alle styling (kleuren/lettertypen bovenaan als CSS-variabelen)
js/main.js                   Mobiel menu + (placeholder) verwerking contactformulier
assets/logo.svg              Logo (stempel-beeldmerk), ook gebruikt als favicon
```

## 1. Op GitHub zetten

1. Maak een nieuwe **public** repository aan op GitHub, bijv. `innocert-website`.
2. Upload alle bestanden uit deze map naar de root van die repository (via de GitHub-website met "Add file → Upload files", of via git).
3. Ga naar **Settings → Pages**.
4. Bij **Branch**, kies `main` en map `/ (root)` → **Save**.
5. Na een paar minuten is de site bereikbaar op `https://<jouw-gebruikersnaam>.github.io/innocert-website/`.

## 2. Eigen domein koppelen (innocert.nl)

Je huidige DNS-zone bij je provider (het paneel met "Huidige DNS records") bevat dit:

| Naam | Type | Content | Actie |
|---|---|---|---|
| `www` | A | 185.104.28.238 | **Vervangen** door CNAME → `<jouw-gebruikersnaam>.github.io` |
| `www` | AAAA | 2a06:2ec0:1::ffed | **Verwijderen** (vervalt samen met de A-record hierboven) |
| `@` | A | 185.104.28.238 | **Vervangen** door 4 A-records (zie hieronder) |
| `@` | AAAA | 2a06:2ec0:1::ffed | **Vervangen** door 4 AAAA-records (optioneel, zie hieronder) |
| `ftp` | A / AAAA | 185.104.28.238 / 2a06:2ec0:1::ffed | **Laten staan** — alleen aanpassen als je geen FTP-toegang tot de oude hosting meer nodig hebt |
| `mail` | A / AAAA | 185.104.28.238 / 2a06:2ec0:1::ffed | **Niet aanraken** — anders werkt je e-mail niet meer |
| `@` | NS (3x, ns.zxcs.\*) | — | **Niet aanraken** |
| `_domainkey`, `_dmarc`, `@` (SPF) | TXT | — | **Niet aanraken** — belangrijk voor e-mailafzending/DKIM/DMARC/SPF |

### Stap voor stap

1. **Verwijder** de bestaande `www` A-record en `www` AAAA-record.
2. **Voeg toe**: `www` CNAME → `<jouw-gebruikersnaam>.github.io` (TTL mag op 24h blijven staan, of lager zoals 1h tijdens het overzetten).
3. **Verwijder** de bestaande `@` (kale domein) A-record.
4. **Voeg toe**: vier `@` A-records, elk met TTL 24h (of lager):
   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```
5. *(Optioneel, voor IPv6)* **Vervang** de bestaande `@` AAAA-record door deze vier:
   ```
   2606:50c0:8000::153
   2606:50c0:8001::153
   2606:50c0:8002::153
   2606:50c0:8003::153
   ```
   Doe je dit niet, dan werkt de site gewoon over IPv4 — dit is geen verplichte stap.
6. Laat `ftp`, `mail`, de drie `NS`-records en de drie `TXT`-records (`_domainkey`, `_dmarc`, SPF) **ongewijzigd** — die hebben niets met de website te maken en raken anders je e-mail kwijt.
7. In de GitHub-repository: maak een bestand **`CNAME`** (geen extensie) in de root met als inhoud:
   ```
   innocert.nl
   ```
8. In **Settings → Pages** van de repository: vul bij **Custom domain** `innocert.nl` in en sla op. Zodra GitHub de DNS heeft geverifieerd (kan tot 24-48 uur duren na het wijzigen van de records) verschijnt de optie **Enforce HTTPS** — vink die aan.
9. GitHub zorgt er dan automatisch voor dat `www.innocert.nl` doorverwijst naar `innocert.nl`.

**Let op:** zodra je de `@`-A-records en `www`-CNAME wijzigt, gaat je site live op GitHub Pages in plaats van op de huidige hosting. Zorg dat de site naar tevredenheid is voordat je deze stap zet — e-mail (`mail`-record) blijft intussen gewoon werken via de huidige provider.

Wil je dat ik dit met je doorloop zodra je toegang hebt tot het DNS-paneel? Dan kan ik je precies vertellen welk veld je moet aanpassen.

## 3. Contactformulier laten werken

Het formulier op `contact.html` toont nu alleen een bevestigingstekst, maar verstuurt nog niets. Twee makkelijke opties:

- **Formspree** (gratis voor laag volume): maak een account aan op formspree.io, vervang `<form id="contact-form">` door `<form id="contact-form" action="https://formspree.io/f/JOUW-ID" method="POST">` en verwijder de `e.preventDefault()`-regel in `js/main.js`.
- **mailto-link**: simpelste optie, maar opent de eigen e-mailclient van de bezoeker in plaats van het bericht direct te versturen.

Laat het weten als je wilt dat ik een van deze opties direct voor je inricht.

## 4. Inhoud aanpassen

- Teksten staan direct in de HTML-bestanden, makkelijk te vinden en aan te passen.
- Telefoonnummer en KVK-nummer zijn bijgewerkt met de gegevens die je hebt aangeleverd.
- De teksten bij **BRL 0512** en **BRL 0503** zijn nu gebaseerd op de daadwerkelijke beoordelingsrichtlijnen die je hebt geüpload. Controleer ze nog even op toon en volledigheid — het zijn beknopte samenvattingen, geen volledige juridische weergave van de BRL's.
- Nieuwe blogartikelen: kopieer `blog-artikel-voorbeeld.html`, pas titel/tekst aan, en voeg een kaart toe op `blog.html` (en eventueel `index.html`).

## 5. Lokaal bekijken

Geen installatie nodig — open `index.html` direct in je browser, of start lokaal een servertje:

```
python3 -m http.server 8000
```

en ga naar `http://localhost:8000`.
