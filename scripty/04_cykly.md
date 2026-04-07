# Cykly v Bashi

### 1. Teoretický úvod (K čemu to slouží?)
Cykly umožňují provádět stejnou akci opakovaně. Místo toho, abys psal příkaz 100krát pro každý soubor zvlášť, napíšeš cyklus, který projde všechny soubory ve složce a provede s nimi to, co potřebuješ.

**V praxi to jako správce využiješ, když:**
- Chceš hromadně přejmenovat tisíce fotek.
- Potřebuješ vytvořit zálohu pro každou složku v `/home`.
- Chceš otestovat dostupnost 50 různých IP adres (ping).

---

### 2. Konfigurace a syntaxe
V Bashi se nejčastěji používají dva typy cyklů: `for` a `while`.

**Cyklus `for` (Procházení seznamu):**
```bash
for i in 1 2 3 4 5; do
    echo "Opakování číslo $i" > vystup_$i.txt
done
```

**Cyklus `while` (Dokud platí podmínka):**
```bash
while [ $pocitadlo -lt 10 ]; do
    echo "Stále pracuji..." > stav.txt
    ((pocitadlo++))
done
```

**Vysvětlení klíčových slov:**
- `for i in seznam`: `i` je proměnná, do které se postupně ukládají prvky ze seznamu.
- `do`: Začátek těla cyklu (toho, co se má opakovat).
- `done`: Konec těla cyklu.
- `seq 1 10`: Příkaz, který vygeneruje sekvenci čísel od 1 do 10 (skvělé pro `for`).
- `((i++))`: Aritmetická operace pro zvýšení hodnoty proměnné o 1.

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Zálohování všech souborů v aktuální složce:**
```bash
#!/bin/bash

for soubor in *; do
    if [ -f "$soubor" ]; then
        cp "$soubor" "${soubor}.bak"
        echo "Vytvořena záloha: $soubor.bak" >> zaloha.log
    fi
done
```
- **Co to dělá:** `*` zastupuje všechny soubory a složky. Cyklus projde každý prvek, zkontroluje, zda je to soubor (`-f`), a pokud ano, vytvoří jeho kopii s příponou `.bak`. Záznam o každé záloze přidá do logu.

**B. Ping test na seznam serverů:**
```bash
#!/bin/bash

servery="8.8.8.8 1.1.1.1 127.0.0.1"

for ip in $servery; do
    ping -c 1 "$ip" > /dev/null
    if [ $? -eq 0 ]; then
        echo "Server $ip je dostupný" >> ping_report.txt
    else
        echo "Server $ip je NEDOSTUPNÝ" >> ping_report.txt
    fi
done
```
- **Co to dělá:** Pro každou IP adresu v seznamu provede jeden ping (`-c 1`). Výstup pingu zahodí do `/dev/null`. Pomocí `$?` (návratový kód) zjistí, zda se ping povedl, a výsledek zapíše do reportu.

**C. Generování souborů s čísly:**
```bash
#!/bin/bash

for i in $(seq 1 5); do
    touch "uzivatel_$i.txt"
    echo "ID uživatele: $i" > "uzivatel_$i.txt"
done
```
- **Co to dělá:** Pomocí `seq` vytvoří čísla 1 až 5. Pro každé číslo vytvoří soubor a zapíše do něj příslušné ID.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Číselná řada**
Vytvoř skript `pocitej.sh`, který vypíše do souboru `cisla.txt` čísla od 1 do 20, každé na nový řádek. Použij cyklus `for` a příkaz `seq`.

**Úloha 2: Hromadné vytváření složek**
Vytvoř skript, který získá seznam názvů jako argumenty (`$@`). Pro každý název vytvoří složku a uvnitř ní soubor `readme.txt`. Do souboru `vytvoreno.log` zapíše seznam všech vytvořených složek.

**Komplexní úloha:**
Vytvoř skript `clean_logs.sh`, který:
1. Projde všechny soubory v aktuálním adresáři, které končí příponou `.log`.
2. Pro každý takový soubor zkontroluje, zda má velikost větší než 100 bajtů (použij `find` nebo `stat`, pro jednoduchost stačí `wc -c < soubor`).
3. Pokud je větší, soubor smaže a do `history.log` zapíše: "Smazán velký log: [název_souboru] dne [datum]".
4. Pokud je menší, ponechá ho a do `history.log` zapíše: "Log [název_souboru] ponechán".

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
#!/bin/bash
for i in $(seq 1 20); do
    echo "$i" >> cisla.txt
done
```
- **Vysvětlení:** `seq 1 20` vygeneruje seznam čísel. Cyklus `for` do proměnné `i` postupně ukládá hodnoty od 1 do 20. `>>` přidává čísla na konec souboru.

**Řešení 2:**
```bash
#!/bin/bash
for nazev in "$@"; do
    mkdir -p "$nazev"
    touch "$nazev/readme.txt"
    echo "Vytvořena složka: $nazev" >> vytvoreno.log
done
```
- **Vysvětlení:** `$@` představuje všechny parametry, které zadáš při spuštění (např. `./skript.sh data zaloha test`).

**Řešení komplexní úlohy:**
```bash
#!/bin/bash
for soubor in *.log; do
    velikost=$(wc -c < "$soubor")
    
    if [ "$velikost" -gt 100 ]; then
        rm "$soubor"
        echo "Smazán velký log: $soubor dne $(date)" >> history.log
    else
        echo "Log $soubor ponechán" >> history.log
    fi
done
```
- **Vysvětlení:** 
  - `wc -c < "$soubor"` zjistí počet znaků (bajtů) v souboru.
  - `-gt 100` kontroluje, zda je hodnota větší než 100.
  - `history.log` uchovává záznam o každém rozhodnutí skriptu.
  - Cyklus `*.log` zajistí, že skript nesmaže nic jiného než logy.
