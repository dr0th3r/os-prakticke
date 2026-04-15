# Apache2 - Webový server

Tento materiál se zaměřuje na instalaci, konfiguraci virtuálních hostitelů a základní správu webového serveru Apache2.

### 1. Teoretický úvod (K čemu to slouží?)

Apache2 je jedním z nejpoužívanějších open-source HTTP serverů. Umožňuje hostovat webové stránky a aplikace (PHP, Python, atd.). Správce IT jej používá k publikování interních nebo externích webů a služeb. Hlavní výhodou Apache je jeho modularita a podpora pro virtuální hostitele (VirtualHosts), které umožňují běžet více webů na jedné IP adrese.

### 2. Konfigurace a syntaxe

Instalace: `sudo apt update && sudo apt install apache2`

Hlavní adresáře a soubory:

- `/etc/apache2/`: Hlavní konfigurační adresář.
- `/etc/apache2/sites-available/`: Konfigurační soubory jednotlivých webů.
- `/etc/apache2/sites-enabled/`: Odkazy (symlinky) na povolené weby.
- `/etc/apache2/mods-available/`: Dostupné moduly (např. php, rewrite, ssl).
- `/etc/apache2/mods-enabled/`: Odkazy na aktivované moduly.
- `/var/www/html/`: Výchozí kořenový adresář pro webové soubory.

**Klíčové příkazy pro správu:**

- `apache2ctl configtest`: Kontrola syntaktických chyb v konfiguraci.
- `a2ensite nazev_webu.conf` / `a2dissite`: Povolení / zakázání webu.
- `a2enmod nazev_modulu` / `a2dismod`: Povolení / zakázání modulu.
- `systemctl reload apache2`: Znovunačtení konfigurace (lepší než restart, nepřeruší spojení).

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**Vytvoření nového virtuálního hostitele:**

```apache
<VirtualHost *:80>
    ServerName www.moje-maturita.cz
    DocumentRoot /var/www/maturita
    ErrorLog ${APACHE_LOG_DIR}/error_maturita.log
    CustomLog ${APACHE_LOG_DIR}/access_maturita.log combined
</VirtualHost>
```

**Co to dělá:** Definuje konfiguraci pro web, který bude reagovat na doménu `www.moje-maturita.cz`. Soubory webu budou v `/var/www/maturita`. Logy chyb a přístupů budou odděleny od hlavních logů.

**Kontrola syntaxe před restartem:**

```bash
sudo apache2ctl configtest
```

**Co to dělá:** Ověří, zda v konfiguračních souborech nejsou překlepy. Pokud vypíše `Syntax OK`, můžete bezpečně restartovat server bez rizika, že nenaběhne.

**Změna výchozího indexového souboru:**

```apache
DirectoryIndex main.php index.html
```

**Co to dělá:** Určuje pořadí souborů, které Apache hledá při přístupu k adresáři. V tomto případě upřednostní `main.php` před `index.html`.

**Povolení uživatelských webů (mod_userdir):**

```bash
sudo a2enmod userdir
sudo systemctl reload apache2
```

**Co to dělá:** Umožňuje uživatelům mít vlastní web v adresáři `~/public_html`. Web je pak dostupný na adrese `http://server/~uzivatel/`.

**Přesměrování z HTTP na HTTPS (mod_rewrite):**

```apache
<VirtualHost *:80>
    ServerName www.moje-maturita.cz
    RewriteEngine on
    RewriteCond %{SERVER_PORT} ^80$
    RewriteRule ^(.*)$ https://%{SERVER_NAME}$1 [L,R]
</VirtualHost>
```

**Co to dělá:** Pokud uživatel přijde na nezabezpečený port 80, modul `rewrite` ho automaticky přesměruje na stejnou adresu, ale s protokolem HTTPS.

### 4. Cvičení pro studenta (Procvičování)

1. **Instalace a test:** Nainstalujte Apache2 a změňte výchozí text v `/var/www/html/index.html` na "Ahoj světe". Ověřte funkčnost v prohlížeči (nebo pomocí `curl localhost`).
2. **Nový web:** Vytvořte nový VirtualHost pro doménu `skola.local`, který bude mít kořenový adresář v `/var/www/skola`. Nezapomeňte adresář vytvořit a nastavit mu práva.
3. **Logování:** Nastavte pro web `skola.local` vlastní soubory logů v `/var/log/apache2/skola_access.log` a `skola_error.log`.
4. **Komplexní úloha:** Zprovozněte dva nezávislé weby na jednom serveru:
   - Web A: `prvni.cz`, kořen `/var/www/prvni`.
   - Web B: `druhy.cz`, kořen `/var/www/druhy`.
   - Oba weby musí mít vlastní logování a u obou musí být zakázán výpis obsahu adresářů (`Options -Indexes`).
   - Aktivujte modul `userdir` a vytvořte pro uživatele `student` testovací stránku v jeho domovském adresáři.
   - Aby domény fungovaly lokálně, přidejte je do `/etc/hosts`.

### 5. Řešení cvičení

**Úloha 1:**

```bash
sudo apt install apache2
echo "Ahoj světe" | sudo tee /var/www/html/index.html
curl localhost
```

**Úloha 2 & 3:**

```bash
sudo mkdir -p /var/www/skola
sudo chown -R $USER:www-data /var/www/skola
# Vytvoření /etc/apache2/sites-available/skola.conf:
# <VirtualHost *:80>
#     ServerName skola.local
#     DocumentRoot /var/www/skola
#     ErrorLog /var/log/apache2/skola_error.log
#     CustomLog /var/log/apache2/skola_access.log combined
# </VirtualHost>
sudo a2ensite skola.conf
sudo systemctl reload apache2
```

**Úloha 4 (Komplexní úloha):**

1. Příprava adresářů:
   `sudo mkdir -p /var/www/prvni /var/www/druhy`
2. Konfigurace `/etc/apache2/sites-available/prvni.conf` (podobně i pro `druhy.conf`):

```apache
<VirtualHost *:80>
    ServerName prvni.cz
    DocumentRoot /var/www/prvni
    <Directory /var/www/prvni>
        Options -Indexes
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/prvni_error.log
</VirtualHost>
```

3. Povolení webů:
   `sudo a2ensite prvni.conf druhy.conf`
4. Přidání do `/etc/hosts`:
   `echo "127.0.0.1 prvni.cz druhy.cz" | sudo tee -a /etc/hosts`
5. Restart:
   `sudo systemctl restart apache2`

6. Nastavení uživatelského webu:
   `sudo a2enmod userdir`
   `mkdir ~/public_html`
   `echo "Web studenta" > ~/public_html/index.html`
   `sudo systemctl reload apache2`

**Logika řešení komplexní úlohy:**

- `Options -Indexes`: Zabezpečení – pokud v adresáři chybí `index.html`, Apache nevypíše seznam souborů, ale vrátí chybu 403.
- `/etc/hosts`: Lokální DNS záznamy, které nasměrují doménu na IP adresu loopbacku (vlastního stroje).
- `a2ensite`: Vytváří symbolický odkaz z `sites-available` do `sites-enabled`.
- `mod_userdir`: Mapuje URL `~uzivatel` na fyzickou složku v `/home`.
