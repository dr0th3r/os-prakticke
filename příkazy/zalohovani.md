# Zálohování (Strategie a typy záloh)

Tento materiál se zaměřuje na použití příkazu `tar` pro různé strategie zálohování: plnou, inkrementální a diferenciální zálohu.

### 1. Teoretický úvod (K čemu to slouží?)

Správce IT musí zvolit vhodnou strategii zálohování, aby šetřil místo na disku a čas přenosu dat.

- **Plná záloha (Full):** Kopíruje všechna vybraná data. Je nejjednodušší na obnovu, ale zabírá nejvíce místa a trvá nejdéle.
- **Inkrementální záloha:** Kopíruje pouze data, která se změnila **od posledního zálohování** (ať už bylo plné nebo také inkrementální). Šetří místo, ale obnova vyžaduje mít k dispozici plnou zálohu a všechny mezilehlé inkrementální části.
- **Diferenciální záloha:** Kopíruje pouze data, která se změnila **od poslední plné zálohy**. Je zlatou střední cestou – obnova vyžaduje jen plnou zálohu a poslední diferenciální část.

### 2. Konfigurace a syntaxe

Příkaz `tar` používá pro pokročilé zálohování tyto parametry:

- `-g` (listed-incremental): Umožňuje vytvářet inkrementální zálohy pomocí tzv. "snapshot souboru" (metadata o tom, kdy byl který soubor naposledy archivován).
- `--newer` (nebo `-N`): Archivuje pouze soubory novější než zadané datum (užitečné pro jednoduché diferenciální zálohy).
- `-c`, `-v`, `-f`, `-z`: (viz předchozí kapitola – vytvoření, výpis, soubor, komprese).

### 3. Nejčastější použití (Praktické ukázky strategií)

**Plná záloha s vytvořením snapshot souboru:**
```bash
tar -cvzf full_backup.tar.gz -g metadata.snar /home/student/dokumenty
```
**Co to dělá:** Vytvoří plnou komprimovanou zálohu. Do souboru `metadata.snar` si uloží aktuální stav souborů (datum jejich poslední změny). Tento soubor je klíčový pro další kroky.

**Inkrementální záloha (pouze změny od minule):**
```bash
tar -cvzf incremental_1.tar.gz -g metadata.snar /home/student/dokumenty
```
**Co to dělá:** Porovná aktuální stav souborů s tím, co je zapsáno v `metadata.snar`. Do nového archivu přidá pouze soubory, které byly změněny nebo přidány od předchozí zálohy.

**Diferenciální záloha (změny od konkrétního data):**
```bash
tar -cvzf diff_backup.tar.gz --newer="2024-04-01" /home/student/dokumenty
```
**Co to dělá:** Vytvoří zálohu všech souborů, které byly vytvořeny nebo změněny od 1. dubna 2024. Pokud toto datum odpovídá poslední plné záloze, jedná se o diferenciální zálohu.

### 4. Cvičení pro studenta (Procvičování)

1. **Plná záloha:** Vytvořte plnou zálohu složky `/var/log` s názvem `full.tar.gz` a inicializujte snapshot soubor `logy.snar`.
2. **Práce s daty:** Do složky `/var/log` vytvořte nový soubor (např. `test.log`) a poté proveďte první inkrementální zálohu s názvem `inc1.tar.gz` pomocí stejného snapshot souboru.
3. **Diferenciální přístup:** Vytvořte zálohu všech souborů v `/home`, které se změnily od včerejšího poledne (formát "yesterday 12:00").
4. **Komplexní scénář:** Navrhněte týdenní plán zálohování:
    - V pondělí se spustí plná záloha.
    - Od úterý do pátku se spouštějí inkrementální zálohy.
    - Napište příkazy pro pondělí a úterý.

### 5. Řešení cvičení

**Úloha 1:**
```bash
sudo tar -cvzf full.tar.gz -g logy.snar /var/log
```

**Úloha 2:**
```bash
sudo touch /var/log/test.log
sudo tar -cvzf inc1.tar.gz -g logy.snar /var/log
```

**Úloha 3:**
```bash
tar -cvzf home_recent.tar.gz --newer="yesterday 12:00" /home
```

**Úloha 4 (Týdenní plán):**
```bash
# Pondělí (Plná záloha + nový snapshot)
tar -cvzf backup_monday_full.tar.gz -g weekly.snar /data

# Úterý (První inkrement)
tar -cvzf backup_tuesday_inc.tar.gz -g weekly.snar /data
```

**Logika řešení:**
- Inkrementální záloha s `-g` je "chytrá" – pokud soubor `weekly.snar` neexistuje, tar ho vytvoří a udělá plnou zálohu. Pokud existuje, tar ho přečte a udělá pouze přírůstek.
- Při obnově inkrementální zálohy musíte rozbalit nejdříve pondělní soubor, pak úterní, pak středeční atd., vždy do stejné složky.
- `--newer` (diferenciální) je jednodušší na správu (nepotřebuje snapshot soubor), ale vyžaduje od správce hlídat správné datum poslední plné zálohy.
