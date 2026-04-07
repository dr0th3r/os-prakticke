# Příkaz uniq

### 1. Teoretický úvod (K čemu to slouží?)
Příkaz `uniq` (Unique) slouží k odstranění duplicitních řádků v textu. **Důležité pravidlo:** `uniq` umí odstranit pouze řádky, které jsou **přímo pod sebou**. Proto se téměř vždy používá v kombinaci s příkazem `sort`.

**V praxi to jako správce využiješ, když:**
- Chceš zjistit unikátní seznam IP adres z logu a vědět, kolikrát se každá z nich objevila.
- Potřebuješ vyčistit seznam uživatelů od duplicitních jmen.
- Chceš spočítat, kolikrát se v systému opakuje určitá chyba.

---

### 2. Konfigurace a syntaxe
`uniq` nevyžaduje žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**
```bash
sort /etc/passwd | uniq > unikaty.txt
```

**Vysvětlení přepínačů:**
- **uniq**: Spouští program.
- **-c**: (Count) Nejpoužívanější přepínač u maturity. Před každý řádek vypíše počet, kolikrát se v původním textu opakoval.
- **-d**: (Duplicates) Vypíše pouze ty řádky, které se v textu opakovaly.
- **-u**: (Unique) Vypíše pouze ty řádky, které se v textu objevily právě jednou.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Odstranění duplicit ze seznamu:**
```bash
sort /etc/passwd | uniq > uzivatele_cisti.txt
```
- **Co to dělá:** `sort` seřadí stejné řádky pod sebe, aby je `uniq` mohl rozpoznat a odstranit. Výsledek uloží do souboru. (V `/etc/passwd` duplicity běžně nejsou, ale příkaz takto funguje).

**B. Spočítání výskytů každého uživatele v logu:**
```bash
sort /var/log/auth.log | uniq -c > statistika_prihlaseni.txt
```
- **Co to dělá:** Přidáním přepínače `-c` uvidíš před každým řádkem logu číslo, kolikrát se tam úplně stejný řádek opakoval.

**C. Vyhledání duplicitních záznamů v souboru služeb:**
```bash
sort /etc/services | uniq -d > duplicity_sluzeb.txt
```
- **Co to dělá:** Vypíše pouze ty řádky, které se v souboru `/etc/services` vyskytují více než jednou.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní unikáty**
Vezmi soubor `/etc/group` (seznam skupin). Seřaď jej a pomocí `uniq` z něj vytvoř unikátní seznam. Výsledek ulož do `skupiny_ciste.txt`.

**Úloha 2: Počítání výskytů v logu**
Vezmi systémový log `/var/log/syslog`. Seřaď jej a zjisti, kolikrát se v něm který řádek opakuje (použij `-c`). Výsledek ulož do `syslog_stats.txt`.

**Úloha 3: Hledání pouze unikátních řádků**
Máš soubor `/etc/hosts`. Vypiš z něj pouze ty řádky, které se v celém souboru objevují **pouze jednou** (použij přepínač `-u`). Výsledek ulož do `hostitele_unikatni.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
sort /etc/group | uniq > skupiny_ciste.txt
```
- **Vysvětlení:**
    - Nejdříve seřadíme řádky pod sebe pomocí `sort`, jinak by `uniq` duplicity nepoznal.

**Řešení 2:**
```bash
sort /var/log/syslog | uniq -c > syslog_stats.txt
```
- **Vysvětlení:**
    - Přepínač `-c` vypíše statistiku výskytů každého řádku v logu.

**Řešení 3:**
```bash
sort /etc/hosts | uniq -u > hostitele_unikatni.txt
```
- **Vysvětlení:**
    - Přepínač `-u` zajistí, že řádky s duplicitami nebudou ve výpisu vůbec.
