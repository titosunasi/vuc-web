# Nasadenie cez GitHub namiesto webFTP — čo mám spraviť

Pre onepager VÚC noviny. Nahradzuje ručné nahrávanie cez webFTP.

---

## Čo sa mení

| | webFTP (doteraz) | GitHub Pages (odteraz) |
|---|---|---|
| Nahratie | pretiahnuť súbor v prehliadači | *Commit* → *Push*, dve tlačidlá |
| História | žiadna, prepíšeš a je po starom | každá zmena zapísaná, návrat späť |
| Náhľad na schválenie | žiadny, ide to rovno na ostrú | vlastná adresa ešte pred doménou |
| Kto má akú verziu | hádame | repozitár je jediný zdroj pravdy |
| Cena | v hostingu | zadarmo |

**Dôležité:** GitHub Pages vie hostovať **len statické súbory** — HTML, CSS, JS, obrázky.
Na tento onepager to stačí. WordPress ani nič s PHP tam nefunguje.

---

## MOJE TO-DO — jednorazové nastavenie (~30 min)

### 1. Účet a appka
- [ ] Založiť účet na **github.com** (ak ešte nemám)
- [ ] Stiahnuť **GitHub Desktop** — desktop.github.com
  *Appka s tlačidlami, žiadny príkazový riadok.*
- [ ] Prihlásiť sa v nej

### 2. Repozitár
- [ ] V GitHub Desktop: **File → New Repository**
  - Name: `vuc-noviny`
  - Local path: `~/Claude/`
  - Nechať **Public** (pri Private je Pages platená)
- [ ] **Publish repository** (tlačidlo hore)

Tým vznikne priečinok `~/Claude/vuc-noviny/` — tam bude Claude pracovať.

### 3. Zapnúť Pages
- [ ] Na github.com otvoriť repozitár → **Settings** → vľavo **Pages**
- [ ] *Source*: **Deploy from a branch**
- [ ] *Branch*: **main**, priečinok **/ (root)** → **Save**
- [ ] Počkať 1–2 min, v záložke **Actions** musí byť zelená fajka
- [ ] Skopírovať si adresu: `https://MOJEMENO.github.io/vuc-noviny/`

### 4. Skúška
- [ ] Otvoriť tú adresu — musí sa zobraziť stránka
- [ ] Zmeniť čokoľvek v texte → *Commit* → *Push* → o minútu skontrolovať, že sa zmena prejavila

**Po tomto bode viem publikovať bez FTP.**

---

## Denná práca — ako to vyzerá potom

1. Claude upraví `index.html`
2. V GitHub Desktop vidím zoznam zmien
3. Dole vľavo napíšem krátky popis (napr. „väčší nadpis")
4. **Commit to main** → **Push origin**
5. O minútu je to online

Žiadne sťahovanie príloh, žiadne pretiahnutie do webFTP.

---

## Napojenie na doménu

**Odporúčanie: na toto sa neponáhľať.** Kým sa onepager pripravuje a schvaľuje,
úplne stačí adresa `github.io`. Doménu pripojím až keď je hotový.

### Možnosť A — subdoména *(odporúčam)*

Napr. `noviny.obcaniapretrencin.sk`. Hlavný web ostáva nedotknutý na Websupporte,
nový onepager beží vedľa neho.

Vo WebAdmine: **doména → DNS → pridať záznam**

```
Typ:     CNAME
Názov:   noviny
Hodnota: MOJEMENO.github.io
```

Potom na GitHube: **Settings → Pages → Custom domain** → `noviny.obcaniapretrencin.sk`
→ **Save** → po chvíli zaškrtnúť **Enforce HTTPS**.

### Možnosť B — celá doména na GitHub

Len ak má na tej doméne bežať **výhradne** tento onepager. Prepíšem A záznamy:

```
Typ: A   Názov: @   Hodnota: 185.199.108.153
Typ: A   Názov: @   Hodnota: 185.199.109.153
Typ: A   Názov: @   Hodnota: 185.199.110.153
Typ: A   Názov: @   Hodnota: 185.199.111.153
```

⚠️ **Toto odpojí od domény všetko, čo tam beží teraz.** Pre `obcaniapretrencin.sk`
to nerobiť — je tam živý web.

### Na čo počítať
- DNS zmena môže trvať **až 24 hodín**, kým sa prejaví všade
- Certifikát pre HTTPS si GitHub vybaví sám, ale až po tom, čo DNS začne fungovať
- `Enforce HTTPS` sa dá zapnúť až keď je certifikát pripravený

---

## Čo povedať Claudovi v novom chate

> Projekt nasadzujem cez GitHub Pages, nie cez FTP. Pracovný priečinok je
> `~/Claude/vuc-noviny/`, je to git repozitár napojený na GitHub.
>
> Takže:
> - súbory rob priamo v tom priečinku, neposielaj mi ich ako prílohy
> - po väčšej zmene mi povedz, čo mám dať do popisu commitu
> - živú verziu si over na `https://MOJEMENO.github.io/vuc-noviny/`
> - commit a push si robím sám cez GitHub Desktop

---

## Otvorená otázka

Na akú adresu má onepager nakoniec ísť? Podľa toho sa zvolí možnosť A alebo B:

- subdoména existujúcej domény → **A**
- vlastná nová doména → **B**
- zatiaľ nevieme → nechať na `github.io` a doriešiť neskôr
