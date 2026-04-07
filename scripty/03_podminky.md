# Podmínky v Bashi

### 1. Teoretický úvod (K čemu to slouží?)

Bez podmínek by skript jen tupě prováděl jeden příkaz za druhým. Podmínka (`if`) umožňuje skriptu "myslet" a rozhodovat se podle aktuální situace v systému.

**V praxi to jako správce využiješ, když:**

- Chceš zkontrolovat, zda soubor existuje, než se ho pokusíš smazat.
- Potřebuješ zjistit, zda má uživatel dostatečná oprávnění (root).
- Chceš porovnat velikost dvou souborů nebo zjistit, zda je disk plný.
- Chceš vědět, zda předchozí příkaz proběhl v pořádku, nebo skončil chybou.
- Potřebuješ zkontrolovat více podmínek najednou (např. "pokud soubor existuje A ZÁROVEŇ je v něm text").

---

### 2. Konfigurace a syntaxe

V Bashi se podmínky píší pomocí konstrukce `if [ podmínka ]; then ... fi`. Důležité jsou **mezery** uvnitř hranatých závorek!

**Základní šablona:**

```bash
if [ "$promenna" == "hodnota" ]; then
    echo "Shoda!" > vysledek.txt
else
    echo "Neshoda!" > vysledek.txt
fi
```

**Porovnávání čísel (Numerické operátory):**

- `-eq`: Equal (rovná se).
- `-ne`: Not Equal (nerovná se).
- `-gt`: Greater Than (větší než).
- `-lt`: Less Than (menší než).
- `-ge`: Greater or Equal (větší nebo rovno).
- `-le`: Less or Equal (menší nebo rovno).

**Testování řetězců (Textu):**

- `==`: Jsou řetězce stejné?
- `!=`: Jsou řetězce různé?
- `-z`: Je řetězec **prázdný** (nulová délka)?
- `-n`: Je řetězec **NENÍ prázdný** (zkratka pro non-zero)?

**Testování souborů a složek:**

- `-f cesta`: Existuje tento soubor?
- `-d cesta`: Existuje tato složka?
- `-s cesta`: Má soubor velikost větší než 0? (Není prázdný?)
- `-r cesta`: Máme právo pro čtení?
- `-w cesta`: Máme právo pro zápis?
- `-x cesta`: Je soubor spustitelný?

**Logické operátory (Skládání podmínek):**

- `!`: Negace (Opačná podmínka). Příklad: `[ ! -f soubor ]` znamená "pokud soubor neexistuje".
- `-a`: Logický AND (A zároveň). Obě podmínky musí platit. Příklad: `[ $vek -gt 18 -a $prukaz == "ano" ]`.
- `-o`: Logický OR (Nebo). Stačí, když platí jedna z nich. Příklad: `[ $jmeno == "pavel" -o $jmeno == "honza" ]`.

**Kontrola úspěchu příkazu (`$?`):**
Speciální proměnná `$?` obsahuje návratový kód posledního provedeného příkazu (0 = úspěch, cokoliv jiného = chyba).

---

### 3. Nejčastější použití (Praktické ukázky 2. části)

**A. Práce se soubory a složkami (`-d`, `-f`, `-s`):**
Tento skript zkontroluje, zda existuje složka pro logy a zda v ní je soubor, který není prázdný, než ho začne zpracovávat.

```bash
#!/bin/bash

if [ -d "/var/log/app" -a -s "/var/log/app/error.log" ]; then
    echo "Logy ze složky /var/log/app jsou připraveny k odeslání."
else
    echo "CHYBA: Složka chybí nebo je log prázdný!"
fi
```

**B. Číselné porovnávání (`-gt`, `-lt`):**
Správce může chtít hlídat počet souborů v adresáři se zálohami. Pokud jich je víc než 5, vypíše varování.

