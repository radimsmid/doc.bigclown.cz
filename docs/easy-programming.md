# Jednoduché programování domácí automatizace

## O čem projekt je a co ti přinese

Dlouhou dobu jsem snil o tom, že si postavím vlastní automatizaci.
Ale ten hardware...
Já prostě raději programuji a integruji.
Takže bylo super, když se objevilo Arduino, esp8266 a vše okolo.

No a teď s BigClownem to je ještě lepší, protože řeší věci v Arduinu neřešené - zajišťuje aby mi jednotky běžely na baterky nějakou rozumnou dobu a aby byla komunikace a vlastně celý systém bezpečný.
Funkčnost systému a závislosti se řeší pomocí skriptů v Pythonu nebo Node.js.

Proto jsem připravil tento projekt, který ti pomůže pochopit způsob vytváření těchto skriptů.
A pokud se nechceš trápit psaním, tak ti ukážu bezva nástroj NodeRED, který si kód píše sám.

Ukáži ti, jak:
* Pythonem při překročení nastavené meze rozsvítíme LEDku na Core Module
* Pes Node RED tweetovat teplotu a vlhkost z bezdrátového čidla
* Rozblikáme LEDku v případě, když někde ve prší
* Pomocí pluginu do Node RED si zobrazíme měřená data

## Jak na to...

### Kup si zvýhodněný [Basic Wireless Set](https://obchod.bigclown.cz/products/basic-wireless-set)

### Poskládej si jednotky Base a Remote

### Zprovozni si Raspberry Pi

Můžeš použít předpřipravené BigClown Raspberry Pi, nebo použij své, pokud na něm máš Raspbian Jessie.

#### Předpřipravené Raspberry Pi

Mělo by ti stačit ho aktualizovat a nainstalovat Node-RED těmito příkazy:
sudo apt-get update && sudo apt-get upgrade
sudo npm install -g node-red
