# Zjišťování síťových informací

### 1. Teoretický úvod (K čemu to slouží?)

Předtím, než začneš nastavovat firewall nebo router, musíš vědět, jak je na tom tvůj systém teď. Potřebuješ zjistit, jaké máš IP adresy, zda vidíš do internetu a hlavně – které služby (programy) ti v systému "naslouchají" na síti.

**V praxi to jako správce využiješ, když:**

- Potřebuješ zjistit, zda ti běží webový server a na jakém portu (80 nebo 443?).
- Chceš ověřit, jakou IP adresu dostal server od DHCP serveru.
- Řešíš problém, proč nejde internet (chyba v DNS nebo ve směrování?).

---

### 2. Konfigurace a syntaxe

Většina informací se zjišťuje pomocí příkazů balíčku `iproute2` (příkaz `ip`) a `iproute2-doc` (příkaz `ss`).

**Zobrazení IP adres a rozhraní:**

```bash
ip addr > ip_adresy.txt
```

- **ip**: Hlavní příkaz pro správu sítě.
- **addr**: (zkratka pro address) Zobrazí IP adresy na všech síťových kartách (eth0, lo, wlan0).

**Vyhledávání portů a běžících služeb:**

```bash
ss -tunlp > otevrene_porty.txt
```

- **ss**: (Socket Statistics) Moderní náhrada za starý `netstat`.
- **-t**: (TCP) Zobrazí TCP spojení.
- **-u**: (UDP) Zobrazí UDP spojení.
- **-n**: (Numeric) Zobrazí čísla portů místo názvů (např. 22 místo ssh).
- **-l**: (Listening) Zobrazí pouze porty, na kterých systém "poslouchá" (čeká na spojení).
- **-p**: (Process) Zobrazí název programu (procesu), kterému daný port patří.

**Zobrazení směrování a DNS:**

```bash
ip route > routovaci_tabulka.txt
cat /etc/resolv.conf > dns_servery.txt
```

- **ip route**: Ukáže, kudy tečou data do internetu (výchozí brána - default gateway).
- **/etc/resolv.conf**: Soubor, kde jsou uloženy IP adresy DNS serverů (překladače jmen).

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Zjištění IP adresy konkrétního rozhraní:**

```bash
ip addr show eth0
```

- **Co to dělá:** Vypíše podrobné informace pouze o síťové kartě `eth0`. Uvidíš tam IPv4 adresu (u slova `inet`) a stav karty (`UP` nebo `DOWN`).

**B. Zjištění, která služba zabírá port 80:**

```bash
ss -tunlp | grep ":80"
```

- **Co to dělá:** Příkaz `ss` vypíše vše, ale `grep` vyfiltruje jen řádek s portem 80. Ve sloupci "Process" uvidíš např. `apache2` nebo `nginx`.

**C. Kontrola výchozí brány (Default Gateway):**

```bash
ip route | grep default > vychozi_brana.txt
```

- **Co to dělá:** Vybere řádek začínající slovem `default`. IP adresa za slovem `via` je tvoje výchozí brána (router).

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Průzkum rozhraní**
Vypiš seznam všech síťových rozhraní v systému a výsledek ulož do souboru `moje_site.txt`. Zjisti z něj, jak se jmenuje tvá hlavní síťová karta (např. `ens33`, `eth0`).

**Úloha 2: Hledání SSH portu**
Pomocí příkazu `ss` zjisti, zda ti v systému běží SSH server. SSH standardně běží na portu 22. Výsledek (řádek s portem 22) ulož do `ssh_status.txt`.

**Komplexní úloha:**
Máš za úkol provést rychlou diagnostiku sítě na serveru. Vytvoř skript (nebo sérii příkazů), který do souboru `diagnostika.txt` postupně zapíše:

1. Aktuální IP adresu rozhraní `eth0` (nebo tvého hlavního rozhraní).
2. Seznam všech TCP portů, na kterých systém naslouchá, i s názvy programů.
3. IP adresu DNS serveru, který systém používá.
4. IP adresu výchozí brány (default gateway).

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
ip addr > moje_site.txt
```

- **Vysvětlení:** Příkaz vypíše všechna rozhraní. Rozhraní `lo` je smyčka (localhost), ostatní jsou fyzické nebo virtuální karty.

**Řešení 2:**

```bash
ss -tunlp | grep ":22" > ssh_status.txt
```

- **Vysvětlení:** Pokud je ve výstupu řádek s `:22` a procesem `sshd`, server běží a naslouchá.

**Řešení komplexní úlohy:**

```bash
echo "--- IP ADRESA ---" > diagnostika.txt
ip addr show eth0 | grep inet >> diagnostika.txt

echo "--- NASLOUCHAJÍCÍ PORTY ---" >> diagnostika.txt
ss -tlnp >> diagnostika.txt

echo "--- DNS SERVERY ---" >> diagnostika.txt
cat /etc/resolv.conf >> diagnostika.txt

echo "--- DEFAULT GATEWAY ---" >> diagnostika.txt
ip route | grep default >> diagnostika.txt
```

- **Vysvětlení:**
  - Použili jsme `grep inet`, abychom z výpisu `ip addr` vytáhli jen řádky s IP adresami.
  - Všechny výstupy kromě prvního přidáváme pomocí `>>`, aby se soubor nepřemazal.
  - `ss -tlnp` vypíše přehlednou tabulku běžících služeb.
