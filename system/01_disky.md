# Disková úložiště: Identifikace a zaplnění

### 1. Teoretický úvod (K čemu to slouží?)

Správce systému musí vědět, jaký hardware je k serveru připojen a jak je rozdělen. Musí umět identifikovat disky, jejich oddíly a sledovat, kolik na nich zbývá volného místa. Bez této znalosti bys nemohl k systému přidávat nové disky ani řešit situace, kdy se server zastaví kvůli zaplnění.

**V praxi to jako správce využiješ, když:**

- Připojíš k serveru nový disk a potřebuješ zjistit jeho název (např. `/dev/sdb`).
- Potřebuješ zjistit UUID (unikátní ID) disku pro jeho automatické připojení v `/etc/fstab`.
- Uživatelé si stěžují, že nemohou ukládat soubory, a ty musíš najít, který oddíl je plný.

---

### 2. Konfigurace a syntaxe

Linux přistupuje k diskům jako k souborům v adresáři `/dev/`. První disk je obvykle `sda`, druhý `sdb`. Jejich oddíly se pak číslují (např. `sda1`, `sda2`).

**Zobrazení struktury disků (Příkaz `lsblk`):**

```bash
lsblk > strom_disku.txt
```

- **lsblk**: (List Block Devices) Vypíše přehledný strom všech disků a jejich oddílů.
  - `-f`: (Filesystem) Přidá k výpisu informaci o souborovém systému (např. `ext4`) a bodu připojení (např. `/`).

**Podrobné informace o oddílech (Příkaz `fdisk -l`):**

```bash
sudo fdisk -l > tabulka_oddilu.txt
```

- **fdisk -l**: (List) Vypíše detailní tabulku oddílů, jejich přesnou velikost v sektorech a typ (Linux, Swap, EFI). Vyžaduje práva správce (`sudo`).

**Unikátní identifikace disků (Příkaz `blkid`):**

```bash
sudo blkid > seznam_uuid.txt
```

- **blkid**: (Block ID) Vypíše unikátní UUID každého oddílu. UUID se nemění ani při přepojení disku do jiného slotu, proto je klíčové pro spolehlivou konfiguraci v systému.

**Sledování zaplnění disků (Příkaz `df`):**

```bash
df -h > vyuziti_mista.txt
```

- **df**: (Disk Free) Zobrazí aktuální zaplnění všech připojených disků.
  - `-h`: (Human-readable) Vypíše velikosti v GB/MB místo bajtů.

**Zjištění velikosti složek a souborů (Příkaz `du`):**

```bash
du -sh /var/log/ > velikost_slozky.txt
```

- **du**: (Disk Usage) Spočítá skutečnou velikost obsahu na disku (složek i souborů).
  - `-s`: (Summary) Vypíše pouze celkový součet za celou složku, ne každý podadresář zvlášť.
  - `-h`: (Human-readable) Vypíše velikosti v GB/MB místo bloků.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Zjištění cesty k připojenému disku:**

```bash
lsblk -f > kontrola_pripojeni.txt
```

- **Co to dělá:** Tento příkaz ti hned ukáže, pod jakým jménem (např. `sda1`) je tvůj disk v systému a kam je připojen (sloupec `MOUNTPOINT`). Pokud u disku v tomto sloupci nic není, disk není připojen a systém s ním nemůže pracovat.

**B. Vyhledání UUID pro soubor /etc/fstab:**

```bash
sudo blkid /dev/sda1 > uuid_disku.txt
```

- **Co to dělá:** Vytáhne unikátní kód disku `sda1`. Pokud tento kód zapíšeš do konfiguračního souboru `/etc/fstab`, systém tento disk vždy po zapnutí bezpečně najde a připojí.

**C. Hledání "žroutů" místa v adresáři:**

```bash
du -h /var/log/ > rozpis_velikosti.txt
```

- **Co to dělá:** Příkaz `du -h` (bez přepínače -s) vypíše velikost každé podsložky zvlášť. To umožní v rámci velkého adresáře (jako je `/var/log`) přesně najít, která jeho část zabírá nejvíc místa.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Průzkum disků**
Vypiš seznam všech diskových zařízení v systému pomocí `lsblk` a výstup ulož do souboru `disky_info.txt`. Najdi v něm svůj hlavní disk a zjisti jeho velikost.

**Úloha 2: Diagnostika oddílů**
Použij příkaz `fdisk -l` k vypsání všech oddílů na tvém pevném disku. Výsledek ulož do souboru `oddily_podrobne.txt`. Najdi v něm řádek, kde je uveden typ souborového systému tvého hlavního oddílu.

**Úloha 3: Kontrola zaplnění**
Vypiš aktuální zaplnění všech disků v lidsky čitelné formě (v GB/MB). Výsledek ulož do souboru `zaplneni_report.txt`.

**Komplexní úloha:**
Maturitní zadání: "Audit diskového prostoru".

1. Do souboru `audit_disku.txt` ulož přehled všech připojených disků (použij `lsblk -f`).
2. Do stejného souboru přidej informaci o zaplnění kořenového adresáře `/` (použij `df -h /`).
3. Zjisti UUID oddílu, který je v systému připojen jako `/` (kořen), a přidej ho na konec souboru `audit_disku.txt`.
4. Nakonec do souboru přidej informaci o tom, kolik místa celkem zabírá adresář `/var` (použij `du -sh`).

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
lsblk > disky_info.txt
```

- **Vysvětlení:**
  - `lsblk` vypíše hierarchii disků a jejich oddílů. Velikost je uvedena ve sloupci `SIZE`.

**Řešení 2:**

```bash
sudo fdisk -l > oddily_podrobne.txt
```

- **Vysvětlení:**
  - Příkaz `fdisk -l` (vyžaduje sudo) ukazuje technické detaily. Hledej sloupec `Type`, kde bývá napsáno např. `Linux` nebo `Linux swap`.

**Řešení 3:**

```bash
df -h > zaplneni_report.txt
```

- **Vysvětlení:**
  - Přepínač `-h` je nezbytný pro srozumitelný výpis v GB (G) nebo MB (M). Sloupec `Use%` ukazuje zaplnění v procentech.

**Řešení komplexní úlohy:**

```bash
# 1. Přehled disků
lsblk -f > audit_disku.txt

# 2. Zaplnění kořenového adresáře
echo "--- Zaplnění / ---" >> audit_disku.txt
df -h / >> audit_disku.txt

# 3. UUID kořenového oddílu
echo "--- UUID kořene ---" >> audit_disku.txt
sudo blkid /dev/sda1 >> audit_disku.txt # (Pozn: Cestu /dev/sda1 nahraď podle lsblk)

# 4. Velikost /var
echo "--- Velikost složky /var ---" >> audit_disku.txt
du -sh /var >> audit_disku.txt
```

- **Vysvětlení:**
  - `lsblk -f` je nejlepší pro zjištění spojení mezi diskem a bodem připojení.
  - UUID získáme přesně pro ten oddíl, který je u `/` v `lsblk`.
  - Všechny výstupy kromě prvního přidáváme pomocí `>>`, aby se soubor nepřemazal.
