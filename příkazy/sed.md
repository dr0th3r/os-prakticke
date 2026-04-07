# Příkaz sed

### 1. Teoretický úvod (K čemu to slouží?)

Příkaz `sed` (Stream Editor) slouží k úpravě textu za běhu, aniž bys musel soubor otevírat v textovém editoru (jako nano nebo vim). Funguje tak, že čte vstup po řádcích, provede na nich zadanou změnu a výsledek vypíše.

**V praxi to jako správce využiješ, když:**

- Potřebuješ hromadně nahradit určité slovo v celém konfiguračním souboru (např. změnit IP adresu nebo port).
- Chceš smazat konkrétní řádky ze souboru (např. prázdné řádky nebo komentáře).
- Potřebuješ automatizovaně upravit text v rámci skriptu.

---

### 2. Konfigurace a syntaxe

`sed` je standardní nástroj Linuxu, který nevyžaduje žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**

```bash
sed 's/původní/nový/g' soubor.txt > upravený.txt
```

**Vysvětlení jednotlivých částí příkazu:**

- `sed`: Spouští samotný program.
- `'s/.../.../g'`: Samotný příkaz pro úpravu textu.
  - `s`: Znamená substitute (nahradit).
  - `/`: Oddělovač částí příkazu.
  - `původní`: Text, který hledáme.
  - `nový`: Text, kterým chceme původní text nahradit.
  - `g`: (Global) Znamená, že se nahradí všechny výskyty na řádku, nejen ten první.
- `soubor.txt`: Vstupní soubor, který chceme číst.
- `> upravený.txt`: Přesměrování výstupu do nového souboru.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Nahrazení cesty k souboru (Změna oddělovače):**

```bash
sed 's|/var/www/html|/opt/web|g' /etc/apache2/sites-available/000-default.conf > apache_cesta.txt
```

- **Co to dělá:** Místo standardního `/` používáme jako oddělovač `|`. `sed` tak snadno nahradí celou cestu k webové složce, aniž by se pletlo lomítko v cestě s lomítkem v příkazu. Výsledek uloží do souboru.

**B. Smazání řádků začínajících komentářem (Příkaz `d` a regulární výraz `^`):**

```bash
sed '/^#/d' /etc/fstab > fstab_bez_komentaru.txt
```

- **Co to dělá:** Symbol `^` říká, že znak `#` musí být na začátku řádku. Příkaz `d` (Delete) tyto řádky smaže z výstupu. Výsledek uloží do souboru.

**C. Výpis pouze určité části souboru (Přepínač `-n` a příkaz `p`):**

```bash
sed -n '10,20p' /var/log/syslog > prvních_deset.txt
```

- **Co to dělá:** Přepínač `-n` (No-print) zastaví automatický výpis všech řádků. Příkaz `10,20p` pak řekne `sed`, aby vybral řádky 10 až 20 a vytiskl (`p`) pouze je. Výsledek uloží do souboru.

**C. Vyhledání a úprava řetězce pomocí regulárního výrazu (Regex `.` a `*`):**

```bash
sed 's/User.*/User anonymous/g' /etc/vsftpd.conf > vsftpd_upraveno.txt
```

- **Co to dělá:** Regex `.*` znamená "vše, co následuje za slovem User". `sed` tedy najde jakýkoliv řádek začínající na `User` (např. `User pavel`) a celý ho nahradí za `User anonymous`. Výsledek uloží do souboru.

**D. Provedení více úprav najednou (Řetězení pomocí středníku `;`):**

```bash
sed 's/Error/Chyba/g; s/Warning/Varovani/g' /var/log/syslog > log_cz.txt
```

- **Co to dělá:** Středník umožňuje spojit dva příkazy dohromady. `sed` nejprve na každém řádku nahradí "Error" za "Chyba" a následně na tomtéž řádku nahradí "Warning" za "Varovani". Výsledek uloží do souboru.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Základní nahrazování**
Vytvoř kopii souboru `/etc/hosts` a v ní nahraď všechny výskyty slova `localhost` slovem `moje-pc`. Výsledek ulož do souboru `hosts_upraveny.txt`.

**Úloha 2: Výběr řádků**
Vypiš pouze prvních 5 řádků ze souboru `/etc/passwd` pomocí příkazu `sed`. Výstup ulož do souboru `prvních_pet.txt`. (Tip: použij přepínač `-n` a příkaz `1,5p`).

**Úloha 3: Práce s regulárními výrazy**
Máš soubor `nastaveni.txt`, kde jsou řádky typu `ServerName=moje-domena.cz`. Pomocí `sed` a regulárního výrazu nahraď vše, co je za rovnítkem, slovem `univerzalni-server`. Výsledek ulož do `nastaveni_upraveno.txt`.

**Komplexní úloha:**
Máš soubor `/etc/ssh/sshd_config`. Pomocí jednoho příkazu `sed` (odděluj úkoly středníkem `;`) proveď tyto tři kroky najednou:

1. Odstraň všechny zakomentované řádky (začínající `#`).
2. Odstraň všechny prázdné řádky (použij regulární výraz `^$`).
3. Nahraď vše za slovem `PermitRootLogin` slovem `no` pouze v rozsahu řádků 10 až 30.
   Výsledek ulož do `sshd_upraveno.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
sed 's/localhost/moje-pc/g' /etc/hosts > hosts_upraveny.txt
```

- **Vysvětlení:** Používáme příkaz `s` (substitute) pro nahrazení hledaného textu novým textem.

**Řešení 2:**

```bash
sed -n '1,5p' /etc/passwd > prvních_pet.txt
```

- **Vysvětlení:** Přepínač `-n` vypne automatický tisk všech řádků a `1,5p` vytiskne jen řádky v zadaném rozsahu.

**Řešení 3:**

```bash
sed 's/ServerName=.*/ServerName=univerzalni-server/g' nastaveni.txt > nastaveni_upraveno.txt
```

- **Vysvětlení:** Regex `.*` po rovnítku vybere jakýkoliv text, který tam následuje, a nahradí ho za náš nový text.

**Řešení komplexní úlohy:**

```bash
sed '/^#/d; /^$/d; 10,30s/PermitRootLogin.*/PermitRootLogin no/g' /etc/ssh/sshd_config > sshd_upraveno.txt
```

- **Vysvětlení:** Středník odděluje tři úkoly.
  - První část (`/^#/d`) odstraní komentáře
  - Druhá (`/^$/d`) prázdné řádky
  - Třetí (`10,30s/.../g`) provede nahrazení všeho za klíčovým slovem pouze v zadaném rozsahu řádků.
