# Správa systémových služeb: systemctl

### 1. Teoretický úvod (K čemu to slouží?)
Služba (též démon) je program, který běží na pozadí a poskytuje určitou funkci (např. SSH pro vzdálený přístup nebo Apache pro webové stránky). V moderních Linuxech (Debian/Ubuntu) tyto služby spravuje systém nazvaný `systemd`. Správce systému musí umět tyto služby zapínat, vypínat a zajišťovat, aby se po restartu serveru samy spustily.

**V praxi to jako správce využiješ, když:**
- Změníš konfiguraci webového serveru a potřebuješ, aby se změny projevily (restart).
- Služba přestala fungovat a ty chceš zjistit, v čem je chyba (status).
- Potřebuješ, aby se databáze (např. MySQL) automaticky spustila hned po zapnutí počítače (enable).

---

### 2. Konfigurace a syntaxe
Ke správě všech služeb se používá jeden hlavní příkaz, který vyžaduje práva správce (`sudo`).

**Ovládání služeb (Příkaz `systemctl`):**
```bash
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2
```
- **start**: Zapne vypnutou službu.
- **stop**: Vypne běžící službu.
- **restart**: Službu vypne a okamžitě znovu zapne (nejčastější volba pro načtení změn).

**Zjišťování stavu a automatický start:**
```bash
systemctl status ssh > stav_ssh.txt
sudo systemctl enable ssh
sudo systemctl disable ssh
```
- **status**: Nejdůležitější diagnostika. Ukáže, zda služba běží (`active`) a vypíše posledních pár řádků z jejího logu.
- **enable**: Nastaví službu tak, aby se automaticky spustila při startu počítače.
- **disable**: Zruší automatické spouštění při startu.

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Kontrola všech běžících služeb:**
```bash
systemctl list-units --type=service --state=running > bezici_sluzby.txt
```
- **Co to dělá:** Vypíše přehlednou tabulku všech programů, které právě teď aktivně běží na pozadí. To ti pomůže rychle zjistit, jaké služby máš na serveru k dispozici.

**B. Zjištění, zda je služba nastavena na automatický start:**
```bash
systemctl is-enabled apache2 > kontrola_startu.txt
```
- **Co to dělá:** Příkaz vypíše buď `enabled` (spustí se sama), nebo `disabled` (musíš ji zapnout ručně). Výsledek se uloží do souboru.

**C. Rychlé ověření, zda služba funguje:**
```bash
systemctl is-active ssh > aktivita_ssh.txt
```
- **Co to dělá:** Místo dlouhého výpisu `status` vypíše jen jedno slovo: `active` nebo `inactive`. Je to skvělé pro použití v automatických skriptech.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Průzkum stavu**
Zjisti aktuální stav služby `sshd`. Výsledek ulož do souboru `ssh_stav.txt`. Zjisti z něj, jak dlouho služba běží (hledej řádek `Active: active (running) since ...`).

**Úloha 2: Změna automatického startu**
Napiš příkaz, který zajistí, aby se webový server `apache2` automaticky **nespouštěl** při startu počítače. Ověř výsledek příkazem `is-enabled` a ulož ho do `apache_start_info.txt`.

**Úloha 3: Restart a kontrola**
Restartuj službu `sshd` a hned poté ověř, zda je ve stavu `active`. Tento stav (pouze jedno slovo) ulož do souboru `ssh_check.txt`.

**Komplexní úloha:**
Maturitní scénář: "Údržba serveru".
1. Zastav službu `apache2`. Do souboru `servis.log` zapiš zprávu: "Probíhá údržba, web je vypnut".
2. Nastav službu `apache2` tak, aby se po příštím restartu PC automaticky spustila.
3. Pomocí příkazu `systemctl` vypiš seznam všech služeb, které v systému selhaly (použij `--state=failed`). Výsledek ulož do `chyby_sluzeb.txt`.
4. Nakonec službu `apache2` znovu zapni a do `servis.log` přidej informaci o aktuálním čase spuštění.

---

### 5. Řešení cvičení

**Řešení 1:**
```bash
systemctl status sshd > ssh_stav.txt
```
- **Vysvětlení:** 
    - `status` vypíše i procesy, které pod službou běží, a případné chybové hlášky z poslední doby.

**Řešení 2:**
```bash
sudo systemctl disable apache2
systemctl is-enabled apache2 > apache_start_info.txt
```
- **Vysvětlení:** 
    - `disable` neodstraní službu, pouze smaže symbolický odkaz, který ji spouštěl při bootu.

**Řešení 3:**
```bash
sudo systemctl restart sshd
systemctl is-active sshd > ssh_check.txt
```
- **Vysvětlení:** 
    - `is-active` vrací čistý textový výstup, který se snadno ukládá a čte.

**Řešení komplexní úlohy:**
```bash
# 1. Zastavení a log
sudo systemctl stop apache2
echo "Probíhá údržba, web je vypnut" > servis.log

# 2. Enable
sudo systemctl enable apache2

# 3. Hledání chyb
systemctl list-units --type=service --state=failed > chyby_sluzeb.txt

# 4. Spuštění a finální log
sudo systemctl start apache2
echo "Web opět spuštěn dne: $(date)" >> servis.log
```
- **Vysvětlení:** 
    - `--state=failed` je velmi užitečný přepínač pro administrátory, protože okamžitě ukáže, co v systému "zheblo".
    - `sudo` je u startu/stopu nezbytné, protože tyto akce ovlivňují celý systém a všechny uživatele.
    - `>>` u posledního řádku zajistí, že log o spuštění bude pod zprávou o vypnutí.
