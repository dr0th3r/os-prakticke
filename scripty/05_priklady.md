# Komplexní maturitní příklady v Bashi

### 1. Teoretický úvod (K čemu to slouží?)

Maturitní zkouška vyžaduje schopnost propojit vše, co ses naučil – od proměnných a podmínek až po cykly a práci se soubory. Cílem je vytvořit funkční nástroj, který řeší reálný problém správce sítě.

**Typické maturitní scénáře:**

- Hromadná správa uživatelů (např. ze souboru).
- Zálohování a údržba systému.
- Analýza logů a bezpečnostní upozornění.

---

### 2. Konfigurace a syntaxe

Všechny příklady níže jsou hotové skripty. Před spuštěním nezapomeň na:

```bash
chmod +x nazev_skriptu.sh
```

---

### 3. Nejčastější použití (Maturitní příklady)

**A. Skript na automatické zálohování s datem:**

```bash
#!/bin/bash

# Konfigurace
zdroj="/var/www/html"
cil_složka="/backup"
datum=$(date +%Y-%m-%d)
archiv="backup_$datum.tar.gz"

# Kontrola složky pro zálohy
if [ ! -d "$cil_složka" ]; then
    mkdir -p "$cil_složka"
fi

# Samotné zálohování (tar -c create, -z gzip, -f file)
tar -czf "$cil_složka/$archiv" "$zdroj" 2> "$cil_složka/error.log"

# Kontrola výsledku
if [ $? -eq 0 ]; then
    echo "Záloha ze dne $datum byla úspěšně vytvořena." > "$cil_složka/report.txt"
else
    echo "Záloha ze dne $datum SELHALA! Zkontroluj error.log." > "$cil_složka/report.txt"
fi
```

- **Co to dělá:** Tento skript automaticky zálohuje webovou složku, zabalí ji do archivu s aktuálním datem v názvu a vytvoří textový report o úspěchu či selhání.

**B. Skript na hromadné vytvoření složek pro studenty:**

```bash
#!/bin/bash

# Předpokládáme soubor studenty.txt (každé jméno na novém řádku)
if [ ! -f "studenty.txt" ]; then
    echo "Chyba: Soubor studenty.txt neexistuje!" > script.log
    exit 1
fi

for jmeno in $(cat studenty.txt); do
    mkdir -p "/home/studenti/$jmeno"
    touch "/home/studenti/$jmeno/vytvoreno.txt"
    echo "Uživatel $jmeno: Složka vytvořena dne $(date)" >> script.log
done
```

- **Co to dělá:** Prochází soubor se jmény řádek po řádku, pro každého studenta vytvoří domovskou složku a zapíše potvrzení do logu.

**C. Skript na monitoring místa na disku:**

```bash
#!/bin/bash

limit=90
vyuziti=$(df / | grep / | awk '{ print int($5) }')

if [ "$vyuziti" -gt "$limit" ]; then
    echo "VAROVÁNÍ: Disk je zaplněn z $vyuziti %!" > /tmp/disk_alarm.txt
    echo "Alarm odeslán dne $(date)" >> /var/log/disk_monitor.log
else
    echo "Stav disku OK ($vyuziti %)" > /tmp/disk_status.txt
fi
```

- **Co to dělá:** Použije `df` pro zjištění stavu, `awk` pro vytažení procent jako čísla. Pokud zaplnění překročí 90 %, vytvoří alarmový soubor.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Kontrola připojení k sítě**
Vytvoř skript `ping_test.sh`, který v cyklu prozkoumá IP adresy od `192.168.1.1` do `192.168.1.10`. Pro každou IP zkusí jeden ping a do souboru `ping_vysledky.txt` vypíše, zda je daná adresa "Online" nebo "Offline".

**Úloha 2: Generátor náhodných souborů**
Vytvoř skript, který jako argument přijme číslo. Tolik prázdných souborů skript vytvoří v aktuální složce s názvy `data_1.txt`, `data_2.txt` atd. Každý vytvořený soubor zapíše do seznamu `vytvorene.txt`.

**Komplexní úloha:**
Vytvoř skript `uzivatelsky_manazer.sh`, který:

1. Přečte soubor `novi_uzivatele.csv`, kde jsou jména oddělená čárkou (např. `pavel,jan,petr`).
2. Pomocí `tr` nahradí čárky mezerami, aby mohl seznam zpracovat v cyklu.
3. Pro každé jméno:
   - Zkontroluje, zda už složka s tímto jménem v `/tmp/uzivatele/` neexistuje.
   - Pokud neexistuje, vytvoří ji a do ní zapíše soubor `info.txt` s datem vytvoření.
   - Pokud existuje, do logu `manazer.log` zapíše varování: "Uživatel [jméno] už má složku!".
4. Na konci vypíše celkový počet vytvořených složek do souboru `souhrn.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
#!/bin/bash
for i in $(seq 1 10); do
    ip="192.168.1.$i"
    ping -c 1 "$ip" > /dev/null

    if [ $? -eq 0 ]; then
        echo "$ip: Online" >> ping_vysledky.txt
    else
        echo "$ip: Offline" >> ping_vysledky.txt
    fi
done
```

- **Vysvětlení:** Používáme `seq` pro generování poslední části IP adresy a `$?` pro zjištění výsledku pingu.

**Řešení 2:**

```bash
#!/bin/bash
pocet=$1
for i in $(seq 1 $pocet); do
    nazev="data_$i.txt"
    touch "$nazev"
    echo "$nazev" >> vytvorene.txt
done
```

- **Vysvětlení:** Skript používá argument `$1` jako horní hranici pro `seq`.

**Řešení komplexní úlohy:**

```bash
#!/bin/bash
vstup=$(cat novi_uzivatele.csv | tr ',' ' ')
pocitadlo=0

mkdir -p /tmp/uzivatele

for jmeno in $vstup; do
    složka="/tmp/uzivatele/$jmeno"

    if [ -d "$složka" ]; then
        echo "Uživatel $jmeno už má složku!" >> manazer.log
    else
        mkdir "$složka"
        echo "Vytvořeno dne $(date)" > "$složka/info.txt"
        ((pocitadlo++))
    fi
done

echo "Celkem vytvořeno uživatelů: $pocitadlo" > souhrn.txt
```

- **Vysvětlení:**
  - `tr` převede CSV na seznam oddělený mezerami, který `for` umí zpracovat.
  - `-d` kontroluje existenci složky.
  - `pocitadlo` sleduje počet úspěšně vytvořených složek.
  - `mkdir -p` na začátku zajistí, že nadřazená složka existuje.
