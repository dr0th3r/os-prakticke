# Základy Firewallu - iptables

### 1. Teoretický úvod (K čemu to slouží?)

`iptables` je nástroj pro správu síťového provozu přímo v jádře Linuxu. Funguje jako "strážce u brány". Každý paket, který do počítače přijde nebo z něj odejde, musí projít sadou pravidel, která určí, co se s ním stane.

**V praxi to jako správce využiješ, když:**

- Chceš zakázat přístup k webovému serveru všem kromě vybraných IP adres.
- Potřebuješ povolit SSH připojení jen pro vybrané administrátory.
- Chceš zabránit tomu, aby tvůj server posílal data na určité nebezpečné IP adresy.

---

### 2. Konfigurace a syntaxe

Základem `iptables` jsou **tabulky** (nejčastěji `filter`) a v nich **řetězce** (chains).

**Základní řetězce (Chains):**

- **INPUT**: Pakety směřující **DO** tohoto počítače.
- **OUTPUT**: Pakety směřující **Z** tohoto počítače ven.

**Akce (Targets):**

- **ACCEPT**: Povolit (pustit dál).
- **DROP**: Zahodit (tichý konec, odesílatel nic neví).
- **REJECT**: Odmítnout (pošle zprávu o odmítnutí).

**Základní syntaxe příkazu:**

```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

- **iptables**: Název programu.
- **-A**: (Append) Přidat pravidlo na konec řetězce.
- **-p tcp**: (Protocol) Určuje protokol (tcp, udp, icmp).
- **--dport 22**: (Destination Port) Určuje cílový port.
- **-j ACCEPT**: (Jump) Určuje, co se má s paketem stát.

**Užitečné příkazy pro správu:**

- `iptables -L -n`: Vypíše aktuální pravidla.
- `iptables -F`: Smaže (vyčistí) všechna aktuální pravidla.
- `iptables-save > firewall.rules`: Uloží pravidla do souboru.

---

### 3. Nejčastější použití (Praktické ukázky)

**A. Povolení SSH a zakázání všeho ostatního (Bezpečnost):**

```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -P INPUT DROP
```

- **Co to dělá:** První příkaz povolí vstup na port 22 (SSH). Druhý příkaz nastaví **výchozí politiku** (`-P`) na `DROP`. To znamená, že cokoli není výslovně povoleno, bude zahozeno.

**B. Blokování odchozí komunikace na konkrétní IP:**

```bash
iptables -A OUTPUT -d 1.2.3.4 -j REJECT
```

- **Co to dělá:** Přepínač `-d` (Destination) slouží k určení cílové adresy. Serveru se nepodaří odeslat žádný paket na IP `1.2.3.4`, protože ho firewall odmítne.

**C. Zakázání pingu (ICMP protokol):**

```bash
iptables -A INPUT -p icmp -j DROP
```

- **Co to dělá:** Systém přestane odpovídat na příkaz `ping`. Paket typu `icmp` bude okamžitě zahozen.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Povolení webového serveru**
Napiš příkaz, který povolí příchozí provoz na portu 80 (HTTP) pro všechny uživatele. Aktuální seznam pravidel ulož do souboru `firewall_web.txt`.

**Úloha 2: Blokování konkrétní IP adresy**
Napiš příkaz, který zakáže (pomocí `REJECT`) veškerý příchozí provoz z IP adresy `192.168.1.50`. Ověř výsledek výpisem pravidel a ulož ho do `blokovano.txt`.

**Komplexní úloha:**
Nastav Linux tak, aby fungoval jako bezpečný webový server:

1. Smaž všechna stará pravidla.
2. Povol příchozí SSH (port 22) pouze z IP adresy `192.168.1.10`.
3. Povol příchozí HTTP (port 80) pro kohokoliv.
4. Nastav výchozí politiku pro INPUT na `DROP` (vše ostatní zakázáno).
5. Ulož celou tuto konfiguraci do souboru `server_firewall.sh`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -L -n > firewall_web.txt
```

- **Vysvětlení:** Použili jsme TCP protokol a cílový port 80. Výpis `-n` (numeric) je přehlednější, protože nepřevede čísla portů na názvy služeb.

**Řešení 2:**

```bash
iptables -A INPUT -s 192.168.1.50 -j REJECT
iptables -L -n > blokovano.txt
```

- **Vysvětlení:** Přepínač `-s` (source) slouží k určení zdrojové IP adresy. `REJECT` dá útočníkovi vědět, že je blokován (narozdíl od tichého `DROP`).

**Řešení komplexní úlohy:**

```bash
# 1. Vyčištění
iptables -F

# 2. Bezpečné SSH (povolení jen správci)
iptables -A INPUT -p tcp -s 192.168.1.10 --dport 22 -j ACCEPT

# 3. Povolení webu pro všechny
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# 4. Výchozí politika
iptables -P INPUT DROP

# 5. Uložení
iptables-save > server_firewall.sh
```

- **Vysvětlení:**
  - Nejdříve jsme vyčistili stará pravidla pomocí `iptables -F`.
  - Příkazy povolující služby dáváme **před** nastavením výchozí politiky `DROP`, abychom si hned nezavřeli přístup.
  - V pravidle pro SSH jsme zkombinovali `-s` (zdrojová IP) a `--dport` (port).
