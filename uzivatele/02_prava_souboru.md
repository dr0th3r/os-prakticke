# Práva souborů a složek: chmod a chown

### 1. Teoretický úvod (K čemu to slouží?)
V Linuxu má každý soubor a složka své majitele a sadu práv. Ta určují, kdo může soubor číst (`read`), měnit (`write`) nebo spouštět (`execute`). Bez správného nastavení práv by kdokoliv mohl smazat tvoje soukromá data nebo měnit důležité systémové konfigurace.

**V praxi to jako správce využiješ, když:**
- Vytvoříš skript a potřebuješ ho spustit (musíš mu dát právo `x`).
- Chceš, aby určitá složka byla přístupná jen jednomu uživateli (např. `/home/pavel`).
- Potřebuješ změnit majitele souboru, který jsi vytvořil jako root, aby ho mohl upravovat běžný uživatel.

---

### 2. Konfigurace a syntaxe
Práva se v Linuxu dělí na tři skupiny: **U** (User/Vlastník), **G** (Group/Skupina) a **O** (Others/Ostatní).

**Zobrazení práv:**
```bash
ls -l soubor.txt > prava.txt
```
Ve výpisu uvidíš řetězec jako `-rwxr-xr--`.
- `-`: Typ (soubor `-`, složka `d`).
- `rwx`: Práva vlastníka (čtení, zápis, spuštění).
- `r-x`: Práva skupiny (čtení, spuštění).
- `r--`: Práva ostatních (jen čtení).

**Změna majitele (Příkaz `chown`):**
```bash
chown pavel:studenti soubor.txt
```
- `pavel`: Nový vlastník souboru.
- `:studenti`: Nová skupina, které soubor patří.

**Změna práv (Příkaz `chmod`):**
Existují dva způsoby zápisu:
1. **Symbolický:** `chmod u+x soubor.sh` (Přidej vlastníku právo ke spuštění).
2. **Číselný:** `chmod 755 soubor.sh`.
    - `4`: Read (čtení)
    - `2`: Write (zápis)
    - `1`: Execute (spuštění)
    - Součet dává právo: `7 (4+2+1)` je plné právo, `5 (4+1)` je čtení a spuštění, `0` je žádné právo.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Rekurzivní změna majitele celého adresáře:**
```bash
chown -R pavel:studenti /var/www/html/
ls -ld /var/www/html/ > kontrola_majitele.txt
```
- **Co to dělá:** Přepínač `-R` (Recursive) zajistí, že se majitel změní nejen složce, ale i všem souborům a podsložkám uvnitř. Druhý příkaz ověří změnu a zapíše ji do souboru.

**B. Nastavení bezpečných práv pro webový server:**
```bash
chmod 644 index.html
chmod 755 /var/www/html/
ls -l index.html > prava_webu.txt
```
- **Co to dělá:** Soubor `index.html` bude moci majitel měnit (`6 = 4+2`), ale ostatní si ho jen přečtou. Složka musí mít právo `x` (`755`), aby do ní mohl systém "vstoupit".

**C. Odstranění práv pro ostatní (Soukromí):**
```bash
chmod o-rwx tajne_heslo.txt
ls -l tajne_heslo.txt > stav_tajne.txt
```
- **Co to dělá:** Přepínač `o-rwx` odebere (mínus `-`) všechna práva skupině "Ostatní". K souboru se teď dostane jen vlastník a lidé ve skupině.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Úplný základ (Symbolický zápis)**
Vytvoř prázdný soubor `skript.sh`. Pomocí příkazu `chmod` a symbolického zápisu mu přidej právo ke spuštění pro uživatele (`u`) a odeber právo k zápisu pro skupinu (`g`). Výsledek ověř výpisem `ls -l` a ulož do `prava_skriptu.txt`.

**Úloha 2: Číselný zápis a majitel**
Vytvoř složku `projekty`. Nastav jí práva tak, aby vlastník mohl vše (čtení, zápis, vstup), skupina jen číst a vstupovat, a ostatní neměli žádná práva. (Použij číselný zápis). Poté změň skupinu této složky na `studenti`. Stav ulož do `projekty_stav.txt`.

**Úloha 3: Hromadná změna (Rekurze)**
Máš složku `verejne` s mnoha soubory. Napiš příkaz, který všem souborům i složkám uvnitř nastaví vlastníka `root` a skupinu `root`. Zároveň nastav všem souborům právo jen ke čtení pro všechny (`444`). Výsledek ulož do `hromadny_report.txt`.

**Komplexní úloha:**
Jsi správce sítě a musíš vytvořit sdílenou složku pro oddělení IT:
1. Vytvoř složku `/opt/it_data`.
2. Změň majitele složky na uživatele `spravce` a skupinu na `it_oddeleni`.
3. Nastav práva tak, aby:
   - Vlastník (`spravce`) mohl vše.
   - Skupina (`it_oddeleni`) mohla číst a měnit soubory (zápis).
   - Ostatní (`others`) nemohli do složky ani vstoupit.
4. Do souboru `vysledek_it.txt` ulož podrobný výpis této složky, ze kterého bude jasně vidět nastavení práv a majitelů.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
touch skript.sh
chmod u+x,g-w skript.sh
ls -l skript.sh > prava_skriptu.txt
```
- **Vysvětlení:**
    - `u+x` přidá právo execute vlastníkovi.
    - `g-w` odebere právo write skupině.
    - Čárka v příkazu `chmod` umožňuje řetězit více změn najednou.

**Řešení 2:**
```bash
mkdir projekty
chmod 750 projekty
sudo chown :studenti projekty
ls -ld projekty > projekty_stav.txt
```
- **Vysvětlení:**
    - `7 (4+2+1)` – vlastník (rwx).
    - `5 (4+1)` – skupina (r-x).
    - `0` – ostatní (---).
    - `chown :studenti` změní pouze skupinu souboru (dvojtečka na začátku).

**Řešení 3:**
```bash
sudo chown -R root:root verejne/
sudo chmod -R 444 verejne/
ls -lR verejne/ > hromadny_report.txt
```
- **Vysvětlení:**
    - `-R` je nutné pro rekurzivní změnu celého obsahu adresáře.
    - `444` nastaví `r--r--r--` (všichni mohou jen číst).

**Řešení komplexní úlohy:**
```bash
# 1. Vytvoření (vyžaduje sudo pro /opt)
sudo mkdir -p /opt/it_data

# 2. Změna majitele a skupiny
sudo chown spravce:it_oddeleni /opt/it_data

# 3. Nastavení práv (Vlastník: 7, Skupina: 7, Ostatní: 0)
sudo chmod 770 /opt/it_data

# 4. Export pro kontrolu
ls -ld /opt/it_data > vysledek_it.txt
```
- **Vysvětlení:**
    - `770` znamená: `rwx` pro majitele, `rwx` pro skupinu a `---` pro ostatní.
    - `ls -ld` je důležité – přepínač `d` zajistí, že uvidíme práva samotné složky, nikoliv jejího obsahu.
    - Tato konfigurace je bezpečná, protože nikdo "zvenčí" do složky ani neuvidí.
