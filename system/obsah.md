# Obsah tématu: Správa a diagnostika systému

Tato sekce se věnuje praktickému sledování a ovládání Linuxového serveru. Každý bod má svůj samostatný soubor.

## 1. Disková úložiště (01_disky.md)
- Zobrazení struktury disků a oddílů (`lsblk`).
- Podrobné informace o tabulce oddílů (`fdisk -l`).
- Unikátní identifikace disků pomocí UUID (`blkid`).
- Sledování zaplnění disků a velikosti složek (`df`, `du`).

## 2. Paměť a procesy (02_procesy.md)
- Sledování operační paměti RAM a swapu (`free`).
- Výpis a vyhledávání běžících programů (`ps aux`).
- Interaktivní sledování zátěže systému (`top`).
- Bezpečné a vynucené ukončování procesů (`kill`, `pkill`).

## 3. Správa systémových služeb (03_sluzby.md)
- Ovládání služeb pomocí `systemctl` (start, stop, restart).
- Zjišťování stavu služby a řešení problémů (`status`).
- Nastavení automatického spouštění po startu (`enable`, `disable`).

## 4. Systémové a aplikační logy (04_logy.md)
- Práce s moderním logovacím systémem (`journalctl`).
- Prohlížení textových logů v `/var/log/` (syslog, auth.log).
- Specifické logy důležitých služeb:
  - **OpenSSH** (přihlášení a útoky).
  - **Apache** (webový provoz a chyby).
  - **Samba a NFS** (sdílení souborů).
  - **MySQL** (databázové operace).

## 5. Plánování úloh (05_cron.md)
- Automatizace úloh pomocí démona `cron`.
- Správa uživatelských tabulek (`crontab -e`, `-l`).
- Formát časového zápisu (minuta, hodina, den, měsíc, den v týdnu).
- Směrování výstupů a chyb z plánovaných úloh.
