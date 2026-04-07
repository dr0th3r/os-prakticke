# Příprava k praktické maturitě: Operační systémy a sítě

Tento dokument slouží jako kompletní přehled konfigurací, příkazů a teoretických základů pro praktickou zkoušku.

---

## 1. BASH A TEXTOVÉ NÁSTROJE (AUTOMATIZACE)

### Základní nástroje pro zpracování textu

- **grep**: Vyhledávání v textu/souborech.
  - `grep "error" /var/log/syslog` (hledání řetězce)
  - `grep -i` (ignorovat velikost písmen), `-v` (invertovaný výběr - vše kromě), `-E` (rozšířené regulární výrazy), `-r` (rekurzivně v adresáři).
- **awk**: Sloupcový procesor.
  - `awk '{print $1}' soubor` (vypíše první sloupec)
  - `awk -F ":" '{print $1}' /etc/passwd` (změna oddělovače na dvojtečku)
  - `awk '/pattern/ {print $2}'` (pokud řádek obsahuje pattern, vypiš 2. sloupec)
- **sed**: Stream editor (nahrazování).
  - `sed 's/old/new/g' soubor` (globální nahrazení)
  - `sed -i 's/old/new/g' soubor` (editace přímo v souboru)
- **tr**: Transformace znaků.
  - `echo "hello" | tr 'a-z' 'A-Z'` (převod na velká písmena)
  - `tr -d ' '` (smazání všech mezer)
- **cut**: Výřez textu.
  - `cut -d':' -f1 /etc/passwd` (podobné jako awk, jednodušší)

### Bash Skriptování - struktury

- **Proměnné**: `NAME="Server"`, volání `${NAME}`.
- **Podmínky**:
  ```bash
  if [ -f /etc/passwd ]; then
      echo "Soubor existuje"
  fi
  ```
- **Cykly (procházení souboru po řádcích)**:
  ```bash
  while read LINE; do
      echo "Zpracovávám: $LINE"
  done < soubor.txt
  ```

---

## 2. MONITORING SÍTĚ A IPTABLES (LINUX)

### Sledování provozu

- **Počet odeslaných paketů na eth0**:
  - `ip -s link show eth0` (hledat sekci TX: packets)
  - `cat /proc/net/dev` (systémový soubor s čítači)
  - Příkaz pro script: `ip -s link show eth0 | grep "TX:" -A 1 | tail -n 1 | awk '{print $1}'`
- **Sledování portu 80 přes iptables**:
  1. Přidání pravidla: `iptables -A INPUT -p tcp --dport 80 -j ACCEPT` (pokud jen počítáme, stačí mít pravidlo v řetězci)
  2. Výpis: `iptables -L -v -n` (sloupec `pkts` a `bytes`)
  3. Nulování: `iptables -Z` (vynuluje čítače)

### Diagnostické příkazy

- `ss -tulpen` nebo `netstat -antp` (seznam otevřených portů a aplikací)
- `tcpdump -i eth0 port 80` (odposlech reálného provozu na portu 80)
- `mtr google.com` (kombinace ping a traceroute)

---

## 3. SPRÁVA UŽIVATELŮ A PRÁV

### Hromadné vytváření uživatelů

Scénář: Máš soubor `uzivatele.txt` ve formátu `jmeno:heslo`.

```bash
#!/bin/bash
while IFS=":" read -r USER PASS; do
    useradd -m -s /bin/bash "$USER"
    echo "$USER:$PASS" | chpasswd
    echo "Uživatel $USER byl vytvořen."
done < uzivatele.txt
```

### Práva a vlastníci

- `chmod 755 soubor` (rwxr-xr-x)
- `chown user:group soubor` (změna vlastníka a skupiny)
- **Speciální práva**:
  - `chmod +s /usr/bin/prikaz` (SUID - spustí se pod vlastníkem souboru, obvykle root)
  - `chmod +t /tmp` (Sticky bit - soubor může smazat jen vlastník)

---

## 4. ZÁLOHOVÁNÍ (TAR)

- **Plná záloha**: `tar -cvzf zaloha.tar.gz /var/www/html`
  - `-c` (create), `-v` (verbose), `-z` (gzip), `-f` (file)
- **Inkrementální záloha**:
  ```bash
  # První spuštění vytvoří snapshot a plnou zálohu
  tar -cvzf zaloha_1.tar.gz -g metadata.snar /data
  # Druhé spuštění uloží pouze změny od minule
  tar -cvzf zaloha_2.tar.gz -g metadata.snar /data
  ```
- **Rozbalení**: `tar -xvzf zaloha.tar.gz -C /cilovy/adresar`

---

## 5. SPRÁVA SLUŽEB (SYSTEMD)

- `systemctl start|stop|restart|status|reload [sluzba]`
- `systemctl enable|disable [sluzba]` (spuštění po startu)
- `systemctl mask [sluzba]` (úplné zablokování služby)

### Konkrétní služby - konfigurace:

#### SSH (OpenSSH) - `/etc/ssh/sshd_config`

- `Port 2222` (změna portu)
- `PermitRootLogin no` (zákaz roota)
- `PasswordAuthentication no` (pouze klíče)

#### Web (Apache2) - `/etc/apache2/`

