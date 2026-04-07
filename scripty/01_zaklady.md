# Základy Bash skriptování

### 1. Teoretický úvod (K čemu to slouží?)

Bash skript je v podstatě textový soubor obsahující posloupnost příkazů, které bys normálně psal do terminálu. Místo toho, abys je psal jeden po druhém, uložíš je do skriptu a Linux je provede automaticky za tebe.

**V praxi to jako správce využiješ, když:**

- Chceš zautomatizovat opakující se činnost (např. každodenní promazávání dočasných souborů).
- Potřebuješ provést sérii složitých instalací na více serverech.
- Chceš vytvořit vlastní nástroj, který kombinuje více Linuxových příkazů dohromady.

---

### 2. Konfigurace a syntaxe

Bash skripty obvykle končí příponou `.sh` (není to povinné, ale je to dobrým zvykem pro přehlednost).

**Základní struktura skriptu:**

```bash
#!/bin/bash

# Toto je komentář, Linux ho ignoruje
jmeno="Student"
echo "Ahoj $jmeno, dnes je $(date)" > vystup_skriptu.txt
```

**Vysvětlení jednotlivých částí:**

- `#!/bin/bash`: (Shebang) Musí být vždy na prvním řádku. Říká systému, že tento soubor má spustit pomocí programu `/bin/bash`.
- `#`: Hash (mřížka) označuje komentář. Slouží pro tvoje poznámky v kódu.
- `jmeno="Student"`: Definice proměnné. Všimni si, že kolem `=` nesmí být mezery!
- `$jmeno`: Volání proměnné. Před název musíš dát znak `$`.
- `$(příkaz)`: (Command substitution) Provede příkaz (zde `date`) a jeho výsledek vloží do textu.
- `chmod +x skript.sh`: Nejdůležitější krok – skriptu musíš dát právo ke spuštění, jinak nepůjde spustit.

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Vytvoření záložní složky a přesun souborů:**

```bash
#!/bin/bash

mkdir -p ~/zaloha
cp *.txt ~/zaloha/
echo "Záloha textových souborů dokončena dne $(date)" > ~/zaloha/log.txt
```

- **Co to dělá:** Skript vytvoří složku `zaloha` v domovském adresáři (pokud neexistuje), zkopíruje do ní všechny soubory s příponou `.txt` a zapíše informaci o čase do logu.

**B. Výpis informací o systému do souboru:**

```bash
#!/bin/bash

echo "Informace o systému pro uživatele: $USER" > system_info.txt
echo "Aktuální adresář: $PWD" >> system_info.txt
echo "Verze jádra: $(uname -r)" >> system_info.txt
```

- **Co to dělá:** Využívá vestavěné proměnné `$USER` (jméno přihlášeného) a `$PWD` (cesta k aktuální složce) a příkaz `uname`. Vše postupně zapisuje (přidává přes `>>`) do souboru.

**C. Vyčištění složky /tmp (Jednoduchý úklid):**

```bash
#!/bin/bash

rm -rf /tmp/stare_soubory/*
echo "Úklid složky /tmp dokončen" > /var/log/uklid.log
```

- **Co to dělá:** Smaže obsah konkrétního podadresáře v `/tmp` a zapíše potvrzení do logu.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: První skript**
Vytvoř skript `ahoj.sh`, který do souboru `pozdrav.txt` vypíše text "Ahoj, jsem uživatel [tvoje_jméno] a dnes je [aktuální_datum]". Skriptu nezapomeň dát práva ke spuštění.

**Úloha 2: Práce s proměnnými**
Vytvoř skript, který definuje dvě proměnné: `slozka="testovaci_data"` a `soubor="data.log"`. Skript následně vytvoří složku s tímto názvem a uvnitř ní vytvoří prázdný soubor s daným názvem (použij příkaz `touch`).

**Komplexní úloha:**
Vytvoř skript `system_check.sh`, který:

1. Do souboru `report.txt` vypíše aktuální datum (příkaz `date`) a jméno počítače (příkaz `hostname`).
2. Do stejného souboru přidá seznam všech aktuálně přihlášených uživatelů (příkaz `who`).
3. Nakonec do souboru přidá informaci o tom, jak dlouho systém běží (příkaz `uptime`).
   Všechny výstupy musí být v jednom souboru pod sebou.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
#!/bin/bash
echo "Ahoj, jsem uživatel $USER a dnes je $(date)" > pozdrav.txt
```

- **Vysvětlení:** Použili jsme vestavěnou proměnnou `$USER` a příkaz `date` v kulatých závorkách s dolarem.
- **Důležité:** Před spuštěním musíš zadat `chmod +x ahoj.sh`.

**Řešení 2:**

```bash
#!/bin/bash
slozka="testovaci_data"
soubor="data.log"

mkdir -p "$slozka"
touch "$slozka/$soubor"
```

- **Vysvětlení:** Proměnné jsme použili jako argumenty pro příkazy `mkdir` a `touch`. Uvozovky kolem proměnných jsou dobrým zvykem (chrání před mezerami v názvech).

**Řešení komplexní úlohy:**

```bash
#!/bin/bash
echo "--- System Check Report ---" > report.txt
date >> report.txt
hostname >> report.txt
echo "--- Přihlášení uživatelé ---" >> report.txt
who >> report.txt
echo "--- Doba běhu systému ---" >> report.txt
uptime >> report.txt
```

- **Vysvětlení:**
  - První řádek používá `>`, aby se starý report přepsal.
  - Všechny další řádky používají `>>`, aby se text přidával na konec souboru a nepřemazal to, co už tam je.
  - Pomocí `echo` jsme přidali oddělovače pro lepší přehlednost.
