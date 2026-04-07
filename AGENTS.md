# AGENTS - Projektová příručka

Tento soubor definuje obecné konvence a filozofii projektu pro přípravu na maturitní zkoušku z OS a sítí. Každý agent pracující na tomto projektu musí tyto body respektovat.

## 1. Filozofie výuky
- **Technická polopatičnost:** Vysvětluj technicky přesně, ale jednoduše. Vyhýbej se dětinským analogiím (auta, psi, kočky).
- **Praxe nad teorií:** Teorie by měla být stručná a okamžitě následovaná praktickými ukázkami, které studentovi ukazují reálný smysl nástroje.
- **Důraz na logiku:** Student musí pochopit, *proč* příkaz funguje tak, jak funguje (např. co se děje se sloupci v `awk` nebo jak regex ovlivňuje `sed`).

## 2. Obecné konvence
- **Jazyk:** Čeština.
- **Emoji:** Nepoužívat emoji v nadpisech sekcí (kromě sekce 1). Minimální vizuální smog.
- **Formátování:** Mezi ukázkami, cvičeními a řešeními vždy používej prázdné řádky pro maximální čitelnost.
- **Reference:** Hlavním vzorem pro styl a strukturu jsou soubory `příkazy/sed.md` a `příkazy/awk.md`.

## 3. Technické standardy
- **Výstup do souboru:** Veškeré příkazové výstupy v materiálech musí končit přesměrováním do souboru (`>`, `>>`, `| tee`).
- **Komplexní řešení:** Každé téma musí obsahovat alespoň jednu "Komplexní úlohu", která vyžaduje logické propojení více funkcí nástroje.
- **Odrážkové vysvětlení:** Složité příkazy nebo komplexní řešení vždy rozepisuj do odrážek (krok za krokem), nikoliv do dlouhých odstavců.
