# Obsah tématu: Networking a Firewall (Linux)

Tato sekce se zaměřuje na praktické základy správy sítě a bezpečnosti v systému Linux (Debian/Ubuntu). Každý bod má svůj samostatný soubor.

## 1. Zjišťování síťových informací (01_info_sit.md)
- Zobrazení IP adres a stavu rozhraní (`ip addr`).
- Vyhledávání portů a běžících služeb (`ss -tunlp`).
- Zobrazení směrovací tabulky a DNS (`ip route`, `/etc/resolv.conf`).
- Diagnostické nástroje (`ping`, `nslookup`).

## 2. Základy Firewallu - iptables (02_iptables_zaklady.md)
- Tabulky a řetězce (INPUT, OUTPUT).
- Akce (ACCEPT, DROP, REJECT).
- Povolení/zakázání portů pro konkrétní služby (SSH/22, HTTP/80).
- Práce se zdrojovými a cílovými IP adresami (`-s`, `-d`).

## 3. Pokročilá správa - Logování a Statistiky (03_iptables_pokrocile.md)
- Sledování statistik paketů a bajtů na portu (`iptables -L -v`).
- Logování vybraného provozu (`-j LOG`).
- Omezování rychlosti (modul `limit`).
- Tagování (značkování) paketů pomocí `MARK`.

## 4. Komplexní maturitní úloha (04_komplexni_uloha.md)
- Kompletní zabezpečení Linuxového serveru:
  - Povolení nezbytných služeb a zakázání zbytku.
  - Logování pokusů o nepovolené SSH připojení.
  - Sledování počtu paketů odeslaných na web (port 80).
  - Omezení ICMP provozu (ping).
