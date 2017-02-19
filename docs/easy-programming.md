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

`sudo apt-get update && sudo apt-get upgrade`
`sudo npm install -g node-red`

#### Vlastní Raspberry Pi

* Přidej si repozitář BigClown
* Nainstaluj si aktuální verzi node.js pomocí těchto příkazů `curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -` a `sudo apt install -y nodejs`
* Nainstaluj si Node-RED `sudo npm install -g node-red`
* Nainstaluj si MQTT broker `sudo apt-get install mosquitto mosquitto-clients`
* Nainstaluj si gateway sloužící pro komunikaci mezi MQTT a jednotkou `sudo apt-get install bc-workroom-gateway`

### Pošli si a přijmi pár testovacích zpráv

#### Rozsvícení diody
`mosquitto_pub -t "nodes/base/light/-/set" -m '{"state": true}'`

#### Zhasnutí diody
`mosquitto_pub -t "nodes/base/light/-/set" -m '{"state": false}'`

#### Zachycení zpráv z Remote jednotky
`mosquitto_sub -v -t 'nodes/remote/#'`

> **Poznámka** Chce to chvilku trpělivosti data chodí jednou za 30s až dorazí uvidíš něco jako
> `nodes/remote/thermometer/i2c0-49 {"temperature": [24.18, "\u2103"]}`
> `nodes/remote/humidity-sensor/i2c0-40 {"relative-humidity": [32.0, "%"]}`

**Pokud ses dostal až sem tak vše funguje jak má a můžeme se posunout k zajímavějším věcem.**
