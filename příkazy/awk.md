# AWK

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `awk` slouží k automatickému zpracování textových tabulek a výstupů v terminálu. V Linuxu většina příkazů (jako `ls -l`, `ps aux`, `df -h`) vypisuje data v úhledných sloupcích oddělených mezerou. `awk` je nástroj, který do těchto sloupců umí "sáhnout" a vybrat si jen to, co tě zajímá.

**V praxi to jako správce využiješ, když:**

- Potřebuješ z výpisu všech běžících procesů vytáhnout jen jejich PID (identifikační číslo).
- Chceš z dlouhého konfiguračního souboru vypsat jen jména uživatelů.
- Chceš sečíst čísla ve sloupci (např. velikost všech logů v adresáři).

---

### 2. Konfigurace a syntaxe

`awk` je vestavěný příkaz, který se spouští přímo v terminálu.

**Základní syntaxe se zápisem do souboru:**

```bash
awk -F':' '{ print $1 }' /etc/passwd > uzivatele.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `awk`: Spouští samotný program.
- `-F':'`: Přepínač (Field separator). Říká `awk`, co odděluje sloupce v souboru. V souboru `/etc/passwd` jsou to dvojtečky `:`. Pokud tento přepínač nepoužiješ, `awk` automaticky předpokládá mezeru.
- `' { ... } '`: Celý příkaz pro `awk` musí být v těchto jednoduchých uvozovkách, aby ho terminál (Bash) chybně neinterpretoval.
- `print $1`: Příkaz uvnitř `awk`. `$1` znamená první sloupec, `$2` druhý atd. `$0` znamená celý aktuální řádek.
- `/etc/passwd`: Cesta k souboru, který chceme číst.
- `> uzivatele.txt`: Přesměrování. Místo na obrazovku se výsledek zapíše do souboru `uzivatele.txt`.

---

### 3. Nejčastější použití

**A. Vyfiltrování zaplněných disků nad 80 %:**

```bash
df -h | awk 'int($5) > 80 { print $1 " je plný z " $5 }' > kriticke_disky.txt
```

- **Co to dělá:** `df -h` vypíše stav disků. `awk` se podívá do 5. sloupce (kapacita v %). `int($5)` zajistí, že se na text "85%" dívá jako na číslo 85. Pokud je větší než 80, vypíše název disku (`$1`) a jeho zaplnění do souboru.

**B. Získání PID (ID procesu) konkrétní služby (např. sshd):**

```bash
ps aux | awk '/sshd/ { print $2 }' > ssh_pids.txt
```

- **Co to dělá:** `ps aux` vypíše všechny procesy, které běží. `/sshd/` řekne `awk`, aby hledal jen řádky, které obsahují text "sshd". `{ print $2 }` pak z těchto řádků vytáhne druhý sloupec, což je v Linuxu vždy PID procesu.

**C. Sečtení velikosti všech souborů v aktuálním adresáři:**

```bash
ls -l | awk '{ soucet += $5 } END { print "Celková velikost v bajtech: " soucet }' > velikost.txt
```

- **Co to dělá:** `ls -l` vypisuje velikost souboru v 5. sloupci. `awk` pro každý řádek přičte hodnotu z 5. sloupce do proměnné `soucet`. Blok `END` se provede až po přečtení všech řádků a vypíše výslednou částku do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Úplný základ**
Ze souboru `/etc/passwd` vypiš pouze první sloupec (uživatelská jména). Oddělovačem je dvojtečka `:`. Výsledek ulož do `jmena.txt`.

**Úloha 2: Spojování textu (Formátování)**
Ze souboru `/etc/passwd` vypiš první sloupec (jméno) a šestý sloupec (domovský adresář) ve formátu: `Uživatel [jméno] má složku v [adresář]`. Výsledek ulož do `seznam_slozek.txt`.

**Úloha 3: Filtrování a výpočty**
Z výpisu `ls -l` v aktuálním adresáři vypiš jméno a velikost souborů, které jsou větší než 1 MB (1 048 576 bajtů). Velikost ale vypiš v kilobajtech (vyděl bajty hodnotou 1024).

**Úloha 4: Sumarizace**
Z výpisu `ps aux` vyber všechny procesy uživatele `root`. Pro každý proces vypiš jeho PID (2. sloupec) a na úplný konec výpisu přidej celkový součet procentuálního využití paměti (%MEM, 4. sloupec) všech těchto procesů.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
awk -F':' '{ print $1 }' /etc/passwd > jmena.txt
```

**Řešení 2:**

```bash
awk -F':' '{ print "Uživatel " $1 " má složku v " $6 }' /etc/passwd > seznam_slozek.txt
```

**Řešení 3:**

```bash
ls -l | awk '$5 > 1048576 { print $9 " má velikost " $5/1024 " KB" }'
```

**Řešení 4:**

```bash
ps aux | awk '$1 == "root" { print $2; soucet += $4 } END { print "Celková paměť root procesů: " soucet "%" }'
```

- **Vysvětlení:** `soucet += $4` sčítá hodnoty paměti v průběhu čtení souboru. Blok `END` se provede až na konci, kde vypíšeme výslednou sumu.
