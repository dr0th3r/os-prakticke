# Příkaz `grep`

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `grep` (Global Regular Expression Print) slouží k vyhledávání textu v souborech nebo ve výstupech jiných příkazů. Je to jeden z nejpoužívanějších nástrojů v Linuxu, protože umožňuje rychle najít specifickou informaci v obrovském množství dat.

**V praxi to jako správce využiješ, když:**

- Potřebuješ najít řádek s konkrétním nastavením v konfiguračním souboru.
- Chceš z logu vytáhnout jen chyby (hledáš slovo "error").
- Potřebuješ zjistit, zda je v systému nainstalován určitý balíček.

---

### 2. Konfigurace a syntaxe

`grep` je standardní součástí Linuxu a nevyžaduje konfiguraci.

**Základní syntaxe se zápisem do souboru:**

```bash
grep -i "hledaný_text" soubor.txt > vysledek.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `grep`: Spouští samotný program.
- `-i`: (Ignore case) Přepínač, který zajistí, že se nerozlišují velká a malá písmena (najde "Error" i "error").
- `"hledaný_text"`: Slovo nebo věta, kterou chceme najít.
- `soubor.txt`: Soubor, ve kterém chceme hledat. Pokud soubor nezadáš, `grep` čeká na vstup z roury (`|`).
- `> vysledek.txt`: Přesměrování výstupu (vytvoří nový soubor s nalezenými řádky).

**Důležité přepínače:**

- `-v`: (Invert match) Vypíše všechno, co **neobsahuje** hledaný text.
- `-r`: (Recursive) Hledá text ve všech souborech v aktuálním adresáři i podadresářích.
- `-n`: (Line number) Vypíše i číslo řádku, na kterém se text nachází.
- `-c`: (Count) Místo řádků vypíše jen počet nalezených výskytů.

---

### 3. Nejčastější použití

**A. Vyhledání chyb v systémovém logu:**

```bash
grep -i "error" /var/log/syslog > chyby.txt
```

- **Co to dělá:** Prohledá soubor `/var/log/syslog`. Díky `-i` najde všechna slova "error", "ERROR" nebo "Error" a uloží celé řádky do souboru `chyby.txt`.

**B. Zjištění, zda běží konkrétní služba (např. Apache):**

```bash
ps aux | grep "apache2" > stav_apache.txt
```

- **Co to dělá:** `ps aux` vypíše všechny běžící procesy. `grep` z tohoto výpisu vybere jen řádky, kde se vyskytuje slovo "apache2", a uloží je do souboru.

**C. Odstranění zakomentovaných řádků z konfigurace:**

```bash
grep -v "^#" /etc/ssh/sshd_config > cista_konfigurace.txt
```

- **Co to dělá:** Hledá řádky, které začínají znakem `#` (zakomentované řádky). Díky přepínači `-v` je ale zahodí a vypíše vše ostatní. Získáš tak čistý konfigurační soubor bez komentářů.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní vyhledávání**
V souboru `/etc/passwd` najdi řádek, který patří uživateli `root`. Výsledek ulož do souboru `root_info.txt`.

**Úloha 2: Počítání výskytů**
Zjisti, kolikrát se v souboru `/var/log/auth.log` vyskytuje slovo "failure" (neúspěšné přihlášení).

**Úloha 3: Rekurzivní hledání**
Najdi všechny soubory v adresáři `/etc`, které obsahují slovo "nameserver". Výsledek (názvy souborů i s řádky) ulož do `dns_nastaveni.txt`.

**Úloha 4: Kombinace**
Vypiš všechny běžící procesy uživatele `root` (použij `ps aux`). Z tohoto výpisu pomocí `grep` vyřaď samotný proces `grep` (častý problém při hledání) a výsledek ulož do `procesy_roota.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
grep "root" /etc/passwd > root_info.txt
```

- **Vysvětlení:** Jednoduché hledání řetězce "root" v daném souboru.

**Řešení 2:**

```bash
grep -c "failure" /var/log/auth.log
```

- **Vysvětlení:** Přepínač `-c` (count) zajistí, že se vypíše pouze číslo (počet řádků s výskytem).

**Řešení 3:**

```bash
grep -r "nameserver" /etc > dns_nastaveni.txt
```

- **Vysvětlení:** Přepínač `-r` prohledá celou složku `/etc` do hloubky.

**Řešení 4:**

```bash
ps aux | grep "root" | grep -v "grep" > procesy_roota.txt
```

- **Vysvětlení:** První `grep` najde procesy roota, ale v seznamu se objeví i ten samotný vyhledávací příkaz `grep`. Druhý `grep` s přepínačem `-v` (invert) tento řádek odstraní.
