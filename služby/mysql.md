# MySQL / MariaDB - Import, Export a Zálohování

Tento materiál se zaměřuje výhradně na základní operace pro přenos a zálohování databází pomocí MariaDB.

### 1. Teoretický úvod (K čemu to slouží?)

Správa databází v rámci maturity vyžaduje schopnost bezpečně vyexportovat data do souboru (pro zálohu nebo migraci) a následně je na jiném stroji nebo v jiném čase do databáze opět naimportovat. Pro tyto účely se používají standardní systémové nástroje `mysqldump` a `mysql`.

### 2. Konfigurace a syntaxe

Instalace: `sudo apt update && sudo apt install mariadb-server`

**Export (Zálohování):**
`mysqldump -u [uzivatel] -p [nazev_databaze] > [soubor.sql]`

- `-u`: Uživatelské jméno (často `root`).
- `-p`: Vyžádá si zadání hesla (po stisknutí Enter).
- `>`: Přesměrování výstupu do souboru (SQL skriptu).

**Import (Obnova):**
`mysql -u [uzivatel] -p [nazev_databaze] < [soubor.sql]`

- `<`: Přesměrování souboru SQL do databázového serveru.

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**Záloha konkrétní databáze do souboru:**
```bash
sudo mysqldump -u root -p moje_databaze > zaloha_webu.sql
```
**Co to dělá:** Vygeneruje textový soubor `zaloha_webu.sql`, který obsahuje všechny SQL příkazy potřebné k vytvoření tabulek a vložení dat z databáze `moje_databaze`.

**Obnova dat ze zálohy:**
```bash
sudo mysql -u root -p moje_databaze < zaloha_webu.sql
```
**Co to dělá:** Přečte soubor `zaloha_webu.sql` a postupně provede všechny v něm obsažené příkazy přímo v databázi `moje_databaze`.

**Záloha všech databází na serveru najednou:**
```bash
sudo mysqldump -u root -p --all-databases > kompletni_zaloha.sql
```
**Co to dělá:** Exportuje úplně všechny databáze, tabulky a uživatelská nastavení z celého serveru do jednoho velkého souboru.

### 4. Cvičení pro studenta (Procvičování)

1. **Zálohování:** Vyexportujte databázi `test_db` do souboru `test_zaloha.sql`.
2. **Obnova:** Naimportujte soubor `test_zaloha.sql` do nově vytvořené prázdné databáze `obnovena_db`.
3. **Komplexní úloha:** Vytvořte plán zálohování, který:
    - Vyexportuje všechny databáze na serveru.
    - Soubor pojmenuje podle aktuálního data (použijte `$(date +%F)`).
    - Výsledný SQL soubor následně zabalí do archivu `tar.gz` (využijte znalosti z kapitoly o zálohování).

### 5. Řešení cvičení

**Úloha 1:**
```bash
sudo mysqldump -u root -p test_db > test_zaloha.sql
```

**Úloha 2:**
```bash
# Nejdříve musíme prázdnou databázi vytvořit:
sudo mysql -u root -p -e "CREATE DATABASE obnovena_db;"
# Poté importujeme:
sudo mysql -u root -p obnovena_db < test_zaloha.sql
```

**Úloha 3 (Komplexní úloha):**
```bash
# 1. Export s datem v názvu:
sudo mysqldump -u root -p --all-databases > backup_$(date +%F).sql

# 2. Archivace výsledného SQL souboru:
tar -cvzf full_db_backup.tar.gz backup_$(date +%F).sql
```

**Logika řešení:**
- Při importu musí cílová databáze (`obnovena_db`) již existovat, jinak příkaz skončí chybou.
- Použití `$(date +%F)` automaticky vloží do názvu souboru aktuální datum (např. `backup_2024-04-07.sql`), což je ideální pro automatizované skripty.
- Kombinace SQL exportu a `tar` archivu je standardní cestou pro dlouhodobé uchovávání záloh.
