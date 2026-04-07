# Příkaz `find`

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `find` slouží k vyhledávání souborů a adresářů v souborovém systému na základě nejrůznějších kritérií (jména, velikosti, času změny, oprávnění atd.). Na rozdíl od `locate`, který hledá v databázi, `find` prohledává disk v reálném čase, takže je vždy aktuální.

**V praxi to jako správce využiješ, když:**

- Potřebuješ najít všechny konfigurační soubory končící na `.conf`.
- Hledáš velké soubory (např. nad 100 MB), které ti zaplňují disk.
- Chceš najít a smazat všechny soubory starší než 30 dní.
- Potřebuješ najít soubory, které patří konkrétnímu uživateli.

---

### 2. Konfigurace a syntaxe

`find` je základní systémový nástroj.

**Základní syntaxe se zápisem do souboru:**

```bash
find /cesta -name "nazev_souboru" > vysledky.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `find`: Spouští samotný program.
- `/cesta`: Adresář, kde má hledání začít (např. `/etc`, `/home` nebo `.` pro aktuální složku). `find` hledá rekurzivně (i v podadresářích).
- `-name`: Kritérium hledání. V uvozovkách zadáváš masku souboru.
- `> vysledky.txt`: Přesměrování seznamu nalezených cest do souboru.

**Důležitá kritéria (přepínače):**

- `-iname`: Stejné jako `-name`, ale ignoruje velikost písmen.
- `-type`: Typ hledaného objektu (`f` pro soubor, `d` pro adresář, `l` pro odkaz).
- `-size`: Velikost souboru (např. `+100M` pro soubory větší než 100 MB, `-10k` pro menší než 10 KB).
- `-mtime`: Čas poslední změny v dnech (např. `-7` znamená změněno v posledním týdnu).
- `-user`: Soubory patřící konkrétnímu uživateli.
- `-perm`: Soubory s konkrétními právy (např. `-perm 777`).

---

### 3. Nejčastější použití

**A. Hledání velkých logů v `/var/log`:**

```bash
find /var/log -type f -size +50M > velke_logy.txt
```

- **Co to dělá:** Prohledá složku `/var/log`, hledá pouze soubory (`-type f`), které jsou větší než 50 megabajtů. Seznam uloží do souboru.

**B. Hledání souborů podle přípony v domovském adresáři:**

```bash
find ~ -iname "*.jpg" > moje_fotky.txt
```

- **Co to dělá:** V domovském adresáři uživatele (`~`) najde všechny soubory končící na `.jpg`, `.JPG`, `.Jpg` atd.

**C. Provedení akce nad nalezenými soubory (např. smazání):**

```bash
find /tmp -type f -mtime +1 -exec rm {} \;
```

- **Co to dělá:** Najde v `/tmp` všechny soubory starší než 1 den a pro každý z nich spustí příkaz `rm`. Složená závorka `{}` zastupuje název nalezeného souboru, `\;` ukončuje příkaz. **Pozor: Používej opatrně!**

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Hledání adresářů**
Najdi v adresáři `/etc` všechny adresáře, které mají v názvu slovo "ssh". Výsledek ulož do `ssh_slozky.txt`.

**Úloha 2: Hledání podle práv**
Najdi v aktuálním adresáři všechny soubory, které mají nastavená práva pro spuštění pro kohokoliv (např. `777`). Výsledek ulož do `nebezpecne_soubory.txt`.

**Úloha 3: Komplexní hledání**
Najdi v adresáři `/var` všechny soubory (`f`), které patří uživateli `root`, jsou větší než 1 MB a byly změněny v posledních 2 dnech. Výsledek ulož do `root_zmeny.txt`.

**Úloha 4: Hledání a počítání (Komplexní)**
Pomocí `find` najdi všechny `.conf` soubory v `/etc` a pomocí roury (`|`) a příkazu `wc` spočítej, kolik jich celkem je. Výsledek ulož do `pocet_konfiguraci.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
find /etc -type d -name "*ssh*" > ssh_slozky.txt
```

**Řešení 2:**

```bash
find . -type f -perm 777 > nebezpecne_soubory.txt
```

**Řešení 3:**

```bash
find /var -type f -user root -size +1M -mtime -2 > root_zmeny.txt
```

**Řešení 4:**

```bash
find /etc -type f -name "*.conf" | wc -l > pocet_konfiguraci.txt
```

- **Vysvětlení:** `find` vypíše cesty k souborům (každou na nový řádek) a `wc -l` tyto řádky spočítá.
