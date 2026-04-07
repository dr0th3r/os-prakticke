# Přesměrování a řetězení příkazů

### 1. Teoretický úvod (K čemu to slouží?)

V Linuxu málokdy používáme příkazy izolovaně. Skutečná síla terminálu spočívá v tom, že dokážeme výstup jednoho příkazu předat do vstupu druhého, ukládat výsledky do souborů nebo spouštět příkazy podmíněně podle toho, zda ten předchozí uspěl.

**V praxi to jako správce využiješ, když:**

- Potřebuješ uložit výpis běžících procesů do logu pro pozdější analýzu.
- Chceš vyhledat konkrétní chybu v logu a rovnou ji spočítat nebo smazat.
- Potřebuješ automatizovaně vytvořit složku a hned do ní vejít, ale jen pokud se vytvoření povedlo.
- Chceš hromadně smazat soubory, které ti vyhledal příkaz `find`.

---

### 2. Přesměrování (Práce se soubory)

Přesměrování určuje, kam má téct výstup příkazu (standardně do terminálu) nebo odkud má brát vstup (standardně z klávesnice).

| Operátor | Význam                           | Příklad                 |
| :------- | :------------------------------- | :---------------------- |
| `>`      | Přepsat soubor výstupem          | `ls > vypis.txt`        |
| `>>`     | Přidat výstup na konec souboru   | `date >> log.txt`       |
| `<`      | Číst vstup ze souboru            | `wc -l < seznam.txt`    |
| `2>`     | Přesměrovat pouze chyby          | `ls /root 2> chyby.txt` |
| `&>`     | Přesměrovat vše (výstup i chyby) | `prikaz &> vse.txt`     |

**Důležité pravidlo:** Operátor `>` bez milosti smaže původní obsah souboru a nahradí ho novým. Pokud chceš data pouze přidávat, vždy používej `>>`.

---

### 3. Zřetězení (Propojování příkazů)

Zřetězení nám umožňuje stavět komplexní "potrubí" (pipelines) nebo určovat logiku spouštění.

**Trubka (Pipe `|`):**
Posílá textový výstup prvního příkazu přímo do vstupu druhého příkazu.

```bash
ps aux | grep "ssh" > procesy_ssh.txt
```

**Logické operátory:**

- `&&` (AND): Druhý příkaz se spustí **pouze pokud první uspěl** (skončil bez chyby).
- `||` (OR): Druhý příkaz se spustí **pouze pokud první selhal**.
- `;` (Středník): Druhý příkaz se spustí **vždy**, bez ohledu na výsledek prvního.

```bash
mkdir backup && cp data.txt backup/ > vysledek.txt
ls soubor.txt || echo "Soubor neexistuje" > error.txt
```

---

### 4. Příkaz xargs (Most pro argumenty)

`xargs` je speciální nástroj, který řeší častý problém: Některé příkazy (jako `rm`, `kill`, `cp`) neumí číst text z trubky (ze standardního vstupu), ale vyžadují ho jako parametr (argument) přímo za sebou.

`xargs` vezme to, co přišlo z trubky, a "přilepí" to jako argument za cílový příkaz.

```bash
# ŠPATNĚ: rm nebude vědět, co má mazat
ls *.tmp | rm

# SPRÁVNĚ: xargs předá názvy souborů příkazu rm
ls *.tmp | xargs rm > smazano.txt
```

---

### 5. Nejčastější použití (Praktické ukázky)

**A. Hromadné vyhledávání v souborech:**

```bash
ls *.txt | xargs grep "chyba" > nalezeno.txt
```

- `ls *.txt`: Vypíše všechny textové soubory.
- `xargs grep "chyba"`: Vezme názvy souborů z výstupu a v každém z nich začne hledat slovo "chyba". Výsledky zapíše do souboru.

**B. Bezpečné vytvoření složky:**

```bash
mkdir projekty && touch projekty/index.html > status.txt
```

- Druhá část se spustí jen tehdy, pokud se složku `projekty` podařilo vytvořit (např. pokud tam již není stejnojmenný soubor).

**C. Záloha s hlášením o chybě:**

```bash
cp data.txt zaloha.txt || echo "Soubor data.txt nebyl nalezen" > error.txt
```

- Pokud kopírování selže (např. soubor neexistuje), vypíše se upozornění do chybového logu.

---

### 6. Cvičení pro studenta (Procvičování)

**Úloha 1: Práce s výstupem**
Vypiš aktuální datum a čas a ulož ho do souboru `cas.txt`. Poté do stejného souboru přidej text "Konec zapisu" tak, aby se původní datum nesmazalo.

**Úloha 2: Diagnostika s chybami**
Zkus vypsat obsah adresáře `/root` (ke kterému pravděpodobně nemáš přístup). Standardní výstup zahoď (pošli do `/dev/null`) a chybové hlášení ulož do souboru `nedostupne.txt`.

**Úloha 3: Inteligentní práce s adresáři**
Napiš příkaz, který se pokusí vejít do složky `doklady`. Pokud se to podaří, vypíše v ní seznam souborů do `vypis.txt`. Pokud složka neexistuje, vypíše do souboru `log.txt` zprávu "Chyba: Slozka nenalezena".

**Komplexní úloha:**
Maturitní scénář: "Analýza logu a statistika".

1. Pomocí `cat` přečti soubor `log.txt` a pomocí `grep` z něj vyber řádky obsahující slovo "ERROR".
2. Tento seznam seřaď a ulož do souboru `chyby.txt`.
3. Pokud se vše podařilo, spočítej počet řádků v novém souboru a výsledek přidej na konec souboru `statistiky.txt`. (Tip: použij `wc -l` a operátor `>>`).

---

### 7. Řešení cvičení

**Řešení 1:**

```bash
date > cas.txt
echo "Konec zapisu" >> cas.txt
```

- **Vysvětlení:** První příkaz vytvoří soubor, druhý operátor `>>` přidá data na konec bez smazání hlavičky.

**Řešení 2:**

```bash
ls /root > /dev/null 2> nedostupne.txt
```

- **Vysvětlení:** `> /dev/null` "umlčí" běžný výstup, `2>` zachytí pouze chybové hlášení "Permission denied".

**Řešení 3:**

```bash
cd doklady && ls > vypis.txt || echo "Chyba: Slozka nenalezena" > log.txt
```

- **Vysvětlení:** `&&` provede `ls` jen při úspěchu `cd`. Pokud `cd` selže, provede se část za `||`.

**Řešení komplexní úlohy:**

```bash
grep "ERROR" log.txt | sort > chyby.txt && wc -l < chyby.txt >> statistiky.txt
```

- **Vysvětlení:**
  - `grep "ERROR" | sort`: Vybere chyby a srovná je.
  - `&&`: Pokud se soubor `chyby.txt` úspěšně vytvořil, pokračujeme k počítání.
  - `wc -l < chyby.txt`: Spočítá řádky v souboru.
  - `>> statistiky.txt`: Přidá výsledek na konec statistického souboru.
