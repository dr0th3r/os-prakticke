# Vstup a výstup v Bashi

### 1. Teoretický úvod (K čemu to slouží?)
Aby byl skript užitečný, musí umět pracovat s daty, která mu zadáš. Buď mu je předáš během spuštění (argumenty), nebo se tě na ně zeptá během běhu (vstup od uživatele).

**V praxi to jako správce využiješ, když:**
- Chceš vytvořit skript na přidání uživatele a jeho jméno mu zadáš jako parametr (`./pridat_uzivatele.sh pavel`).
- Potřebuješ, aby se tě skript před smazáním dat zeptal: "Opravdu chceš smazat vše? (ano/ne)".
- Chceš vytvořit univerzální skript, který zpracuje jakýkoliv soubor, který mu "hodíš" jako argument.

---

### 2. Konfigurace a syntaxe
Bash používá speciální proměnné pro uložení argumentů, se kterými byl skript spuštěn.

**Základní speciální proměnné:**
- `$0`: Název samotného skriptu.
- `$1, $2, $3...`: První, druhý, třetí argument (vstupní hodnota).
- `$#`: Celkový počet argumentů, které byly zadány.
- `$@`: Seznam všech zadaných argumentů.

**Příkaz pro interaktivní vstup:**
```bash
read -p "Zadej své jméno: " uzivatel
echo "Vítej, $uzivatel!" > pozdrav.txt
```

**Vysvětlení přepínače:**
- `read`: Příkaz, který čeká na vstup z klávesnice.
- `-p`: (Prompt) Umožňuje napsat textovou výzvu přímo před zadávání vstupu.
- `uzivatel`: Název proměnné, do které se vstup uloží.

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Skript pro vytvoření zálohy zadaného souboru:**
```bash
#!/bin/bash

soubor=$1
cp "$soubor" "${soubor}_backup"
echo "Vytvořena záloha souboru $soubor" > zaloha_info.txt
```
- **Co to dělá:** Skript vezme první argument (`$1`), zkopíruje ho pod novým názvem (přidá příponu `_backup`) a zapíše potvrzení do souboru. Použití `${soubor}_backup` místo `$soubor_backup` je nutné, aby Bash poznal, kde končí název proměnné.

**B. Kontrola počtu zadaných argumentů:**
```bash
#!/bin/bash

if [ $# -lt 2 ]; then
    echo "Chyba: Musíš zadat alespoň 2 argumenty!" > chyba.log
    exit 1
fi
echo "Zadali jste $# argumentů: $@" > parametry_info.txt
```
- **Co to dělá:** Skript zkontroluje počet argumentů (`$#`). Pokud jich je méně než 2 (`-lt 2`), zapíše chybu do logu a ukončí se (`exit 1`). Jinak vypíše počet a seznam všech parametrů do souboru.

**C. Interaktivní dotaz na vytvoření složky:**
```bash
#!/bin/bash

read -p "Jak se má jmenovat nová složka? " nazv_slozky
mkdir -p "$nazv_slozky"
echo "Složka $nazv_slozky byla vytvořena" > status.txt
```
- **Co to dělá:** Skript se zastaví, počká na zadání názvu, vytvoří složku a zapíše výsledek do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Práce s argumenty**
Vytvoř skript `informace.sh`, který při spuštění očekává tvé jméno a příjmení jako dva argumenty. Skript tyto údaje vypíše do souboru `uzivatel.txt` ve formátu: `Jméno: [první_argument], Příjmení: [druhý_argument]`.

**Úloha 2: Interaktivní skript**
Vytvoř skript, který se uživatele zeptá na název souboru. Tento soubor následně vytvoří (příkaz `touch`) a do souboru `vytvoreno.log` připíše řádek: `Soubor [název] vytvořen dne [datum]`.

**Komplexní úloha:**
Vytvoř skript `copy_rename.sh`, který:
1. Přijme dva argumenty: název existujícího souboru a název nového souboru.
2. Pokud uživatel nezadá právě dva argumenty, vypíše do souboru `errors.txt` text: "Chybný počet parametrů!" a ukončí se.
3. Pokud jsou zadány správně, skript soubor zkopíruje a přejmenuje podle zadání a do souboru `history.txt` zapíše: "Uživatel [jméno_uživatele] zkopíroval [zdroj] na [cíl]".

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
#!/bin/bash
echo "Jméno: $1, Příjmení: $2" > uzivatel.txt
```
- **Vysvětlení:** `$1` a `$2` jsou automaticky naplněny hodnotami, které napíšeš za název skriptu při spouštění (např. `./informace.sh Jan Novak`).

**Řešení 2:**
```bash
#!/bin/bash
read -p "Zadej název souboru k vytvoření: " soubor
touch "$soubor"
echo "Soubor $soubor vytvořen dne $(date)" >> vytvoreno.log
```
- **Vysvětlení:** `read` uloží tvůj vstup do proměnné `soubor`. Příkaz `date` v závorkách vloží aktuální čas a datum.

**Řešení komplexní úlohy:**
```bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Chybný počet parametrů!" > errors.txt
    exit 1
fi

zdroj=$1
cil=$2

cp "$zdroj" "$cil"
echo "Uživatel $USER zkopíroval $zdroj na $cil" >> history.txt
```
- **Vysvětlení:** 
  - `$# -ne 2` kontroluje, zda počet argumentů není roven (not equal) dvěma.
  - `exit 1` ukončí skript s chybovým kódem (pro signalizaci, že se něco nepovedlo).
  - Všechny proměnné dáváme do uvozovek `"$zdroj"`, aby skript fungoval i pro soubory s mezerami v názvu.