```bash
#!/bin/bash

pocet_zaloh=$(ls -1 ~/zalohy | wc -l)

if [ "$pocet_zaloh" -gt 5 ]; then
    echo "VAROVÁNÍ: Ve složce je již $pocet_zaloh záloh. Zvaž smazání starých!"
fi
```

**C. Skládání podmínek (`-a`, `-o`, `!`):**
Ověření vstupu od uživatele. Musí zadat jméno A ZÁROVEŇ toto jméno nesmí být "root" (aby skript nespouštěl pod nebezpečným účtem).

```bash
#!/bin/bash

read -p "Zadej jméno pro nový účet: " jmeno

if [ ! -z "$jmeno" -a "$jmeno" != "root" ]; then
    echo "Jméno uživatele '$jmeno' je v pořádku."
else
    echo "CHYBA: Jméno nesmí být prázdné ani 'root'!"
fi
```

**D. Úspěch příkazu (`$?`):**
Místo pingu zkusíme zjistit, zda v systému existuje konkrétní uživatel pomocí příkazu `id`.

```bash
#!/bin/bash

read -p "Hledaný uživatel: " user
id "$user" > /dev/null 2>&1

if [ $? -eq 0 ]; then
    echo "Uživatel $user v systému existuje."
else
    echo "Uživatel $user nebyl nalezen!"
fi
```

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Kontrola složky a souboru**
Vytvoř skript, který zkontroluje, zda existuje složka `/zaloha` **A ZÁROVEŇ** v ní existuje soubor `posledni.bak`. Pokud platí oboje, vypíše: "Záloha je v pořádku".

**Úloha 2: Výběr uživatele (OR)**
Vytvoř skript, který načte jméno uživatele (`read`). Pokud je jméno "root" **NEBOR** "administrator", vypíše: "Uživatel má plná práva". Jinak vypíše: "Omezený přístup".

**Komplexní úloha: Inteligentní validátor souborů**
Vytvoř skript `validuj.sh`, který postupně projde tyto kontroly a u každé vypíše konkrétní chybu nebo úspěch:

1. Načte od uživatele cestu k souboru (příkaz `read`).
2. Pokud uživatel nezadal vůbec nic (vstup je prázdný), vypíše chybu a skončí.
3. Pokud zadaná cesta existuje, ale není to soubor (je to složka), vypíše chybu.
4. Pokud soubor existuje, ale je prázdný, vypíše chybu.
5. Pokud soubor existuje, má obsah A ZÁROVEŇ máš právo ho číst (`-r`), vypíše: "Soubor je v pořádku a připraven ke zpracování". Pokud ho číst nemůžeš, vypíše chybu o chybějících právech.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
#!/bin/bash
if [ -d "/zaloha" -a -f "/zaloha/posledni.bak" ]; then
    echo "Záloha je v pořádku"
fi
```

**Řešení 2:**

```bash
#!/bin/bash
read -p "Jméno: " jmeno
if [ "$jmeno" == "root" -o "$jmeno" == "administrator" ]; then
    echo "Uživatel má plná práva"
else
    echo "Omezený přístup"
fi
```

**Řešení komplexní úlohy:**

```bash
#!/bin/bash

read -p "Zadej cestu k souboru: " cesta

# 1. Prázdný vstup
if [ -z "$cesta" ]; then
    echo "Chyba: Cesta nebyla zadána!"
    exit 1
fi

# 2. Kontrola, zda nejde o složku
if [ -d "$cesta" ]; then
    echo "Chyba: Zadaná cesta je složka, nikoliv soubor."
    exit 1
fi

# 3. Kontrola existence a prázdnoty
if [ ! -f "$cesta" ]; then
    echo "Chyba: Soubor neexistuje."
elif [ ! -s "$cesta" ]; then
    echo "Chyba: Soubor je prázdný."
else
    # 4. Kontrola práv pro čtení
    if [ -r "$cesta" ]; then
        echo "Soubor $cesta je v pořádku a připraven."
    else
        echo "Chyba: Soubor nalezen, ale nemáš právo ho číst!"
    fi
fi
```
