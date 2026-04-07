# Pokročilá správa - Logování a Statistiky

### 1. Teoretický úvod (K čemu to slouží?)
Když máš nastavený firewall, potřebuješ vědět, co se na něm reálně děje. Sledování statistik ti ukáže, jak moc je která služba vytížená (kolik dat proteklo), a logování ti pomůže odhalit útočníky nebo chyby v síti.

**V praxi to jako správce využiješ, když:**
- Chceš zjistit, kolik paketů uživatelé odeslali na port 80 (HTTP).
- Potřebuješ vidět, kdo se pokoušel přihlásit přes SSH (port 22) a byl zablokován.
- Chceš omezit rychlost pingu, aby tě někdo "nezaplavil" (Flood attack).
- Potřebuješ si "označkovat" určité pakety (Tagování), abys je mohl později lépe sledovat nebo prioritizovat.

---

### 2. Konfigurace a syntaxe
Pro pokročilé funkce používáme v `iptables` moduly a speciální akce.

**Sledování statistik (Počet paketů a bajtů):**
```bash
iptables -L -n -v > statistiky_firewallu.txt
```
- **-v**: (Verbose) Přidá k výpisu dva sloupce: **pkts** (počet paketů) a **bytes** (množství dat v bajtech).

**Logování provozu:**
```bash
iptables -A INPUT -p tcp --dport 22 -j LOG --log-prefix "SSH_ATTEMPT: "
```
- **-j LOG**: Speciální akce, která paket nezahodí ani nepustí, ale zapíše informaci o něm do systémového logu (`/var/log/syslog`).

**Tagování (Značkování) paketů:**
```bash
iptables -t mangle -A PREROUTING -p tcp --dport 80 -j MARK --set-mark 10
```
- **-t mangle**: Speciální tabulka pro úpravu paketů.
- **-j MARK --set-mark 10**: Paket dostane "neviditelný" štítek s číslem 10.

**Zjišťování informací o otagovaných paketech:**
```bash
iptables -t mangle -L -n -v | grep "MARK set 0xa" > statistika_tagu.txt
```
- **-t mangle**: Musíme se podívat do tabulky, kde tagování probíhá.
- **grep "MARK set 0xa"**: Hledáme pravidlo s naší značkou. Číslo 10 se v `iptables` vypisuje v šestnáctkové soustavě (hexadecimal) jako `0xa`.

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Sledování odeslaných paketů na port 80 (HTTP):**
```bash
iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
iptables -L OUTPUT -v -n > statistika_vystupu_80.txt
```
- **Co to dělá:** Povolí odchozí provoz na port 80 a vypíše statistiky. Ve sloupci `pkts` uvidíš, kolik požadavků na webové stránky z tohoto počítače odešlo.

**B. Logování pokusů o nepovolené SSH:**
```bash
iptables -A INPUT -p tcp --dport 22 -j LOG --log-prefix "SSH_BLOCKED: "
iptables -A INPUT -p tcp --dport 22 -j DROP
```
- **Co to dělá:** Každý pokus o SSH bude nejdříve zapsán do logu s předponou `SSH_BLOCKED` a hned poté zahozen.

**C. Sledování statistik pomocí tagů (Značkování):**
```bash
iptables -t mangle -A PREROUTING -p tcp --dport 22 -j MARK --set-mark 100
iptables -t mangle -L -n -v > vsechny_tagy.txt
```
- **Co to dělá:** V tabulce `mangle` označíme každý přicházející paket na SSH (22) značkou 100. Když pak vypíšeme tabulku `mangle` s přepínačem `-v`, uvidíme u tohoto pravidla počet paketů a bajtů, které dostaly tuto značku. (Poznámka: 100 je v hexu `0x64`).

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Statistiky rozhraní**
Vypiš podrobné statistiky (počet paketů a bajtů) pro všechny síťové karty. Výsledek ulož do souboru `sit_statistika.txt`.

**Úloha 2: Logování webového provozu**
Napiš příkaz, který do systémového logu zapíše každý příchozí požadavek na port 80 (HTTP) s předponou "WEB_ACCESSED: ". Výsledek ulož do skriptu `log_web.sh`.

**Úloha 3: Tagování a zobrazení statistik**
Napiš příkaz, který v tabulce `mangle` označkuje všechny příchozí TCP pakety na portu 443 (HTTPS) značkou `50`. Poté vypiš statistiky tabulky `mangle` do souboru `https_tagy.txt`.

**Komplexní úloha:**
Maturitní zadání: "Sleduj provoz na serveru pomocí tagů a logování".
1. Povol příchozí SSH (port 22) pro všechny.
2. Všechny příchozí TCP pokusy na ostatní porty loguj s předponou "REJECTED_TRY: " a poté je zahazuj (`DROP`).
3. V tabulce `mangle` označkuj značkou `20` každý paket, který odchází z tvého serveru na port 443 (HTTPS).
4. Vypiš aktuální statistiky tabulky `mangle` tak, aby bylo vidět, kolik paketů dostalo značku `20`. Výsledek ulož do `final_tag_report.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
iptables -L -v -n > sit_statistika.txt
```
- **Vysvětlení:** Přepínač `-v` (verbose) je klíčový pro zobrazení sloupců `pkts` a `bytes`.

**Řešení 2:**
```bash
iptables -A INPUT -p tcp --dport 80 -j LOG --log-prefix "WEB_ACCESSED: "
iptables-save > log_web.sh
```
- **Vysvětlení:** Logování se provádí akcí `-j LOG`. Předpona usnadňuje pozdější filtrování logu pomocí `grep`.

**Řešení 3:**
```bash
iptables -t mangle -A PREROUTING -p tcp --dport 443 -j MARK --set-mark 50
iptables -t mangle -L -n -v > https_tagy.txt
```
- **Vysvětlení:** Tagování probíhá v tabulce `mangle`. Ve výpisu `https_tagy.txt` uvidíme počet otagovaných HTTPS paketů (značka 50 je v hexu `0x32`).

**Řešení komplexní úlohy:**
```bash
# 1. Povolení SSH a logování zbytku
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp -j LOG --log-prefix "REJECTED_TRY: "
iptables -A INPUT -p tcp -j DROP

# 2. Tagování odchozího HTTPS (značka 20)
iptables -t mangle -A POSTROUTING -p tcp --dport 443 -j MARK --set-mark 20

# 3. Výpis statistik tabulky mangle
iptables -t mangle -L -n -v > final_tag_report.txt
```
- **Vysvětlení:** 
  - V reportu `final_tag_report.txt` uvidíš u pravidla v `POSTROUTING` sloupec `pkts`, který ukazuje počet odeslaných HTTPS paketů.
  - Značka 20 se v textovém výpisu `iptables` zobrazí jako `MARK set 0x14`.
  - Pořadí v `INPUT` zajišťuje, že SSH je povoleno a vše ostatní zapsáno do logu a zahozeno.
