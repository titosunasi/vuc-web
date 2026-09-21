# Onepager z PDF — zadanie a prompt pre nový chat

> **Nasadenie:** tento projekt ide cez GitHub Pages, nie cez FTP.
> Postup a moje to-do je v `TODO-github-setup.md` v tomto priečinku.

Vychádza z projektu `obcaniapretrencin.sk` (Kultúrne babie leto, september 2026).
Časovo riadený obsah je zámerne vynechaný.

---

## 1. PROMPT NA SKOPÍROVANIE

> Mám PDF plagát, z ktorého chcem jednoduchý statický onepager. Priložím ho.
>
> **Výstup:** jeden `index.html` + priečinok `assets/`. Žiadne frameworky, žiadny build,
> žiadne knižnice. Nahrávam to ručne cez FTP na zdieľaný hosting, takže všetko musí
> fungovať z relatívnych ciest a otvoriť sa aj ako obyčajný súbor.
>
> **Štruktúra stránky:** headline hore → úvodný blok s textom → obsah v kartách →
> pätička. Vizuál vychádza z plagátu, ale nie je to jeho kópia — plagát sa číta po
> stĺpcoch, web zľava doprava.
>
> **Ako postupuj:**
> 1. Z PDF vytiahni text aj **zoznam zabudovaných fontov** — to mi povie, aká je
>    skutočná typografia. Vyrenderuj si stránku ako obrázok a pozri sa na ňu, samotný
>    text nestačí.
> 2. Skôr než začneš písať kód, povedz mi, čo si z plagátu vyčítal a čo navrhuješ.
> 3. Rob to po častiach a po každej úprave mi to **otvor a refreshni v Safari**
>    cez AppleScript (bez `do JavaScript`, to hádže chybu — použi `set URL of t to theURL`).
> 4. Hotový súbor mi vždy **pošli ako prílohu**, nahrávam ho sám.
>
> **Ako komunikuj:** po slovensky, krátko, bez žargónu. Vždy povedz **prečo**, nielen čo.
> Ak je viac možností, odporuč jednu a zdôvodni. Nepridávaj veci, ktoré som nežiadal.
>
> Som art director, nie developer. Mám základy HTML/CSS, takže pokojne ukazuj kód,
> ale vysvetli princíp. Dizajnové pripomienky budú presné a optické — počítaj s tým,
> že budem riešiť hrúbku obrysu, rytmus medzier a proporcie.
>
> Do `~/.claude/` si pozri `CLAUDE.md`, ak tam je.

---

## 2. ZADANIE — čo sa stavia

### Súbory
```
index.html            všetko v jednom: HTML + <style> + <script>
assets/bg.jpg         pozadie vyexportované z PSD/PDF
assets/LOGO-*.svg     logá
```

### Kostra
| Vrstva | Obsah |
|---|---|
| `h1` | Headline, veľký, z plagátového fontu |
| `.intro` | Farebný blok s úvodným textom |
| `.grid` | Karty s obsahom, `repeat(auto-fit, minmax(300px, 1fr))` |
| `footer` | 3 vycentrované stĺpce: organizátor / partner / odkazy |

### Typografia
- **Nadpisy** — displejový font z plagátu. Ak je z Adobe Fonts, použije sa webový
  projekt (Typekit kit), nie súbor ani PNG.
- **Telo textu** — čitateľný bezpätkový (DM Sans, Inter), nie displejový.
- **Dáta a štítky** — môže byť úzky grotesk (Anton), ale **nikdy dlhší súvislý text**.
- Všetko cez CSS premenné: `--display`, `--text`, `--sans`.

### Spacing
Jedna škála, dôsledne: **4 / 8 / 12 / 16 / 24 / 32 / 48 / 64**.
Žiadne 9, 14, 18, 22, 26. Veci, čo patria k sebe, sú bližšie než veci, čo nie.

### Interaktivita — len toľko, koľko treba
- Hover na karte: `translateY(-8px) scale(1.02)` + prehĺbený tieň.
  Zabaliť do `@media (hover:hover)` a rešpektovať `prefers-reduced-motion`.
- Video: **iframe sa načíta až po kliknutí**, dovtedy len náhľad s play tlačidlom.
  Inak stránka pri otvorení sťahuje všetky prehrávače naraz.
- Kotvy na sekcie namiesto tooltipov.

### Prístupnosť — minimum, ktoré sa oplatí
`alt` na obrázkoch, `aria-label` na tlačidlách bez textu, `:focus-visible` obrysy,
`rel="noopener"` na externých odkazoch, sémantické `dl`/`section`/`footer`.

---

## 3. AKO SA NA TOM PRACUJE

### Overuj, nehádaj
Čo sa dá zmerať, to zmeraj — a povedz výsledok. Osvedčilo sa:

```bash
# šírka textu voči veľkosti písma (proporcie nadpisov)
# pretečenie do šírky na mobile
# čas odozvy a veľkosti súborov
curl -s -o /dev/null -w '%{http_code} %{time_total}s %{size_download}B' URL
```

Vyhlásenia typu „malo by to sedieť" sú na nič. Buď to zmeraj, alebo povedz,
že to overiť nevieš a nech to skontroluje používateľ.

### Náhľad
Panel prehliadača nevie relatívne cesty a renderuje na inej šírke, než hlási.
Preto: **vyrob si dočasnú kópiu s obrázkami vloženými ako data-URI** a tú si otvor.
Na posúdenie desktopu sa na ten náhľad nespoliehaj — počítaj proporcie.

