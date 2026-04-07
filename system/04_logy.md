# Systémové a aplikační logy

### 1. Teoretický úvod (K čemu to slouží?)

Logování je systém zaznamenávání událostí. Každá akce, chyba nebo pokus o přihlášení zanechá záznam v "deníku" (logu). Bez logů by byl správce systému slepý – nemohl by zjistit, proč služba spadla, ani kdo se pokoušel server napadnout.

**V praxi to jako správce využiješ, když:**

- Služba se nespustila a ty chceš vědět proč (hledání chybové hlášky).
- Máš podezření na útok a chceš najít IP adresy, které se neúspěšně hlásily.
- Potřebuješ sledovat provoz na webovém serveru nebo přístupy ke sdíleným souborům.

---

### 2. Konfigurace a syntaxe

Linux používá dva hlavní způsoby logování: moderní binární deník `journald` a klasické textové soubory v `/var/log/`.

**Práce s moderními logy (Příkaz `journalctl`):**

```bash
journalctl -n 20 > posledni_zaznamy.txt
journalctl -u ssh > logy_ssh.txt
```

- **journalctl**: Nástroj pro čtení všech logů spravovaných systémem `systemd`.
  - `-n [číslo]`: Vypíše zadaný počet posledních řádků.
  - `-u [služba]`: (Unit) Zobrazí logy pouze pro konkrétní službu.
  - `-p [úroveň]`: (Priority) Zobrazí jen záznamy určité důležitosti (např. `err` pro chyby).
  - `--since [čas]`: (Since) Zobrazí záznamy od určitého času (např. `"1 hour ago"`, `"yesterday"` nebo `"2024-04-05 10:00:00"`).
  - `--until [čas]`: (Until) Zobrazí záznamy do určitého času.
  - `-f`: (Follow) Sleduje nové záznamy v reálném čase tak, jak přicházejí do systému.

**Práce s textovými logy (Adresář `/var/log/`):**
V tomto adresáři najdeš soubory, které můžeš číst běžnými nástroji jako `cat`, `grep` nebo `tail`.

- **/var/log/syslog**: Hlavní systémový log (všechno dění v systému).
- **/var/log/auth.log**: Záznamy o přihlašování a bezpečnosti.

---

### 3. Logy důležitých služeb (Praktické ukázky)

**A. OpenSSH (Zabezpečený přístup):**
Vše o přihlášení je v `/var/log/auth.log`.

```bash
grep "Failed password" /var/log/auth.log > pokusy_o_utok.txt
```

- **Co tam najdeš:** IP adresy útočníků, časy neúspěšných pokusů a jména uživatelů, pod kterými se někdo zkoušel přihlásit.

**B. Webový server Apache:**
Logy jsou v adresáři `/var/log/apache2/`.

```bash
tail -n 10 /var/log/apache2/access.log > posledni_navstevy.txt
```

- **Co tam najdeš:** `access.log` (seznam všech přístupů na web) a `error.log` (chyby skriptů nebo chybějící soubory).

**C. Sdílení souborů (Samba a NFS):**
Samba má logy v `/var/log/samba/`, NFS zapisuje většinou do `syslog`.

```bash
grep "smbd" /var/log/syslog > samba_provozu.txt
```

- **Co tam najdeš:** Informace o připojených klientech a chybách při sdílení disků.

**D. Databáze MySQL / MariaDB:**
Moderní verze zapisují přímo do journalu.

```bash
journalctl -u mysql -p err > chyby_databaze.txt
```

- **Co tam najdeš:** Problémy se startem databáze, poškozené tabulky nebo chyby v přístupových právech.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Práce s journalctl**
Vypiš posledních 15 řádků z logu služby `sshd` a výsledek ulož do souboru `ssh_posledni.txt`.

**Úloha 2: Vyhledávání chyb**
Prohledej hlavní systémový log `/var/log/syslog` a najdi v něm všechna slova "error" bez ohledu na velikost písmen (použij `grep -i`). Výsledek ulož do `chyby_systemu.txt`.

**Úloha 3: Analýza přihlášení**
Z logu `/var/log/auth.log` vytáhni všechny řádky, které obsahují slovo "Accepted" (úspěšná přihlášení). Výsledek ulož do `uspesna_prihlaseni.txt`.

**Komplexní úloha:**
Maturitní scénář: "Denní audit logů".

1. Do souboru `denni_report.txt` zapiš aktuální datum a čas.
2. Přidej k němu posledních 5 chybových hlášek z webového serveru Apache (hledej v `error.log`).
3. Zjisti počet neúspěšných pokusů o heslo v logu `auth.log` a výsledek (pouze číslo) přidej do reportu.
4. Pomocí `journalctl` zjisti, zda služba `mysql` za poslední den vypsala nějaké chyby (priorita `err`), a tyto řádky přidej na konec reportu.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
journalctl -u sshd -n 15 > ssh_posledni.txt
```

- **Vysvětlení:**
  - `-u sshd` vybere jen záznamy o SSH, `-n 15` zajistí, že uvidíme jen ty nejnovější události.

**Řešení 2:**

```bash
grep -i "error" /var/log/syslog > chyby_systemu.txt
```

- **Vysvětlení:**
  - `grep -i` ignoruje, zda je v logu napsáno "Error", "ERROR" nebo "error".

**Řešení 3:**

```bash
grep "Accepted" /var/log/auth.log > uspesna_prihlaseni.txt
```

- **Vysvětlení:**
  - Každý řádek se slovem "Accepted" představuje uživatele, který zadal správné heslo a přihlásil se.

**Řešení komplexní úlohy:**

```bash
# 1. Čas
echo "--- Denní audit: $(date) ---" > denni_report.txt

# 2. Apache chyby
echo "--- Poslední chyby Apache ---" >> denni_report.txt
tail -n 5 /var/log/apache2/error.log >> denni_report.txt

# 3. Počet útoků na SSH
echo -n "Počet neúspěšných SSH přihlášení: " >> denni_report.txt
grep "Failed password" /var/log/auth.log | wc -l >> denni_report.txt

# 4. MySQL chyby
echo "--- Chyby databáze MySQL (z journalu) ---" >> denni_report.txt
journalctl -u mysql -p err --since "1 day ago" >> denni_report.txt
```

- **Vysvětlení:**
  - `wc -l` spočítá řádky, což nám dá přesný počet pokusů o útok.
  - `--since "1 day ago"` u příkazu `journalctl` je velmi silný nástroj pro omezení výpisu na konkrétní časové období.
  - `echo -n` vypíše text bez konce řádku, takže číslo z `wc -l` se objeví hned za ním na stejném řádku.
