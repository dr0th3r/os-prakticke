# Příkaz tr

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `tr` (Translate) slouží k rychlému nahrazování nebo mazání jednotlivých znaků v textu. Na rozdíl od `sed`, který umí nahrazovat celá slova, `tr` pracuje na úrovni jednotlivých znaků (písmeno po písmenu).

**V praxi to jako správce využiješ, když:**

- Potřebuješ převést všechna písmena v souboru z malých na velká (např. u jmen uživatelů).
- Chceš ze souboru odstranit určité znaky (např. umazat konce řádků z Windows nebo odstranit závorky).
- Potřebuješ nahradit jeden oddělovač jiným (např. změnit středníky na mezery).

---

### 2. Konfigurace a syntaxe

`tr` je standardní nástroj Linuxu a nevyžaduje žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**

```bash
cat soubor.txt | tr 'původní_znaky' 'nové_znaky' > upravený.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `tr`: Spouští samotný program.
- `'původní_znaky'`: Seznam znaků, které chceme v textu vyhledat.
- `'nové_znaky'`: Seznam znaků, kterými chceme ty původní nahradit (ve stejném pořadí).
- `cat soubor.txt |`: Příkaz `tr` neumí číst soubory přímo, proto mu musíme data poslat rourou (`|`).
- `> upravený.txt`: Přesměrování výstupu do nového souboru.

**Nejčastější třídy znaků:**

- `[:lower:]`: Všechna malá písmena (a-z).
- `[:upper:]`: Všechna velká písmena (A-Z).
- `[:digit:]`: Všechny číslice (0-9).
- `[:alpha:]`: Všechna písmena (malá i velká).
- `[:alnum:]`: Všechna písmena i číslice (alfanumerické znaky).
- `[:space:]`: Všechny druhy mezer (mezery, tabulátory, konce řádků).
- **Další třídy můžeme najít s pomocí příkazu** `man tr`

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Převod textu na velká písmena (Třídy znaků):**

```bash
cat jmena.txt | tr '[:lower:]' '[:upper:]' > jmena_velka.txt
```

- **Co to dělá:** Příkaz vezme každé malé písmeno (`[:lower:]`) a nahradí ho jeho velkou variantou (`[:upper:]`). Výsledek uloží do souboru.

**B. Smazání konkrétních znaků (Přepínač `-d`):**

```bash
echo "(123) 456-789" | tr -d '() -' > telefonni_cislo.txt
```

- **Co to dělá:** Přepínač `-d` (Delete) smaže ze vstupu všechny znaky uvedené v uvozovkách (závorky, mezery, pomlčky). Výsledek (čisté číslo) uloží do souboru.

**C. Nahrazení oddělovačů (Jednoduchá záměna):**

```bash
cat data.csv | tr ';' ',' > data_carky.txt
```

- **Co to dělá:** Projde celý vstup a každý středník `;` nahradí čárkou `,`. To je užitečné při převodu formátu CSV souborů. Výsledek uloží do souboru.

**D. Odstranění duplicitních znaků (Přepínač `-s`):**

```bash
echo "Text    s    mnoha    mezerami" | tr -s ' ' > text_cisty.txt
```

- **Co to dělá:** Přepínač `-s` (Squeeze) nahradí vícenásobný výskyt znaku za sebou (zde mezery) pouze jedním výskytem. Výsledek uloží do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní záměna**
Vytvoř soubor `test.txt` s libovolným textem. Pomocí `tr` v něm nahraď všechny tečky `.` vykřičníky `!`. Výsledek ulož do `test_vystup.txt`.

**Úloha 2: Čištění textu**
Máš soubor `hesla.txt`, který obsahuje náhodné znaky včetně číslic. Pomocí `tr` ze souboru odstraň všechny číslice (použij třídu `[:digit:]`). Výsledek ulož do `hesla_bez_cislic.txt`.

**Úloha 3: Formátování jmen**
Máš seznam uživatelů v souboru `uzivatele.txt` napsaný velkými písmeny. Převeď je na malá písmena a zároveň nahraď všechny mezery podtržítky `_`. Výsledek ulož do `uzivatele_format.txt`.

**Komplexní úloha:**
Máš soubor `raw_data.txt`, který obsahuje věty s mnoha přebytečnými mezerami, čárkami a tečkami. Tvým úkolem je:

1. Odstranit z textu všechny čárky a tečky.
2. Nahradit všechny vícenásobné mezery za sebou pouze jednou mezerou.
3. Celý výsledný text převést na velká písmena.
   Výsledek ulož do `data_finalni.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
cat test.txt | tr '.' '!' > test_vystup.txt
```

- **Vysvětlení:** Jednoduchá záměna znaku za znak.

**Řešení 2:**

```bash
cat hesla.txt | tr -d '[:digit:]' > hesla_bez_cislic.txt
```

- **Vysvětlení:** Přepínač `-d` v kombinaci s třídou `[:digit:]` vymaže všechny číslice 0-9.

**Řešení 3:**

```bash
cat uzivatele.txt | tr '[:upper:]' '[:lower:]' | tr ' ' '_' > uzivatele_format.txt
```

- **Vysvětlení:** První `tr` převede text na malá písmena. Výsledek pošleme dalšímu `tr`, který nahradí mezery podtržítky.

**Řešení komplexní úlohy:**

```bash
cat raw_data.txt | tr -d '.,' | tr -s ' ' | tr '[:lower:]' '[:upper:]' > data_finalni.txt
```

- **Vysvětlení:**
  - `tr -d '.,'` smaže tečky a čárky.
  - `tr -s ' '` smrskne vícenásobné mezery do jedné.
  - `tr '[:lower:]' '[:upper:]'` převede vše na velká písmena.
- **Komentář:** Výsledek teče rourou skrz tři příkazy `tr` a nakonec se zapíše do cílového souboru.
