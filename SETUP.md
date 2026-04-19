# Pultiq Landing — Setup zostávajúcich služieb

Všetky potrebné meta tagy a skripty sú už v `index.html`. Ostáva doplniť 3 veci, ktoré potrebujú **tvoje účty** v 3 službách.

---

## 1. Google Search Console (~5 minút)

**Cieľ:** Google bude indexovať stránku, uvidíš vyhľadávacie dotazy, na ktoré sa zobrazuješ, a môžeš submit sitemap.

1. Otvor https://search.google.com/search-console
2. Klikni **„Add property"** → vyber **„Domain"** (odporúčané) → zadaj `pultiq.tech`
3. Google ti dá **TXT záznam** typu `google-site-verification=abc123...` — pridaj ho do DNS u registrátora tvojej domény:
   ```
   Typ: TXT
   Host: @
   Hodnota: google-site-verification=TVOJ_TOKEN
   TTL: 3600
   ```
4. V Search Console klikni **„Verify"**
5. Po overení otvor **Sitemaps** (ľavé menu) → zadaj `sitemap.xml` → **Submit**

**Alternatíva bez DNS** (ak preferuješ HTML verifikáciu):
- V Search Console zvoľ **„URL prefix"** → zadaj `https://www.pultiq.tech`
- Google ti dá meta tag typu `<meta name="google-site-verification" content="TOKEN" />`
- V `index.html` nahraď placeholder `REPLACE_WITH_GOOGLE_VERIFICATION_TOKEN` svojím tokenom
- Redeploy a klikni **Verify**

---

## 2. Bing Webmaster Tools (~3 minúty)

**Cieľ:** Bing ťa bude indexovať aj pre ChatGPT search (Bing je back-end pre ChatGPT).

1. Otvor https://www.bing.com/webmasters
2. Prihlás sa Microsoft účtom, klikni **„Add a site"** → `https://www.pultiq.tech`
3. Zvoľ **HTML Meta Tag** overenie → dostaneš tag typu `<meta name="msvalidate.01" content="TOKEN" />`
4. V `index.html` nahraď placeholder `REPLACE_WITH_BING_VERIFICATION_TOKEN` svojím tokenom
5. Redeploy → klikni **Verify**
6. V Bing Webmaster otvor **Sitemaps** → submit `https://www.pultiq.tech/sitemap.xml`

**Tip:** V Bing Webmaster môžeš importovať verifikáciu z Google Search Console jedným klikom.

---

## 3. Analytika — Plausible alebo Umami

### Možnosť A: Plausible.io (hosted, $9/mes od 10k návštev)

1. Zaregistruj sa na https://plausible.io
2. Klikni **„Add a website"** → zadaj `pultiq.tech`
3. Skript v `index.html` už ukazuje na Plausible — **nič ďalšie netreba**
4. Po DNS nastavení domény už uvidíš návštevnosť v dashboarde

**Zadarmo alternatíva:** 30-dňová trial, potom platený plán.

### Možnosť B: Umami (self-hosted, zadarmo)

Ak máš svoj VPS (tá IP `144.91.66.147`, čo si spomínal), môžeš tam nasadiť Umami:

1. SSH na server
2. ```bash
   git clone https://github.com/umami-software/umami.git
   cd umami
   docker-compose up -d
   ```
3. Nastav nginx reverse proxy `analytics.pultiq.tech` → `localhost:3000`
4. V Umami UI vytvor nový web `pultiq.tech` → dostaneš **Website ID**
5. V `index.html` **zakomentuj Plausible skript** a **odkomentuj** Umami blok — nahraď `REPLACE_WITH_UMAMI_SITE_ID` svojim ID

Umami je plne self-hosted, súlad s vašou cookies politikou (žiadne cookies, žiadny banner).

### Možnosť C: Plausible self-hosted

Ak chceš Plausible zadarmo, daj ho self-hosted:
```bash
git clone https://github.com/plausible/community-edition
cd community-edition && docker-compose up -d
```
Potom reverse-proxy `analytics.pultiq.tech` → `localhost:8000` a v skripte v `index.html` zmeň `src` na `https://analytics.pultiq.tech/js/script.js`.

---

## 4. DNS pre doménu (ak ešte nie je nastavené)

Tvoja doména `pultiq.tech` momentálne smeruje na `144.91.66.147` (pravdepodobne tvoj VPS).

### Možnosť A — chceš GitHub Pages deploy
Zmeň DNS:
```
A @ 185.199.108.153
A @ 185.199.109.153
A @ 185.199.110.153
A @ 185.199.111.153
CNAME www → matejzan-ai.github.io
```
Po propagácii (5–30 min) si zapni HTTPS v Settings → Pages → Enforce HTTPS.

### Možnosť B — chceš zostať na VPS
Skopíruj všetky súbory na server (napr. pod nginx webroot `/var/www/pultiq`):
```bash
scp -r /Users/cc/Documents/ClaudeCode/start/www.pultiq.tech/* user@144.91.66.147:/var/www/pultiq/
```
Uisti sa, že nginx servuje `.html` súbory + `robots.txt`, `sitemap.xml`, `llms.txt`, `og-image.png`, `favicon.svg`.

---

## 5. Overenie po spustení

Po nasadení skontroluj:

- **Google Rich Results Test**: https://search.google.com/test/rich-results?url=https://www.pultiq.tech → má ukázať SoftwareApplication, Organization, FAQPage
- **Schema Validator**: https://validator.schema.org/#url=https://www.pultiq.tech
- **OG preview**:
  - Facebook: https://developers.facebook.com/tools/debug/?q=https://www.pultiq.tech
  - LinkedIn: https://www.linkedin.com/post-inspector/inspect/https%3A%2F%2Fwww.pultiq.tech
  - Twitter: https://cards-dev.twitter.com/validator
- **Mobile-Friendly Test**: https://search.google.com/test/mobile-friendly
- **Lighthouse** (v Chrome DevTools): cieľ 90+ vo všetkých kategóriách

---

## 6. Čo je už hotové ✓

- [x] SEO meta tagy (title, description, keywords, canonical)
- [x] Open Graph + Twitter Card
- [x] JSON-LD structured data (Organization + WebSite + SoftwareApplication + FAQPage)
- [x] `og-image.png` 1200×630 s brandingom
- [x] `robots.txt` s explicitnym povolením AI crawlerov (GPTBot, ClaudeBot, PerplexityBot, Google-Extended, CCBot, atď.)
- [x] `sitemap.xml` s hreflang
- [x] `llms.txt` — brief pre AI asistentov
- [x] Legal stránky: privacy, terms, cookies, gdpr
- [x] Placeholdre pre GSC a Bing verifikačné tokeny
- [x] Plausible skript pripravený na aktiváciu
- [x] Favicon (SVG)
