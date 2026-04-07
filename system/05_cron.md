# Plánování úloh (CRON)

### 1. Teoretický úvod (K čemu to slouží?)

Cron je systémový plánovač úloh v Linuxu. Umožňuje automaticky spouštět skripty nebo příkazy v přesně stanovený čas (např. každou noc ve 3:00, každé pondělí, nebo každých 15 minut). Je to základní nástroj pro automatizaci údržby serveru.

**V praxi to jako správce využiješ, když:**

- Potřebuješ každou noc zálohovat databázi.
- Chceš každou hodinu kontrolovat volné místo na disku a poslat si e-mail při nedostatku.
- Potřebuješ pravidelně promazávat dočasné soubory (`/tmp`).

---

### 2. Konfigurace a syntaxe

Každý uživatel má vlastní tabulku úloh, kterou spravuje příkazem `crontab`.

**Základní práce s crontabem:**

- `crontab -e`: Otevře textový editor pro úpravu tvých naplánovaných úloh.
- `crontab -l`: Vypíše seznam všech tvých aktuálně naplánovaných úloh do terminálu.
- `crontab -r`: Smaže celou tvou tabulku úloh.

**Formát zápisu (Pět hvězdiček):**

Každý řádek v crontabu má 5 polí pro čas a následně samotný příkaz:

```text
* * * * * /cesta/ke/skriptu.sh
| | | | |
| | | | +----- Den v týdnu (0-6, kde 0 je neděle)
| | | +------- Měsíc (1-12)
| | +--------- Den v měsíci (1-31)
| +----------- Hodina (0-23)
+------------- Minuta (0-59)
```

**Speciální znaky:**

- `*`: Každá (minuta, hodina, ...)
- `,`: Seznam hodnot (např. `1,15,30` v minutách)
- `-`: Rozsah (např. `1-5` v dnech v týdnu znamená pondělí až pátek)
- `*/n`: Každých n (např. `*/15` v minutách znamená každých 15 minut)

---

### 3. Nejčastější použití

**A. Zálohování každou noc ve 4:30:**

```bash
30 4 * * * /home/uzivatel/scripts/backup.sh >> /home/uzivatel/logs/backup.log 2>&1
```

- **Co to dělá:** Ve 4:30 ráno spustí zálohovací skript. Výstup i případné chyby (`2>&1`) uloží do souboru, abys mohl ráno zkontrolovat, zda vše proběhlo v pořádku.

**B. Kontrola systému každých 10 minut:**

```bash
*/10 * * * * /usr/local/bin/check_system.sh > /dev/null
```

- **Co to dělá:** Každou desátou minutu (`0, 10, 20...`) spustí kontrolu. Výstup zahodí do "černé díry" (`/dev/null`), protože nás zajímá jen výsledek akce ve skriptu.

**C. Restartování služby každé pondělí o půlnoci:**

```bash
0 0 * * 1 systemctl restart apache2
```

- **Co to dělá:** Přesně v pondělí (1) v 00:00 restartuje webový server.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní časování**
Zapiš řádek pro crontab, který každou neděli ve 23:00 vypíše text "Tydenni udrzba" do souboru `/var/log/maintenance.log`.

**Úloha 2: Časté spouštění**
Zapiš úlohu, která se spustí každých 5 minut, zjistí aktuální čas (příkaz `date`) a připíše ho na konec souboru `/tmp/cas_kontroly.txt`.

**Úloha 3: Práce s rozsahy**
Nastav crontab tak, aby se od pondělí do pátku, vždy v 8:00 a v 16:00, spustil skript `/home/student/sync.sh`.

---

### 5. Řešení cvičení

**Řešení 1:**
V `crontab -e` přidat:

```bash
0 23 * * 0 echo "Tydenni udrzba" >> /var/log/maintenance.log
```

**Řešení 2:**

```bash
*/5 * * * * date >> /tmp/cas_kontroly.txt
```

**Řešení 3:**

```bash
0 8,16 * * 1-5 /home/student/sync.sh
```
