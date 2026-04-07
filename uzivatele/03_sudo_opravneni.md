# Sudo a oprávnění správce

### 1. Teoretický úvod (K čemu to slouží?)
V Linuxu existuje jeden super-uživatel: **root**. Ten může v systému cokoliv – smazat všechny soubory, vypnout síť nebo změnit heslo komukoliv. Je ale nebezpečné pracovat jako root neustále. Proto existuje příkaz `sudo` (SuperUser Do). Ten umožní běžnému uživateli provést jeden konkrétní příkaz s právy roota.

**V praxi to jako správce využiješ, když:**
- Chceš povolit určitému uživateli (např. IT technikovi) instalovat programy, ale nechceš mu dávat heslo k rootovi.
- Potřebuješ, aby uživatel mohl restartovat webový server, ale nic jiného v systému měnit nesměl.
- Chceš mít přehled (log), kdo a kdy v systému prováděl změny s právy správce.

---

### 2. Konfigurace a syntaxe
Hlavní konfigurační soubor pro `sudo` je `/etc/sudoers`. Ten se **nikdy** neupravuje přímo v textovém editoru, ale pomocí speciálního nástroje `visudo`.

**Základní syntaxe v sudoers:**
```text
uživatel    host=(všechny_uživatelé:všechny_skupiny)   příkazy
pavel       ALL=(ALL:ALL) ALL
```
- `pavel`: Jméno uživatele, který dostává práva.
- `ALL=(ALL:ALL)`: Uživatel může spouštět příkazy na všech strojích pod jakýmkoliv uživatelem.
- `ALL`: Uživatel může spouštět úplně všechny příkazy.

**Skupina `sudo` (Debian/Ubuntu):**
Nejjednodušší způsob, jak dát uživateli práva správce, je přidat ho do systémové skupiny `sudo`.
```bash
usermod -aG sudo pavel
groups pavel > seznam_skupin.txt
```
- `-aG`: (Append Group) Přidá uživatele do další skupiny, aniž by ho odebral z těch stávajících.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Kontrola, zda má uživatel práva k sudo:**
```bash
sudo -l > moje_opravneni.txt
```
- **Co to dělá:** Vypíše seznam všech příkazů, které aktuálně přihlášený uživatel smí spouštět pomocí `sudo`. Pokud tam uvidíš `(ALL : ALL) ALL`, máš plná práva správce. Výsledek se uloží do souboru.

**B. Spuštění příkazu jako jiný uživatel (ne jako root):**
```bash
sudo -u student touch soubor_studenta.txt
ls -l soubor_studenta.txt > kontrola_vlastnika.txt
```
- **Co to dělá:** Přepínač `-u` (User) umožní spustit příkaz pod jiným jménem než root. V tomto případě vytvoříme soubor, jehož vlastníkem bude automaticky `student`. Druhý příkaz to ověří a zapíše do souboru.

**C. Sudo bez nutnosti zadávat heslo (Pro skripty):**
V souboru `/etc/sudoers` (přes `visudo`) se to nastavuje takto:
```text
pavel ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart apache2
```
- **Co to dělá:** Uživatel `pavel` bude moci restartovat webový server bez toho, aby ho systém pokaždé otravoval dotazem na heslo. To je nezbytné pro automatické skripty.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Přidání administrátora**
Vytvoř nového uživatele `technik`. Přidej ho do skupiny `sudo`, aby mohl spravovat systém. Ověř, zda je ve skupině správně zapsán, a výpis skupin ulož do souboru `technik_skupiny.txt`.

**Úloha 2: Práce s identitou**
Použij příkaz `sudo -u nobody whoami` a výsledek ulož do souboru `kdo_jsem.txt`. (Příkaz `whoami` vypíše jméno aktuálního uživatele). Zjisti tak, pod jakou identitou se příkaz skutečně spustil.

**Úloha 3: Seznam práv**
Vypiš všechna svoje aktuální sudo oprávnění (příkaz `sudo -l`) a výsledek ulož do souboru `moje_prava.txt`. Najdi v něm řádek, který ti dovoluje spouštět příkazy jako root.

**Komplexní úloha:**
Jsi hlavní správce a musíš nastavit omezená práva pro junior administrátora:
1. Vytvoř uživatele `junior`.
2. Do souboru `sudo_nastaveni.txt` napiš (pouze jako text, neupravuj sudoers přímo!), jak by vypadal řádek v `/etc/sudoers`, který by uživateli `junior` dovolil pouze restartovat službu `sshd` a nic jiného.
3. Vyzkoušej, zda uživatel `junior` může bez sudo číst soubor `/etc/shadow`. Výsledek pokusu (chybovou hlášku) ulož do `pristup_chyba.log`.
4. Nakonec uživatele `junior` ze systému úplně odstraň.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
sudo useradd -m technik
sudo usermod -aG sudo technik
groups technik > technik_skupiny.txt
```
- **Vysvětlení:**
    - `usermod -aG sudo` přidá uživatele k administrátorům.
    - Změna se projeví až při příštím přihlášení uživatele `technik`.

**Řešení 2:**
```bash
sudo -u nobody whoami > kdo_jsem.txt
```
- **Vysvětlení:**
    - `nobody` je speciální systémový uživatel s minimálními právy.
    - Přestože jsi příkaz spustil ty, systém ho provedl pod jménem `nobody`.

**Řešení 3:**
```bash
sudo -l > moje_prava.txt
```
- **Vysvětlení:**
    - Pokud jsi ve skupině `sudo`, uvidíš tam pravděpodobně `%sudo ALL=(ALL:ALL) ALL`.
    - Znak `%` znamená, že pravidlo platí pro celou skupinu, ne jen pro jednoho uživatele.

**Řešení komplexní úlohy:**
```bash
# 1. Vytvoření
sudo useradd -m junior

# 2. Text konfigurace (vzor pro sudoers)
echo "junior ALL=(ALL) /usr/bin/systemctl restart sshd" > sudo_nastaveni.txt

# 3. Pokus o čtení shadow (selže)
cat /etc/shadow 2> pristup_chyba.log

# 4. Smazání
sudo userdel -r junior
```
- **Vysvětlení:**
    - Soubor `/etc/shadow` obsahuje zašifrovaná hesla a smí ho číst pouze root. Běžný uživatel (i junior bez sudo) dostane chybu "Permission denied".
    - Přesměrování `2>` zachytí právě chybovou hlášku o nepovoleném přístupu.
    - Specifikace celého příkazu `/usr/bin/systemctl restart sshd` v sudoers je nejbezpečnější způsob, jak dát uživateli jen tu moc, kterou skutečně potřebuje (princip minimálních privilegií).
    - `userdel -r` smaže uživatele i s jeho domovskou složkou.
