# Příkaz wc

### 1. Teoretický úvod (K čemu to slouží?)
Příkaz `wc` (Word Count) slouží k rychlému zjištění počtu řádků, slov a znaků (bajtů) v textu. Je to nejrychlejší způsob, jak ověřit, kolik dat obsahuje určitý soubor.

**V praxi to jako správce využiješ, když:**
- Chceš zjistit, kolik uživatelů máš v systému (počet řádků v `/etc/passwd`).
- Potřebuješ ověřit, jak moc "narostl" určitý logovací soubor.
- Chceš zkontrolovat velikost souboru v bajtech.

---

### 2. Konfigurace a syntaxe
`wc` nevyžaduje žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**
```bash
wc -l /etc/passwd > pocet_radku.txt
```

**Vysvětlení přepínačů:**
- `wc`: Spouští program.
- `-l`: (Lines) Vypíše pouze počet řádků.
- `-w`: (Words) Vypíše počet slov.
- `-c`: (Characters/Bytes) Vypíše počet bajtů (velikost souboru).

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Spočítání počtu uživatelů v systému:**
```bash
wc -l /etc/passwd > pocet_uzivatelu.txt
```
- **Co to dělá:** Soubor `/etc/passwd` má na každém řádku jednoho uživatele. Příkaz `wc -l` spočítá všechny tyto řádky a výsledek uloží do souboru.

**B. Zjištění počtu skupin v systému:**
```bash
wc -l /etc/group > pocet_skupin.txt
```
- **Co to dělá:** Podobně jako u uživatelů, tento příkaz spočítá řádky v souboru se skupinami a vypíše výsledné číslo.

**C. Zjištění velikosti logu v bajtech:**
```bash
wc -c /var/log/syslog > velikost_logu.txt
```
- **Co to dělá:** Přepínač `-c` vypíše přesnou velikost souboru v bajtech. Do souboru se zapíše číslo následované názvem logu.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Počítání řádků**
Zjisti, kolik řádků má soubor `/etc/services`. Výsledek ulož do souboru `sluzby_pocet.txt`.

**Úloha 2: Velikost v bajtech**
Zjisti přesnou velikost souboru `/etc/hostname` v bajtech. Výsledek ulož do souboru `velikost_hostname.txt`.

**Úloha 3: Počet slov**
Zjisti, kolik slov obsahuje soubor `/etc/hosts` (použij přepínač `-w`) a výsledek ulož do `hosts_slova.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
wc -l /etc/services > sluzby_pocet.txt
```
- **Vysvětlení:**
    - Příkaz spočítá všechny řádky v souboru se službami a výsledek zapíše do souboru.

**Řešení 2:**
```bash
wc -c /etc/hostname > velikost_hostname.txt
```
- **Vysvětlení:**
    - Přepínač `-c` vypisuje velikost v bajtech (characters/bytes).

**Řešení 3:**
```bash
wc -w /etc/hosts > hosts_slova.txt
```
- **Vysvětlení:**
    - Přepínač `-w` spočítá všechna slova (řetězce oddělené mezerou) v souboru s hostiteli.
    - `/etc/hosts` obsahuje mapování IP adres na jména.
