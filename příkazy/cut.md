# Příkaz `cut`

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `cut` slouží k vyřezávání částí textu z každého řádku vstupu. Je to jednodušší alternativa k příkazu `awk`, pokud potřebuješ jen rychle vybrat konkrétní sloupce (pole) nebo určité znaky.

**V praxi to jako správce využiješ, když:**

- Potřebuješ vytáhnout seznam uživatelských jmen ze souboru `/etc/passwd`.
- Chceš z výstupu příkazu získat jen první tři písmena každého řádku.
- Potřebuješ oddělit data v CSV souboru, kde je jasně definovaný oddělovač (např. čárka nebo středník).

---

### 2. Konfigurace a syntaxe

`cut` je standardní součástí systému a nevyžaduje žádnou instalaci.

**Základní syntaxe se zápisem do souboru:**

```bash
cut -d':' -f1 /etc/passwd > uzivatele.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `cut`: Spouští samotný program.
- `-d':'`: (Delimiter) Definuje oddělovač polí. V souboru `/etc/passwd` jsou data oddělena dvojtečkou. Pokud oddělovač nezadáš, výchozím je tabulátor (nikoliv mezera!).
- `-f1`: (Fields) Určuje, které pole (sloupec) chceš vybrat. Číslování začíná od 1.
- `/etc/passwd`: Soubor, který zpracováváme.
- `> uzivatele.txt`: Přesměrování výstupu do souboru.

**Důležité přepínače:**

- `-f`: Výběr polí (např. `-f1,3` vybere první a třetí pole, `-f1-5` vybere pole od prvního do pátého).
- `-c`: Výběr konkrétních znaků na řádku (např. `-c1-10` vybere prvních deset znaků).
- `--complement`: Vypíše všechna pole **kromě** těch vybraných.

---

### 3. Nejčastější použití

**A. Výpis jména a domovské složky uživatelů:**

```bash
cut -d':' -f1,6 /etc/passwd > uzivatele_slozky.txt
```

- **Co to dělá:** Rozdělí řádky souboru `/etc/passwd` podle dvojtečky a vybere 1. sloupec (jméno) a 6. sloupec (home directory).

**B. Získání IP adresy z výpisu rozhraní:**

```bash
hostname -I | cut -d' ' -f1 > moje_ip.txt
```

- **Co to dělá:** `hostname -I` vypíše IP adresy stroje oddělené mezerou. `cut` s oddělovačem mezery vybere jen tu první.

**C. Odstranění prvních 5 znaků z každého řádku:**

```bash
cut -c6- soubor.txt > upraveny_soubor.txt
```

- **Co to dělá:** `-c6-` říká `cut`, aby vypsal vše od 6. znaku až do konce řádku. Tím v podstatě "odřízne" prvních pět znaků.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Práce s oddělovačem**
Máš soubor `data.csv`, kde jsou sloupce odděleny středníkem `;`. Vypiš druhý a čtvrtý sloupec a ulož výsledek do `vystup.txt`.

**Úloha 2: Ořezávání textu**
Příkaz `uptime` vypisuje aktuální čas a stav systému. Pomocí `cut` vypiš jen prvních 8 znaků tohoto výstupu (což je aktuální čas). Ulož do `cas.txt`.

**Úloha 3: Rozsahy polí**
Ze souboru `/etc/passwd` vypiš všechna pole od 1. do 4. (včetně). Jako oddělovač použij dvojtečku. Ulož do `system_info.txt`.

**Úloha 4: Negativní výběr**
Vypiš všechny informace ze souboru `/etc/passwd` kromě hesel (heslo je ve 2. sloupci, oddělovač je `:`). Použij přepínač `--complement`. Ulož do `verejne_info.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
cut -d';' -f2,4 data.csv > vystup.txt
```

**Řešení 2:**

```bash
uptime | cut -c1-8 > cas.txt
```

**Řešení 3:**

```bash
cut -d':' -f1-4 /etc/passwd > system_info.txt
```

**Řešení 4:**

```bash
cut -d':' -f2 --complement /etc/passwd > verejne_info.txt
```

- **Vysvětlení:** `--complement` v kombinaci s `-f2` zajistí, že se vypíše 1. a 3. až 7. sloupec.
