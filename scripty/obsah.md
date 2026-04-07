# Obsah tématu: Bash skriptování

Tato sekce se věnuje automatizaci úloh v Linuxu pomocí skriptů. Každý bod má svůj vlastní soubor s hloubkovým rozborem.

## 1. Základy skriptování (01_zaklady.md)
- Co je to Shebang (`#!/bin/bash`).
- Jak skript vytvořit a dát mu práva (`chmod +x`).
- Proměnné a komentáře.

## 2. Vstup a výstup (02_vstup_vystup.md)
- Čtení uživatelského vstupu (`read`).
- Poziční parametry (`$1`, `$2` – argumenty při spouštění).
- Speciální proměnné (`$0`, `$#`, `$@`).

## 3. Podmínky a rozhodování (03_podminky.md)
- Konstrukce `if`, `then`, `else`, `fi`.
- Porovnávání čísel a textu.
- Testování existence souborů a složek (`-f`, `-d`).
- Kontrola úspěchu předchozího příkazu (`$?`).

## 4. Cykly (04_cykly.md)
- Cyklus `for` (procházení seznamu, souborů v adresáři).
- Cyklus `while` (provádění akce, dokud platí podmínka).
- Práce s příkazem `seq`.

## 5. Komplexní maturitní příklady (05_priklady.md)
- Skript na automatické zálohování (tar).
- Skript na hromadné zakládání uživatelů ze seznamu.
- Skript na monitoring místa na disku a sítě.
