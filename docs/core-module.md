# Core Module


<!-- toc -->


Tento návod ti vysvětlí základní vlastnosti a použití Core Module.

Na Core Codule je moderní low power Cortex M0+ procesor [STM32L083CZ](http://www.st.com/en/microcontrollers/stm32l083cz.html) s úctyhodnými 192kB Flash paměti pro všechny tvoje nápady.
K tomu jsme přidali rádiový komunikační modul, který na frekvenci 868 MHz s přehledem pokryje běžný rodinný dům.
Také zde nalezneš akcelerometr a digitální teplotní senzor.
Core Module můžeš snadno naprogramovat a napájet přes micro USB konektor.

Hlavní cíl už od návrhu byl důraz na nízkou spotřebu.
Všechny komponenty předurčují Core Module k trvalému napájení z baterií a to i několik let.
Proto i v bateriových modulech používáme nejnovější napájecí obvody s co nejnižší spotřebou. Nikde jsme nešetřili.

Pro hardware jsme vyvinuli také softwarové knihovny, které ti snadno zpřístupní všechny periferie, senzory a moduly. Software pro Core Module nazýváme Firmware, popis a návody nalezneš dál v této nápovědě.

K modulům a tagům, které se do Core Module zapojují, se dostaneme později.
Nyní už pojďme na samotný Core Module.
