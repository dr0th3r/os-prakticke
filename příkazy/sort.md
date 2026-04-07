# Příkaz sort

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `sort` (Řazení) slouží k uspořádání řádků v textovém souboru podle abecedy nebo číselné hodnoty. Je to základní nástroj pro organizaci dat v Linuxu.

**V praxi to jako správce využiješ, když:**

- Chceš seřadit seznam souborů podle jejich velikosti.
- Potřebuješ seřadit seznam uživatelů podle jejich jména nebo UID.
- Chceš připravit data pro příkaz `uniq` (ten vyžaduje seřazený vstup).

---

### 2. Konfigurace a syntaxe

`sort` nevyžaduje žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**

```bash
sort /etc/passwd > serazeni_uzivatele.txt
```

**Vysvětlení přepínačů:**

- `sort`: Spouští program.
- `-n`: (Numeric) Seřadí řádky podle číselné hodnoty (standardně řadí abecedně, takže 10 je před 2).
- `-r`: (Reverse) Seřadí výsledek obráceně (např. od Z do A nebo od největšího po nejmenší).
- `-h`: (Human-numeric) Seřadí lidsky čitelné velikosti (např. 1K, 5M, 2G).

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Seřazení uživatelů podle jména:**

```bash
sort /etc/passwd > uzivatele_abeceda.txt
```

- **Co to dělá:** Projde soubor `/etc/passwd` a vypíše všechny uživatele seřazené podle abecedy. Výsledek uloží do souboru.

**B. Seřazení skupin v systému od konce abecedy:**

```bash
sort -r /etc/group > skupiny_obracene.txt
```

- **Co to dělá:** Přepínač `-r` (Reverse) obrátí výsledek řazení. Seznam skupin bude začínat od písmene Z.

**C. Seřazení síťových služeb podle abecedy:**

```bash
sort /etc/services > sluzby_serazene.txt
```

- **Co to dělá:** Seřadí rozsáhlý seznam všech síťových služeb, které Linux zná, a výsledek uloží do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Abecední seznam**
Vezmi soubor `/etc/passwd` a seřaď jeho obsah abecedně. Výsledek ulož do `uzivatele_serazeni.txt`.

**Úloha 2: Obrácené řazení služeb**
Vezmi soubor `/etc/services`. Seřaď jeho obsah od konce abecedy k začátku (Z-A) a výsledek ulož do `sluzby_obracene.txt`.

**Úloha 3: Seřazení podle zaplnění (Human-readable)**
Zjisti stav disků pomocí `df -h` a výsledek seřaď podle volného místa. Výsledek ulož do `disky_serazeni.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
sort /etc/passwd > uzivatele_serazeni.txt
```

- **Vysvětlení:**
  - Bez přepínačů řadí `sort` řádky abecedně od A do Z.

**Řešení 2:**

```bash
sort -r /etc/services > sluzby_obracene.txt
```

- **Vysvětlení:**
  - Přepínač `-r` (Reverse) obrátí výsledek řazení.

**Řešení 3:**

```bash
df -h | sort -h -k 7 > disky_serazeni.txt
```

- **Vysvětlení:**
  - Přepínač `-h` (human-numeric-sort) správně seřadí lidsky čitelné velikosti (jako 10G, 2M) nebo procenta. Přepínač `-k 7` říká, že se má řadit podle sedmého sloupce.
