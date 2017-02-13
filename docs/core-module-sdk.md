# Popis SDK knihovny (API)


Core Module SDK (nebo-li Software Development Kit) je soubor driverů, examplů, hlavičkových souborů, apod.
Ve firmwarovém projektu ho najdeš v adresáři `sdk`.
Tento adresář je ve skutečnosti Git submodul a jeho zdrojový GitHub repozitář najdeš tady:

**https://github.com/bigclownlabs/bc-core-module-sdk**

To, že je to Git submodul má tu výhodu, že se dá "zamknout" na konkrétní verzi knihoven vůči firmware aplikaci.
Také ho můžeš kdykoliv aktualizovat na poslední (nebo konkrétní verzi).

Než se pustíš do vývoje firmware, měl by jsi vědět o tomhle referenčním manuálu pro SDK:

**http://sdk.bigclown.com**

Na výše uvedeném odkazu najdeš kompletní API referenci BigClown knihoven.


**Tohle jsou principy, kterých se držíme při vývoji API:**


* Jednoduchý přístup ke spodním vrstvám hardwaru.

* Okamžitý deklarativní způsob použití hardwarových produktů BigClown.

* Konzistentní a čistý API návrh napříč celou SDK knihovnou.

* Modulární a objektově orientovaný přístup.

* Samovysvětlující názvy pro funkce, datové typy, proměnné, apod.


## Automatické sestavování dokumentace


Všechny API jsou dokumentovaný pomocí **Doxygen komentářů přímo ve zdrojových kódech**.
Dokumentace se pak generuje automaticky pomocí Travis CI (z větve `master`).
Vygenerovaný obsah se hostuje přes GitHub Pages (z větve `gh-pages`).


Než začneš vývoj firmwaru, [zprovozni si vývojové prostředí](core-module-setup.md).


Více informací o základech Core Module najdeš na [tomto odkaze](core-module.md).

> **Poznámka:** Klidně můžeš používat Core Module jako low-level platformu bez našeho SDK-čka.
>               Tenhle přístup ale vyžaduje hlubší znalosti o embedded vývoji.
