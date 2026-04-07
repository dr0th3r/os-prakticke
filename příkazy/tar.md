# Příkaz `tar`

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `tar` (Tape Archiver) je základní nástroj pro práci s archivy v Linuxu. Slouží ke spojení mnoha souborů a adresářů do jednoho jediného souboru (archivu), což je ideální pro zálohování nebo přenos dat. `tar` sám o sobě data nekomprimuje (nezmenšuje), ale umí spolupracovat s nástroji jako `gzip` nebo `bzip2`, které to udělají.

**V praxi to jako správce využiješ, když:**

- Potřebuješ vytvořit zálohu celého webového projektu (složky `/var/www/html`).
- Chceš poslat kolegovi celou složku se skripty v jednom souboru.
- Potřebuješ rozbalit stažený software z internetu.

---

### 2. Konfigurace a syntaxe

`tar` je standardní součástí téměř každé Linuxové distribuce.

**Základní syntaxe pro vytvoření archivu:**

```bash
tar -cvzf zaloha.tar.gz /cesta/ke/slozce > log_zalohy.txt
```

**Vysvětlení přepínačů (každé písmeno má svůj význam):**

- `-c`: (Create) Vytvořit nový archiv.
- `-x`: (eXtract) Rozbalit existující archiv.
- `-v`: (Verbose) Ukáže seznam souborů, se kterými právě pracuje (vypisuje průběh).
- `-z`: (Gzip) Archiv rovnou zkomprimuje (zmenší velikost). Soubor pak končí na `.tar.gz`.
- `-j`: (Bzip2) Silnější komprese než gzip, ale pomalejší. Soubor končí na `.tar.bz2`.
- `-f`: (File) Určuje název výsledného souboru. **Tento přepínač musí být vždy jako poslední před názvem souboru!**

---

### 3. Nejčastější použití

**A. Vytvoření komprimované zálohy složky:**

```bash
tar -cvzf domov_zaloha.tar.gz /home/student > seznam_souboru.txt
```

- **Co to dělá:** Vytvoří soubor `domov_zaloha.tar.gz`, který obsahuje vše z adresáře `/home/student`. Průběh (seznam souborů) se uloží do `seznam_souboru.txt`.

**B. Rozbalení archivu do konkrétního místa:**

```bash
tar -xvzf projekt.tar.gz -C /var/www/test/ > vysledek_rozbaleni.txt
```

- **Co to dělá:** Rozbalí obsah archivu `projekt.tar.gz` do složky `/var/www/test/`. Pokud nepoužiješ `-C`, rozbalí se do aktuálního adresáře.

**C. Zobrazení obsahu archivu bez rozbalení:**

```bash
tar -tf zaloha.tar.gz > obsah_archivu.txt
```

- **Co to dělá:** Přepínač `-t` (list) pouze vypíše, co je uvnitř archivu, aniž by cokoliv rozbaloval na disk.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní archivace**
Vytvoř nekomprimovaný archiv (bez `-z`) s názvem `dokumenty.tar`, který bude obsahovat všechny soubory z aktuální složky končící na `.txt`. Seznam archivovaných souborů ulož do `seznam.txt`.

**Úloha 2: Komprimovaná záloha**
Vytvoř komprimovanou zálohu (pomocí gzip) adresáře `/etc/ssh`. Archiv pojmenuj `ssh_config_backup.tar.gz`.

**Úloha 3: Rozbalování**
Máš archiv `web_data.tar.gz`. Rozbal ho do připravené složky `/tmp/obnova`.

**Komplexní úloha:**
Vytvoř skript `auto_backup.sh`, který:
1. Pomocí `tar` a `gzip` vytvoří archiv složky `/etc`.
2. Název archivu bude obsahovat aktuální datum (např. `backup_2023-10-27.tar.gz`).
3. Archiv se uloží do složky `/var/backups` (předpokládej, že existuje).
4. Výsledek operace (zda se podařilo) připíše do souboru `/var/log/backup_history.log`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
tar -cvf dokumenty.tar *.txt > seznam.txt
```

**Řešení 2:**

```bash
tar -cvzf ssh_config_backup.tar.gz /etc/ssh
```

**Řešení 3:**

```bash
mkdir -p /tmp/obnova
tar -xvzf web_data.tar.gz -C /tmp/obnova > /dev/null
```

**Řešení komplexní úlohy:**

```bash
#!/bin/bash
DNES=$(date +%Y-%m-%d)
tar -cvzf /var/backups/backup_$DNES.tar.gz /etc > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "$DNES: Záloha byla úspěšně vytvořena." >> /var/log/backup_history.log
else
    echo "$DNES: CHYBA při vytváření zálohy!" >> /var/log/backup_history.log
fi
```

- **Vysvětlení:** `$?` je speciální proměnná, která obsahuje návratový kód posledního příkazu. `0` znamená úspěch. `2>&1` přesměruje i chybová hlášení do stejného místa jako standardní výstup.
