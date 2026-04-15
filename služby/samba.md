# Samba - Sdílení mezi platformami

Tento materiál popisuje konfiguraci Samby pro sdílení souborů mezi Linuxovými a Windows systémy.

### 1. Teoretický úvod (K čemu to slouží?)

Samba je implementace síťového protokolu SMB/CIFS pro Unixové systémy. Umožňuje sdílet soubory a tiskárny s klienty Microsoft Windows.

### 2. Konfigurace a syntaxe

Instalace: `sudo apt update && sudo apt install samba`

Hlavní konfigurační soubor: `/etc/samba/smb.conf`

**Základní parametry sdílení (definice v `[nazev_sdileni]`):**

- `path = /cesta/k/adresari`: Fyzické umístění sdílené složky.
- `browseable = yes`: Viditelnost složky v prohlížeči sítě.
- `read only = no`: Povolení zápisu (opakem je `writeable = yes`).
- `guest ok = yes`: Přístup bez hesla (anonymní uživatel).
- `valid users = jmeno`: Omezení přístupu pouze na vybrané uživatele.
- `create mask = 0644`: Práva pro nově vytvořené soubory.
- `directory mask = 0755`: Práva pro nově vytvořené složky.

**Důležitý příkaz pro správu uživatelů:**
`sudo smbpasswd -a uzivatel` (přidá systémového uživatele do databáze uživatelů Samby).

Po každé změně konfigurace: `sudo systemctl restart smbd`

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**Anonymní veřejné sdílení:**

```ini
[public]
   comment = Veřejné dokumenty
   path = /srv/samba/public
   browseable = yes
   guest ok = yes
   read only = yes
```

**Co to dělá:** Vytvoří sdílení viditelné pro všechny v síti pod názvem `public`, do kterého může kdokoli číst bez nutnosti zadávat jméno a heslo.

**Zabezpečené sdílení pro konkrétního uživatele:**

```ini
[soukrome]
   path = /srv/samba/soukrome
   valid users = jana
   read only = no
   browseable = no
```

**Co to dělá:** Vytvoří skryté sdílení (nebude vidět v "Místech v síti"), do kterého se může přihlásit pouze uživatel `jana` se svým Samba heslem a může do něj zapisovat.

**Kontrola konfigurace:**

```bash
testparm
```

**Co to dělá:** Nástroj, který prověří syntaxi `/etc/samba/smb.conf` a vypíše všechny chyby i výslednou konfiguraci.

### 4. Cvičení pro studenta (Procvičování)

1. **Instalace a základ:** Nainstalujte Sambu a vytvořte veřejnou složku `/srv/public`, do které může každý zapisovat (`guest ok = yes`, `read only = no`).
2. **Uživatelský přístup:** Vytvořte uživatele `student` v Linuxu a přidejte ho do Samby s heslem `Maturita2024`.
3. **Práva:** Vytvořte pro uživatele z úlohy 2 sdílení `[studovna]`. Nastavte ve sdílení `[studovna]`, aby nově vytvořené soubory měly práva `0660` (vlastník a skupina mohou číst/psát).
4. **Komplexní úloha:** Nastavte firemní souborový server:
   - Složka `/data/sklady` přístupná pouze pro uživatele `skladnik` s právem zápisu.
   - Složka `/data/marketing` přístupná pro skupinu uživatelů `marketing` (využijte parametr `valid users = @marketing`).
   - Skryté sdílení `[audit]`, do kterého má přístup pouze uživatel `root`.
   - Ověřte konfiguraci pomocí nástroje `testparm`.

### 5. Řešení cvičení

**Úloha 1:**

```bash
sudo apt install samba
sudo mkdir -p /srv/public && sudo chmod 777 /srv/public
# V /etc/samba/smb.conf:
# [public]
#   path = /srv/public
#   guest ok = yes
#   read only = no
sudo systemctl restart smbd
```

**Úloha 2:**

```bash
sudo useradd -m -s /sbin/nologin student
(echo "Maturita2024"; echo "Maturita2024") | sudo smbpasswd -a -s student
# V /etc/samba/smb.conf:
# [studovna]
#   path = /home/student/studovna
#   valid users = student
#   read only = no
```

**Úloha 3:**

```ini
[studovna]
   path = /home/student/studovna
   valid users = student
   read only = no
   create mask = 0660
   directory mask = 0770
```

**Úloha 4 (Komplexní úloha):**

1. Příprava uživatelů a skupin:
   `sudo groupadd marketing`
   `sudo useradd -m skladnik`
   `sudo smbpasswd -a skladnik`
2. Konfigurace `/etc/samba/smb.conf`:

```ini
[sklady]
   path = /data/sklady
   valid users = skladnik
   read only = no

[marketing]
   path = /data/marketing
   valid users = @marketing
   read only = no

[audit]
   path = /data/audit
   valid users = root
   browseable = no
   read only = yes
```

3. Kontrola a restart:
   `testparm -s`
   `sudo systemctl restart smbd`

**Logika řešení komplexní úlohy:**

- `@marketing`: Zavináč v `valid users` značí Linuxovou skupinu, nikoliv jednotlivého uživatele.
- `browseable = no`: Složka se neobjeví při procházení serveru v síti, uživatel musí znát její přesný název (např. `\\server\audit`).
- `testparm`: Před restartem zajistí, že server nezhavaruje kvůli syntaktické chybě (např. chybějící závorka u sekce).
