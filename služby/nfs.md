# NFS (Network File System) - Síťové sdílení

Tento materiál se věnuje sdílení souborů mezi Linuxovými stroji pomocí protokolu NFS.

### 1. Teoretický úvod (K čemu to slouží?)

NFS je standardní protokol v Linuxu a Unixových systémech pro transparentní přístup k souborům přes síť. Umožňuje připojit (mountnout) vzdálený adresář tak, jako by byl lokální.

### 2. Konfigurace a syntaxe

Instalace na server: `sudo apt install nfs-kernel-server`
Instalace na klient: `sudo apt install nfs-common`

**Klíčový konfigurační soubor serveru:** `/etc/exports`

**Syntaxe zápisu v `/etc/exports`:**
`/cesta/k/adresari  klient(volby)`

**Běžné volby (parametry):**

- `rw`: Klient může číst i zapisovat (read-write).
- `ro`: Klient může pouze číst (read-only).
- `sync`: Data jsou zapsána na disk před potvrzením operace (bezpečnější, pomalejší).
- `no_root_squash`: Povoluje vzdálenému uživateli `root` vystupovat na serveru také jako `root` (vysoké bezpečnostní riziko!).
- `root_squash`: Převede vzdáleného roota na uživatele `nobody` (výchozí bezpečnější chování).
- `no_subtree_check`: Zvyšuje výkon tím, že nekontroluje, zda se soubor nachází v přesně exportované podstromové struktuře.

Po změně souboru `/etc/exports` je nutný export: `sudo exportfs -ra`

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**Sdílení adresáře pro celou síť:**

```text
/srv/dokumenty  192.168.1.0/24(rw,sync,no_subtree_check)
```

**Co to dělá:** Exportuje adresář `/srv/dokumenty` všem strojům v síti `192.168.1.0/24` s právy pro čtení i zápis a synchronním režimem zápisu.

**Připojení sdíleného adresáře na klientovi:**

```bash
sudo mount 192.168.1.50:/srv/dokumenty /mnt/vzdaleny_disk
```

**Co to dělá:** Připojí (mountne) exportovaný adresář ze serveru s IP `192.168.1.50` do lokálního adresáře `/mnt/vzdaleny_disk`.

**Trvalé připojení přes /etc/fstab:**

```text
192.168.1.50:/srv/dokumenty  /mnt/vzdaleny_disk  nfs  defaults  0  0
```

**Co to dělá:** Zajišťuje automatické připojení vzdáleného adresáře při každém startu systému klienta.

### 4. Cvičení pro studenta (Procvičování)

1. **Instalace a export:** Nainstalujte NFS server a vyexportujte adresář `/var/nfs_public` pro všechny s právem pouze pro čtení (ro).
2. **Práva:** Změňte konfiguraci tak, aby váš konkrétní počítač (podle IP) mohl do stejného adresáře i zapisovat.
3. **Mountování:** Na druhém stroji (nebo v jiném terminálu) připojte tento adresář do `/home/uzivatel/sit`. Ověřte, že tam vidíte soubory ze serveru.
4. **Komplexní úloha:** Nastavte centrální server pro zálohy:
   - Na serveru vytvořte adresář `/srv/backups` a vyexportujte ho pouze pro IP `10.0.0.10` s právy `rw` a `no_root_squash`.
   - Na klientovi (`10.0.0.10`) nastavte automatické připojení tohoto disku do `/backup` přes `/etc/fstab`.
   - Vyzkoušejte, že uživatel root na klientovi může v tomto adresáři vytvářet soubory s vlastníkem root (díky `no_root_squash`).

### 5. Řešení cvičení

**Úloha 1:**

```bash
sudo mkdir -p /var/nfs_public
echo "/var/nfs_public *(ro,sync,no_subtree_check)" | sudo tee -a /etc/exports
sudo exportfs -ra
```

**Úloha 2:**

```bash
# V /etc/exports:
/var/nfs_public 192.168.1.10(rw,sync,no_subtree_check) *(ro,sync,no_subtree_check)
sudo exportfs -ra
```

**Úloha 3:**

```bash
mkdir -p ~/sit
sudo mount server_ip:/var/nfs_public ~/sit
df -h | grep nfs
```

**Úloha 4 (Komplexní úloha):**

1. Na serveru:
   `sudo mkdir -p /srv/backups`
   `echo "/srv/backups 10.0.0.10(rw,sync,no_root_squash)" | sudo tee -a /etc/exports`
   `sudo exportfs -ra`
2. Na klientovi:
   `sudo mkdir /backup`
   `echo "10.0.0.100:/srv/backups /backup nfs defaults 0 0" | sudo tee -a /etc/fstab`
   `sudo mount -a`
3. Test:
   `sudo touch /backup/test_file && ls -l /backup/test_file` (Měl by patřit rootovi).

**Logika řešení komplexní úlohy:**

- `no_root_squash`: Klíčový parametr pro zálohovací servery, kde je nutné zachovat vlastnictví souborů (root musí zůstat rootem i po síti).
- `exportfs -ra`: Načte znovu konfiguraci exportů bez nutnosti restartovat celou službu `nfs-kernel-server`.
- `mount -a`: Zkontroluje `/etc/fstab` a připojí všechny záznamy, které ještě nejsou připojené.