- `apache2ctl configtest` (kontrola syntaxe)
- `a2ensite / a2dissite` (povolení/zakázání webu)
- VirtualHost v `/etc/apache2/sites-available/000-default.conf`:
  ```apache
  <VirtualHost *:80>
      DocumentRoot /var/www/mujweb
      ServerName www.priklad.cz
  </VirtualHost>
  ```

#### Sdílení souborů (NFS a Samba)

- **NFS** (`/etc/exports`): `/home/export 192.168.1.0/24(rw,sync,no_subtree_check)`
- **Samba** (`/etc/samba/smb.conf`):
  ```ini
  [sdileni]
  path = /srv/samba
  read only = no
  guest ok = yes
  ```

  - Příkaz `smbpasswd -a uzivatel` pro přidání uživatele do Samby.

#### Databáze (MySQL/MariaDB)

- `mysql -u root -p` (přihlášení)
- `CREATE DATABASE test;`, `GRANT ALL PRIVILEGES ON test.* TO 'user'@'localhost' IDENTIFIED BY 'heslo';`
- **Záloha**: `mysqldump -u root -p --all-databases > full_backup.sql`
- **Obnova**: `mysql -u root -p < backup.sql`

---

## 6. CRON (PLÁNOVÁNÍ ÚLOH)

- `crontab -e` (editace pro aktuálního uživatele)
- Formát: `min hod den_měs měs den_týd příkaz`
  - `0 4 * * * /backup.sh` (každý den ve 4:00)
  - `*/15 * * * *` (každých 15 minut)
- **Kontrola logů**: `grep CRON /var/log/syslog`

---

## 7. DIAGNOSTIKA SYSTÉMU (PŘÍKAZY)

- **Místo na disku**: `df -h` (oddíly), `du -sh *` (velikost složek v aktuálním místě)
- **Paměť**: `free -m`, `vmstat`
- **Procesy**: `top` (základ), `htop` (interaktivní), `ps aux` (výpis všech)
- **Zabití procesu**: `kill -9 [PID]`, `pkill [název]`, `xkill` (pro GUI)
- **Hardware**: `lsusb`, `lspci`, `lscpu`, `lsblk`

---

## 8. SÍŤOVÉ NASTAVENÍ (LINUX CLI)

- **Dočasné nastavení**: `ip addr add 192.168.1.10/24 dev eth0`
- **Trvalé (Debian/Ubuntu starší)**: `/etc/network/interfaces`
  ```text
  auto eth0
  iface eth0 inet static
      address 192.168.1.10
      netmask 255.255.255.0
      gateway 192.168.1.1
  ```
- **DNS**: `/etc/resolv.conf` (`nameserver 8.8.8.8`)
- **Routing table**: `ip route show`

---

## 9. CISCO PACKET TRACER (KONFIGURACE)

### Základní management

- `enable` -> `configure terminal`
- `hostname R1`
- `enable secret class` (zašifrované heslo pro priv. režim)

### Switching & VLANs

- **Vytvoření**: `vlan 10`, `name Management`
- **Access port**:
  ```text
  int fa0/1
  switchport mode access
  switchport access vlan 10
  ```
- **Trunk port**:
  ```text
  int gi0/1
  switchport mode trunk
  ```
- **LACP (EtherChannel)**:
  ```text
  int range fa0/1 - 2
  channel-group 1 mode active
  ```

### Routing

- **Statický**: `ip route 10.0.0.0 255.255.255.0 192.168.1.1`
- **Default route**: `ip route 0.0.0.0 0.0.0.0 Serial0/0/0`
- **Router on a stick (Inter-VLAN)**:
  ```text
  int gi0/0.10
  encapsulation dot1Q 10
  ip address 10.0.10.1 255.255.255.0
  ```

### NAT (Network Address Translation)

- **Statický (Port Forwarding)**: `ip nat inside source static tcp 192.168.1.10 80 80.80.80.80 80`
- **Dynamický s přetížením (PAT)**:
  ```text
  access-list 1 permit 192.168.1.0 0.0.0.255
  ip nat inside source list 1 interface Serial0/0/0 overload
  int gi0/0 (inside), int s0/0/0 (outside) -> ip nat inside/outside
  ```

---

## 10. FIREWALLING A BEZPEČNOST

### Iptables logování a tagování

- **Logování zahozených paketů**:
  `iptables -A INPUT -j LOG --log-prefix "FIREWALL-DROP: " --log-level 4`
- **Omezení SSH na konkrétní IP**:
  `iptables -A INPUT -p tcp -s 10.0.0.5 --dport 22 -j ACCEPT`
  `iptables -A INPUT -p tcp --dport 22 -j DROP`
- **Tagování (Mangle)** - pro pokročilé směrování:
  `iptables -t mangle -A PREROUTING -p icmp -j MARK --set-mark 1`

### Port Security (Cisco)

```text
int fa0/1
switchport port-security
switchport port-security maximum 2
switchport port-security violation shutdown
switchport port-security mac-address sticky
```

---

## 11. DALŠÍ ČASTÉ ÚLOHY

- **Změna hostname**: `hostnamectl set-hostname nove-jmeno`
- **Nastavení času**: `timedatectl set-ntp true`
- **Zjištění verze OS**: `cat /etc/os-release`
- **Hledání velkých souborů**: `find / -type f -size +100M`
- **Kontrola integrity balíčků**: `dpkg --verify` nebo `rpm -V`
