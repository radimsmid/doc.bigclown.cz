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


## Blokový diagram


Tady ti naši grafici připravili přehled pojmenování pinů a znázornění základních bloků.
Popis pinů budeš potřebovat, pokud budeš zapojovat nějaké vlastní externí senzory a součástky.
Pokud budeš používat tagy a moduly, tak ty mají v SDK své vlastní piny a nemusíš se o ně starat.


![](images/core-module/core-module.png)


## Mapování GPIO pinů


V následující tabulce je popis pinů a periferií procesoru  [STM32L083CZ](http://www.st.com/en/microcontrollers/stm32l083cz.html):


| Pin | Signál     | MCU Pin      | 5 V Tolerant  |
| --- | :--------- | :----------- | :----------- |
|   1 | P0/A0/TXD0 | PA0  (10)    | -            |
|   2 | P1/A1/RXD0 | PA1  (11)    | Ano          |
|   3 | P2/A2/TXD1 | PA2  (12)    | Ano          |
|   4 | P3/A3/RXD1 | PA3  (13)    | Ano          |
|   5 | P4/A4/DAC0 | PA4  (14)    | -            |
|   6 | P5/A5/DAC1 | PA5  (15)    | -            |
|   7 | P6/RTS1    | PB1  (19)    | Ano          |
|   8 | P7/CTS1    | PA6  (16)    | Ano          |
|   9 | P8         | PB0  (18)    | Ano          |
|  10 | P9         | PB2  (20)    | Ano          |
|  21 | P10/RXD2   | PA10 (31)    | Ano          |
|  22 | P11/TXD2   | PA9  (30)    | Ano          |
|  23 | P12/MISO   | PB14 (27)    | Ano          |
|  24 | P13/MOSI   | PB15 (28)    | Ano          |
|  25 | P14/SCLK   | PB13 (26)    | Ano          |
|  26 | P15/CS     | PB12 (25)    | Ano          |
|  27 | P16/SCL1   | PB8  (45)    | Ano          |
|  28 | P17/SDA1   | PB9  (46)    | Ano          |


<!--
Pro jednotlivé GPIO piny platí následující omezení:

* Maximum sink current for any GPIO is 16 mA.
* Maximum source current for any GPIO is -16 mA.
* Maximum total output current sunk / sourced by all GPIOs is 90 / -90 mA.
-->


## Schéma hardwaru


Kompletní schéma Core Module je [ke stažení v PDF](https://github.com/bigclownlabs/bc-hardware/raw/master/out/bc-module-core/bc-module-core-rev-1-3-sch.pdf) nebo v Eagle návrhovém systému na našem [GitHubu](https://github.com/bigclownlabs/).