### Zdroj pravdy je server
Používateľ do súboru zasahuje aj sám. **Pred každou úpravou stiahni živú verziu**
a rob zmeny na nej, inak mu prepíšeš jeho text:

```bash
curl -s -H 'Cache-Control: no-cache' "https://domena.sk/?cb=$(date +%s)" -o /tmp/live.html
diff /tmp/live.html index.html
```

Po každej úprave vypíš **presný zoznam zmien** oproti tomu, čo beží.

### Práca s PDF (macOS bez poppler)
`pdftotext` ani `pdftoppm` tam nie sú. Funguje Swift + PDFKit:
- text cez `PDFDocument.page(at:).string`
- render na PNG cez `page.draw(with:to:)`
- zabudované fonty cez `zlib.decompress` streamov a `grep /BaseFont`

---

## 4. NA ČO SI DAŤ POZOR

Zoznam vecí, ktoré v tomto projekte **reálne pokazili výsledok** a stáli čas.

**Fonty**
- Adobe Fonts webový projekt posiela **len západoeurópsku sadu**. Slovenské mäkčene
  (Ľ Č Š Ž Ť Ň Ď) chýbajú, kým sa v nastaveniach neprepne na *All Characters*.
  Over to stiahnutím fontu a prečítaním jeho `cmap`, nie okom — na Macu s lokálne
  nainštalovaným fontom to vyzerá správne, ale návštevníkovi nie.
- Ak font nemá kurzívu, **nesklápaj ho**. Prehliadač vyrobí falošný sklon a je to vidno.
- **Impact nie je na iOS.** Vždy k nemu daj webový fallback (Anton).

**Obrysy textu**
- `-webkit-text-stroke` + `paint-order: stroke fill` = obrys za písmenom, ktorý sa
  medzi susednými písmenami zlieva. Presne ten plagátový efekt.
- Pri dvoch obrysových prvkoch nad sebou **rozhoduje `z-index`** — inak hrubý obrys
  spodného zožerie spodok horného.

**Pozadie**
- Obrázok natiahnutý na výšku celej stránky sa na mobile roztiahne aj 10× a zrno
  sa rozmaže do zvislých šmúh. Pod 820 px prepni na `cover` + `100lvh`.
- **iOS Safari farbí svoje lišty podľa pozadia stránky.** Béžová farba pozadia =
  béžové pásy nad a pod obsahom. Rieši sa to cez
  `<meta name="theme-color">`, nie naťahovaním vrstvy.

**YouTube náhľady**
- `maxresdefault.jpg` nemajú všetky videá a YouTube vtedy vráti **404 spolu s platným
  sivým obrázkom 120×90**. Na `error` sa spoľahnúť nedá — kontroluj `naturalWidth <= 120`
  a až potom prepni na `hqdefault`.
- Video ID over cez `https://www.youtube.com/oembed?url=...&format=json` — vráti názov
  a kanál, takže hneď vidíš, či je to ten film a či je kanál distribútor.

**Rozloženie**
- **Striktné poradie zľava doprava a žiadne diery sa nedajú mať naraz.** Buď rovnako
  vysoké rady (diera sa schová do karty), alebo masonry (poradie sa rozbije).
  Rozhodnutie nechaj na používateľa a vysvetli mu ten kompromis.
- Veľkosť písma viaž na **šírku karty cez `cqw`**, nie na šírku okna cez `vw`. Inak
  nadpis pri troch stĺpcoch vyzerá inak než pri jednom.
- `repeat(auto-fit, minmax(...))` v pätičke vyrobí viac stĺpcov, než máš položiek,
  a natlačí ich doľava. Na pevný počet použi `repeat(3, 1fr)`.

**Logá**
- Logo od klienta býva čierne. Na tmavom podklade je neviditeľné — vyrob si bielu
  verziu do `assets/` a pôvodný súbor nechaj na pokoji.
- Logá s rôznym pomerom strán zarovnaj cez spoločné pásmo
  (`min-height` + `align-items:center`), nie rovnakou výškou.

**Meranie návštevnosti**
- Logové štatistiky hostingu sú na návštevnosť **nepoužiteľné** — prevalcujú ich
  WordPress skenery. Typický odtlačok: `/wp-admin/install.php` desiatky ráz
  a `wlwmanifest.xml` v desiatkach ciest.
- Cookieless nástroj (GoatCounter, zadarmo) meria cez JavaScript, ktorý boty
  nespúšťajú → čisté čísla a **žiadna cookie lišta**.
- Pri čítaní IP adries nepozeraj na vlajku, ale na reverzný záznam a objem dát.
  `orange.sk` = človek, `*.cloud` / `googleusercontent` / Cloudflare = stroj.

---

## 5. ČO FUNGOVALO A ČO NIE

| Fungovalo | Nefungovalo |
|---|---|
| Overiť tvrdenie príkazom a ukázať výstup | Povedať „malo by to byť v poriadku" |
| Stiahnuť živú verziu pred úpravou | Upraviť svoju lokálnu kópiu a prepísať mu text |
| Vypísať presný zoznam zmien | Poslať súbor bez vysvetlenia, čo sa v ňom zmenilo |
| Priznať, čo overiť neviem (iOS, prehrávanie videa) | Tváriť sa, že je to overené |
| Jedno odporúčanie so zdôvodnením | Vymenovať štyri možnosti a nechať to na ňom |
| Upozorniť na nezrovnalosť v obsahu | Ticho ju opraviť po svojom |
