# Příkazy head a tail

### 1. Teoretický úvod (K čemu to slouží?)

Příkazy `head` (Hlava) a `tail` (Ocas) slouží k rychlému výpisu začátku nebo konce souboru. V Linuxu se téměř nikdy nečtou obrovské logovací soubory celé (to by terminál zahltilo), ale nahlédne se jen na jejich nejnovější záznamy.

**V praxi to jako správce využiješ, když:**

- Chceš se podívat na prvních pár řádků v souboru `/etc/passwd`.
- Potřebuješ zjistit, co se v systému stalo před minutou (poslední řádky v logu).
- Chceš sledovat, jak se do logu připisují nové řádky v reálném čase.

---

### 2. Konfigurace a syntaxe

`head` a `tail` nevyžadují žádnou konfiguraci.

**Základní syntaxe se zápisem do souboru:**

```bash
head -n 10 /etc/passwd > zacatek_souboru.txt
tail -n 20 /var/log/syslog > konec_souboru.txt
```

**Vysvětlení přepínačů:**

- **head**: Vypíše začátek souboru (standardně 10 řádků).
- **tail**: Vypíše konec souboru (standardně 10 řádků).
- **-n [číslo]**: (Number) Určuje přesný počet řádků, které se mají vypsat.
- **-f**: (Follow) Unikátní přepínač u `tail`. Nechá soubor otevřený a vypisuje nové řádky tak, jak se do něj připisují. (Velmi důležité u monitoringu).

---

### 3. Nejčastější použití (Praktické ukázky přepínačů)

**A. Zobrazení prvních pěti řádků konfiguračního souboru:**

```bash
head -n 5 /etc/passwd > uzivatele_zacatek.txt
```

- **Co to dělá:** Vypíše pouze prvních 5 řádků souboru. Výsledek uloží do souboru.

**B. Zobrazení posledních deseti řádků systémového logu:**

```bash
tail -n 10 /var/log/syslog > posledni_logy.txt
```

- **Co to dělá:** Vypíše posledních deset řádků z hlavního systémového logu. Výsledek uloží do souboru.

**C. Sledování přihlášení uživatelů v reálném čase:**

```bash
tail -f /var/log/auth.log > prubeh_auth.txt
```

- **Co to dělá:** Příkaz zůstane běžet a jakmile se někdo zkusí přihlásit, řádek o tom se hned zapíše do souboru `prubeh_auth.txt`.

---

### 4. Cvičení pro studenta (Procvičování)

**Úloha 1: Prvních deset**
Vypiš prvních 10 řádků ze souboru `/etc/services`. Výsledek ulož do souboru `sluzby_zacatek.txt`.

**Úloha 2: Poslední pětice**
Z logu `/var/log/syslog` vytáhni posledních 5 řádků a ulož je do souboru `posledni_chyby.txt`.

---

### 5. Řešení cvičení

**Řešení 1:**

```bash
head -n 10 /etc/services > sluzby_zacatek.txt
```

- **Vysvětlení:**
  - Příkaz `head` s parametrem `-n 10` vypisuje právě deset řádků ze začátku souboru.

**Řešení 2:**

```bash
tail -n 5 /var/log/syslog > posledni_chyby.txt
```

- **Vysvětlení:**
  - Příkaz `tail` vypisuje konec souboru (nejnovější záznamy).
