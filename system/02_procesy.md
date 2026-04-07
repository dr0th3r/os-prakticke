# Paměť a procesy: Sledování a ovládání

### 1. Teoretický úvod (K čemu to slouží?)

Proces je běžící program v Linuxu (např. webový prohlížeč, terminál nebo webový server). Každý proces spotřebovává část operační paměti (RAM) a výkonu procesoru (CPU). Správce systému musí umět tyto zdroje sledovat a v případě, že program přestane odpovídat nebo systém zpomaluje, musí umět problematický proces ukončit.

**V praxi to jako správce využiješ, když:**

- Systém reaguje pomalu a ty chceš vědět, kolik volné paměti RAM ještě zbývá.
- Některý program "zamrzl" a ty ho potřebuješ vynutit k ukončení (kill).
- Chceš zjistit, kolik instancí určité služby (např. Apache) v systému aktuálně běží.

---

### 2. Konfigurace a syntaxe

Ke správě procesů a paměti se používají vestavěné nástroje, které čtou data o stavu systému v reálném čase.

**Sledování operační paměti (Příkaz `free`):**

```bash
free -h > stav_pameti.txt
```

- **free**: Zobrazí stav RAM a swapu (odkládacího prostoru na disku).
  - `-h`: (Human-readable) Vypíše hodnoty v lidsky čitelných jednotkách (GB, MB).

**Výpis běžících procesů (Příkaz `ps aux`):**

```bash
ps aux > seznam_procesu.txt
```

- **ps aux**: (Process Status) Nejpoužívanější kombinace přepínačů pro výpis všech procesů v systému.
  - `a`: Zobrazí procesy všech uživatelů.
  - `u`: Zobrazí podrobné informace (uživatele, %CPU, %MEM, PID).
  - `x`: Zobrazí i procesy, které nejsou spojeny s terminálem (služby na pozadí).
  - `--sort=-%mem`: Seřadí výpis od procesů s největší spotřebou RAM.
  - `--sort=-%cpu`: Seřadí výpis od procesů s největším vytížením CPU.
  - `f`: (Forest) Zobrazí stromovou strukturu (vztah rodič/potomek).

**Interaktivní sledování systému (Příkaz `top`):**

```bash
top -b -n 1 > aktualni_zatez.txt
```

- **top**: (Table of Processes) Interaktivní správce procesů, který se zobrazuje v reálném čase.
  - `-b`: (Batch mode) Umožňuje výstup příkazu uložit do souboru (místo interaktivního okna).
  - `-n [číslo]`: Počet aktualizací (iterací), než se program ukončí.
  - **Přímo v okně top:** `q` (ukončení), `M` (seřazení podle paměti), `P` (seřazení podle CPU).

**Ukončování procesů (Příkaz `kill` a `pkill`):**

```bash
kill 1234
kill -9 1234
pkill -u student
```

- **kill [PID]**: Pošle procesu (podle jeho ID čísla) signál k ukončení.
  - `-9`: (SIGKILL) Vynutí okamžité ukončení bez ohledu na to, co proces právě dělá. Používej jen v nouzi!
- **pkill [jméno]**: Ukončí procesy podle jejich jména (nemusíš hledat PID).

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Vyhledání ID čísla (PID) konkrétní služby:**

```bash
ps aux | grep "sshd" > info_ssh.txt
```

- **Co to dělá:** `ps aux` vypíše vše, `grep` vybere jen řádky se slovem `sshd`. Ve druhém sloupci pak uvidíš číslo PID, které potřebuješ pro příkaz `kill`.

**B. Interaktivní sledování zátěže (Příkaz `top`):**

```bash
top -n 1 -b > prehled_zateze.txt
```

- **Co to dělá:** Příkaz `top` normálně běží jako interaktivní okno. Přepínače `-n 1` (jedno opakování) a `-b` (Batch mode - dávkový režim) zajistí, že se aktuální stav jen jednou vypíše a uloží do souboru. Je to skvělé pro rychlý "snímek" zátěže procesoru a paměti.

**C. Ukončení všech programů konkrétního uživatele:**

```bash
pkill -u stazista
```

- **Co to dělá:** Pokud máš v systému uživatele `stazista`, který spustil mnoho náročných programů a pak se odhlásil, tento příkaz najednou ukončí vše, co mu patří.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Diagnostika paměti**
Vypiš aktuální stav operační paměti do souboru `ram_stav.txt`. Zjisti z něj, kolik gigabajtů paměti je v systému "celkem" (total) a kolik je jí aktuálně "k dispozici" pro nové programy (available).

**Úloha 2: Vyhledání náročného procesu**
Použij příkaz `ps aux` a výsledek seřaď tak, abys viděl procesy, které zabírají nejvíce paměti. Výstup ulož do `pametove_procesy.txt`. (Tip: použij přepínač `--sort=-%mem`).

**Úloha 3: Bezpečné ukončení simulace**
Spusť na pozadí příkaz `sleep 500 &`. Zjisti jeho PID pomocí příkazu `ps` a poté tento proces ukonči standardním příkazem `kill`. Do souboru `ukonceni.log` zapiš zprávu o úspěšném smazání procesu z paměti.

**Úloha 4: Rychlý úklid prohlížeče**
Ukonči všechny běžící instance prohlížeče `firefox` jedním příkazem.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
free -h > ram_stav.txt
grep "Mem:" ram_stav.txt | awk '{ print "Celkem: " $2 ", K dispozici: " $7 }'
```

- **Vysvětlení:**
  - Sloupec `total` (2. v pořadí) ukazuje osazenou RAM.
  - Sloupec `available` (7. v pořadí) říká, kolik paměti je reálně volné pro spuštění dalších aplikací.

**Řešení 2:**

```bash
ps aux --sort=-%mem > pametove_procesy.txt
```

- **Vysvětlení:**
  - Přepínač `--sort` umožňuje řadit výpis. Znaménko mínus `-` znamená, že největší hodnoty budou nahoře.

**Řešení 3:**

```bash
sleep 500 &
ps aux | grep "sleep" | grep -v "grep" | awk '{ print $2 }' | xargs kill
echo "Proces sleep byl ukončen" > ukonceni.log
```

- **Vysvětlení:**
  - `&` na konci příkazu ho spustí na pozadí, takže můžeš dál psát do terminálu. `kill` bez přepínačů pošle standardní žádost o ukončení.

**Řešení 4:**

```bash
pkill firefox
```

- **Vysvětlení:**
  - `pkill` ukončí všechny procesy se shodným názvem bez nutnosti hledat jednotlivá PID.
