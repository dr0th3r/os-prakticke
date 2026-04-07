# Správa uživatelů v systému Linux

### 1. Teoretický úvod (K čemu to slouží?)
Linux je víceuživatelský systém. To znamená, že na jednom počítači (serveru) může pracovat mnoho lidí najednou a každý má své vlastní soukromé soubory a nastavení. Správce systému (root) musí umět tyto uživatele zakládat, měnit jim hesla a v případě potřeby je ze systému odstranit.

**V praxi to jako správce využiješ, když:**
- Do firmy nastoupí nový zaměstnanec a ty mu musíš vytvořit přístup k serveru.
- Uživatel zapomene heslo a ty mu ho musíš vyresetovat.
- Potřebuješ vytvořit speciálního "systémového" uživatele, pod kterým poběží jen určitý program (např. webový server).

---

### 2. Konfigurace a syntaxe
Všechny informace o uživatelích Linux ukládá do textového souboru `/etc/passwd`. Hesla jsou bezpečně zašifrovaná v souboru `/etc/shadow`.

**Základní příkazy pro správu uživatelů:**
```bash
useradd -m -s /bin/bash pavel
passwd pavel
userdel -r pavel
```

**Vysvětlení jednotlivých částí příkazů:**
- `useradd`: Hlavní příkaz pro přidání nového uživatele.
    - `-m`: (Make home) Automaticky vytvoří domovský adresář v `/home/pavel`. Bez tohoto přepínače se uživatel sice vytvoří, ale nebude mít kam ukládat svá data.
    - `-s /bin/bash`: (Shell) Určuje, jaký terminál bude uživatel používat. `/bin/bash` je standardní volba.
    - `pavel`: Jméno nového uživatele (login).
- `passwd`: Příkaz pro změnu hesla. Pokud ho spustíš jako root a za ním napíšeš jméno, změníš heslo danému uživateli.
- `userdel`: Příkaz pro smazání uživatele.
    - `-r`: (Recursive) Smaže uživatele i s jeho domovským adresářem a všemi soubory. Bez tohoto přepínače by v systému zůstalo "smetí".

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Vytvoření uživatele s expirací účtu (Dočasný pracovník):**
```bash
useradd -e 2026-12-31 brigadnik
cat /etc/passwd | grep "brigadnik" > info_uzivatel.txt
```
- **Co to dělá:** Příkaz vytvoří uživatele `brigadnik`, jehož účet se automaticky zablokuje na konci roku 2026. Druhý příkaz vyhledá záznam o tomto uživateli v systémové databázi a uloží ho do souboru pro kontrolu.

**B. Zablokování a odblokování uživatelského účtu:**
```bash
usermod -L pavel
usermod -U pavel
passwd -S pavel > stav_uctu.txt
```
- **Co to dělá:** `usermod -L` (Lock) "zamkne" účet uživatele `pavel` (nebude se moci přihlásit). `usermod -U` (Unlock) ho opět odemkne. Poslední příkaz zjistí stav účtu (zda je zamčený) a informaci uloží do souboru.

**C. Změna jména nebo domovské složky existujícího uživatele:**
```bash
usermod -l jan.novak jan
usermod -d /home/novy_home -m jan.novak
ls -ld /home/novy_home > kontrola_home.txt
```
- **Co to dělá:** První příkaz změní přihlašovací jméno z `jan` na `jan.novak`. Druhý příkaz mu změní domovskou složku a rovnou do ní přesune i všechna jeho stará data (`-m`). Třetí příkaz ověří existenci nové složky a zapíše ji do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Úplný základ**
Vytvoř nového uživatele s názvem `tester`. Zajisti, aby měl vytvořenou domovskou složku a jako výchozí terminál nastaven `/bin/bash`. Ověř jeho existenci v souboru `/etc/passwd` a řádek s ním ulož do `kontrola_tester.txt`.

**Úloha 2: Práce s heslem a stavy**
Nastav uživateli `tester` heslo. Poté jeho účet "zamkni", aby se nemohl přihlásit. Zjisti stav jeho účtu (příkaz `passwd -S`) a výsledek ulož do souboru `stav_tester.txt`.

**Úloha 3: Hromadná kontrola**
Vypiš seznam všech uživatelů v systému, kteří mají jako domovskou složku nastaven adresář začínající `/home/`. Výsledek (pouze jejich jména) ulož do souboru `seznam_skutecnych_uzivatelu.txt`. (Tip: použij `grep` a `awk`).

**Komplexní úloha:**
Jsi správce a dostal jsi za úkol připravit systém pro nového stážistu:
1. Vytvoř uživatele `stazista` s domovskou složkou.
2. Nastav mu, že se jeho účet automaticky zablokuje za 30 dní od dnešního data (pro účely cvičení použij libovolné datum v budoucnu).
3. Do souboru `report_stazista.txt` ulož informaci o tom, jaký má uživatel UID (ID číslo) a v jakých je skupinách (použij příkaz `id stazista`).
4. Nakonec uživatele `stazista` smaž i s jeho domovskou složkou a do logu `uklid.log` zapiš aktuální čas a potvrzení: "Uživatel smazán".

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
sudo useradd -m -s /bin/bash tester
grep "tester" /etc/passwd > kontrola_tester.txt
```
- **Vysvětlení:**
    - `useradd -m` vytvoří složku `/home/tester`.
    - `-s /bin/bash` zajistí, že po přihlášení uvidí standardní příkazovou řádku.
    - `grep` vytáhne z databáze uživatelů pouze řádek patřící tomuto uživateli.

**Řešení 2:**
```bash
sudo passwd tester
sudo usermod -L tester
passwd -S tester > stav_tester.txt
```
- **Vysvětlení:**
    - `usermod -L` přidá vykřičník před zašifrované heslo v souboru `/etc/shadow`, čímž zneplatní login.
    - `passwd -S` vypíše stav účtu (uvidíš tam `L` jako Locked).

**Řešení 3:**
```bash
grep "/home/" /etc/passwd | awk -F: '{ print $1 }' > seznam_skutecnych_uzivatelu.txt
```
- **Vysvětlení:**
    - `grep "/home/"` vybere pouze "skutečné" lidi (systémoví uživatelé mají domov jinde, např. ve `/var/lib`).
    - `awk -F: '{ print $1 }'` vezme řádek oddělený dvojtečkami a vytáhne jen první sloupec (jméno).

**Řešení komplexní úlohy:**
```bash
# 1. Vytvoření
sudo useradd -m -e 2025-06-01 stazista

# 2. Informace o ID a skupinách
id stazista > report_stazista.txt

# 3. Smazání
sudo userdel -r stazista

# 4. Zápis do logu
echo "$(date): Uživatel stazista smazán ze systému" > uklid.log
```
- **Vysvětlení:**
    - `useradd -e` nastaví datum expirace účtu (formát RRRR-MM-DD).
    - `id` je nejrychlejší způsob, jak zjistit UID, GID a skupiny uživatele.
    - `userdel -r` je nutné pro kompletní smazání všech dat uživatele, aby nezůstala v `/home/`.
    - Všechny operace vyžadují `sudo`, protože mění systémové konfigurační soubory.
