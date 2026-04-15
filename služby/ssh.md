# SSH (OpenSSH) - Vzdálená správa

Tento dokument pokrývá instalaci, zabezpečení a konfiguraci SSH serveru pro bezpečnou vzdálenou správu Linuxových systémů.

### 1. Teoretický úvod (K čemu to slouží?)

SSH (Secure Shell) je kryptografický síťový protokol pro bezpečnou komunikaci v nezabezpečené síti. Nahrazuje starší nezabezpečené protokoly jako Telnet nebo Rlogin.

### 2. Konfigurace a syntaxe

Instalace balíčku: `sudo apt update && sudo apt install openssh-server`

Hlavní konfigurační soubor: `/etc/ssh/sshd_config`

**Klíčové parametry v `sshd_config`:**

- `Port 22`: Definuje port, na kterém server naslouchá (změna portu snižuje počet útoků hrubou silou).
- `PermitRootLogin no`: Zakazuje přímé přihlášení uživatele `root` (vždy se přihlašuje jako běžný uživatel a pak použije `sudo`).
- `PasswordAuthentication no`: Zakazuje přihlašování heslem a vyžaduje SSH klíče (zásadní pro bezpečnost).
- `AllowUsers jmeno`: Omezuje přístup pouze na konkrétní uživatele.
- `PubkeyAuthentication yes`: Povoluje autentizaci pomocí veřejných klíčů.

Po každé změně je nutný restart: `sudo systemctl restart ssh`

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**Přihlášení na server s jiným portem:**

```bash
ssh -p 2222 uzivatel@192.168.1.10
```

**Co to dělá:** Připojí se k serveru na IP adrese `192.168.1.10` přes port `2222`. Pokud není port specifikován, použije se výchozí 22.

**Generování SSH klíčů na klientovi:**

```bash
ssh-keygen -t ed25519
```

**Co to dělá:** Vygeneruje pár klíčů (veřejný a soukromý) pomocí moderního a bezpečného algoritmu Ed25519.

**Zkopírování veřejného klíče na server:**

```bash
ssh-copy-id -p 2222 uzivatel@192.168.1.10
```

**Co to dělá:** Automaticky přidá váš veřejný klíč do souboru `~/.ssh/authorized_keys` na vzdáleném serveru, což umožní přihlášení bez hesla.

### 4. Cvičení pro studenta (Procvičování)

1. **Základní zabezpečení:** Nainstalujte SSH server a změňte port na `4444`. Ověřte, že se na server stále dokážete připojit.
2. **Omezení přístupu:** Upravte konfiguraci tak, aby se uživatel `root` nemohl přihlásit přímo.
3. **Klíčová autentizace:** Vygenerujte si na svém stroji klíče a přeneste je na server. Poté v konfiguraci úplně zakažte autentizaci heslem.
4. **Komplexní úloha:** Nastavte SSH tak, aby:
   - Naslouchalo na portu `2222`.
   - Povolovalo přístup pouze uživateli `admin_os`.
   - Zakazovalo přihlášení roota a heslem.
   - Po připojení vypsalo "Vítejte v zabezpečeném systému" (využijte parametr `Banner`).

### 5. Řešení cvičení

**Úloha 1:**

```bash
sudo apt install openssh-server
sudo sed -i 's/#Port 22/Port 4444/' /etc/ssh/sshd_config
sudo systemctl restart ssh
```

**Úloha 2:**

```bash
sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
# Pokud je řádek zakomentovaný:
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart ssh
```

**Úloha 3:**

```bash
# Na klientovi:
ssh-keygen -t ed25519
ssh-copy-id -p 4444 uzivatel@server_ip
# Na serveru v /etc/ssh/sshd_config:
PasswordAuthentication no
sudo systemctl restart ssh
```

**Úloha 4 (Komplexní úloha):**

1. Vytvoříme soubor s bannerem: `echo "Vítejte v zabezpečeném systému" | sudo tee /etc/ssh/welcome_banner`
2. Upravíme `/etc/ssh/sshd_config`:

```text
Port 2222
PermitRootLogin no
PasswordAuthentication no
AllowUsers admin_os
Banner /etc/ssh/welcome_banner
```

3. Restartujeme službu: `sudo systemctl restart ssh`

**Logika řešení komplexní úlohy:**

- `Port 2222`: Změna portu pro ztížení automatizovaných útoků.
- `AllowUsers`: Bílý seznam (whitelist) uživatelů – nikdo jiný se nepřipojí.
- `Banner`: Cesta k souboru, jehož obsah se zobrazí uživateli před přihlášením.
- `systemctl restart`: Aplikace změn v běžícím procesu démona `sshd`.
