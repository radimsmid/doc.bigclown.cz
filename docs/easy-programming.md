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

> **Poznámka:** Chce to chvilku trpělivosti data chodí jednou za 30s až dorazí uvidíš něco jako
> `nodes/remote/thermometer/i2c0-49 {"temperature": [24.18, "\u2103"]}`

> `nodes/remote/humidity-sensor/i2c0-40 {"relative-humidity": [32.0, "%"]}`

**Pokud ses dostal až sem, tak vše funguje jak má a můžeme se posunout k zajímavějším úlohám.**

### Napiš si v Pythonu jednoduchý skript

Napiš si v Pythonu skript, který přijme přes MQTT data z bezdrátového čidla teploty a vlhkosti a rozsvítí LEDku, když jedna z hodnot přeleze nastavenou mez.

### Pomocí Node-RED zatvítuj teplotu a vlhkost

1. Spusť si Node-RED příkazem
`node-red-pi`

2. V prohlížeči si otevři novou záložku a zadej URL **http://ip-tveho-raspberry:1880**

3. Vpravo nahoře klikni na menu > import > clipboard a vlož následující text:
[{"id":"92298281.d0ca5","type":"tab","label":"Twitter(Teplota a vlhkost)"},{"id":"2d1d3d32.b2d7d2","type":"mqtt in","z":"92298281.d0ca5","name":"thermometer","topic":"nodes/remote/thermometer/+","qos":"2","broker":"fc8241ff.e69d68","x":133.5,"y":103,"wires":[["65e5412e.ef424"]]},{"id":"2d5a3b21.61043c","type":"function","z":"92298281.d0ca5","name":"text","func":"var text = \"teplota: \" + \nmsg.payload[\"temperature\"][0] + \nmsg.payload[\"temperature\"][1] + \n\" vlhkost: \" +\nmsg.payload[\"relative-humidity\"][0] +\nmsg.payload[\"relative-humidity\"][1];\n\nmsg.payload = text;\nreturn msg;","outputs":1,"noerr":0,"x":747.5,"y":176,"wires":[["8ec0d340.85b338","557fae8c.a8c028"]]},{"id":"8ec0d340.85b338","type":"debug","z":"92298281.d0ca5","name":"","active":true,"console":"false","complete":"payload","x":994,"y":88,"wires":[]},{"id":"557fae8c.a8c028","type":"twitter out","z":"92298281.d0ca5","twitter":"","name":"Tweet","x":990.5,"y":267,"wires":[]},{"id":"742fb418.b09dfc","type":"join","z":"92298281.d0ca5","name":"","mode":"custom","build":"merged","property":"payload","propertyType":"msg","key":"topic","joiner":"\\n","timeout":"5","count":"2","x":417.5,"y":179,"wires":[["17ff364d.4d7c82"]]},{"id":"2448519.8315d2e","type":"mqtt in","z":"92298281.d0ca5","name":"humidity-sensor","topic":"nodes/remote/humidity-sensor/+","qos":"2","broker":"fc8241ff.e69d68","x":120.5,"y":262,"wires":[["221f12cf.db5476"]]},{"id":"65e5412e.ef424","type":"json","z":"92298281.d0ca5","name":"","x":272.5,"y":151,"wires":[["742fb418.b09dfc"]]},{"id":"221f12cf.db5476","type":"json","z":"92298281.d0ca5","name":"","x":266.5,"y":206,"wires":[["742fb418.b09dfc"]]},{"id":"17ff364d.4d7c82","type":"delay","z":"92298281.d0ca5","name":"","pauseType":"rate","timeout":"5","timeoutUnits":"seconds","rate":"1","nbRateUnits":"5","rateUnits":"minute","randomFirst":"1","randomLast":"5","randomUnits":"seconds","drop":true,"x":576.5,"y":226,"wires":[["2d5a3b21.61043c"]]},{"id":"fc8241ff.e69d68","type":"mqtt-broker","z":"","broker":"localhost","port":"1883","clientid":"","usetls":false,"compatmode":true,"keepalive":"30","cleansession":true,"willTopic":"","willQos":"0","willPayload":"","birthTopic":"","birthQos":"0","birthPayload":""}]

Tímto jsi naimportoval flow, který každých 5 minut tvítne zprávu o teplotě a vlhkosti.

### Pomocí Node-RED budeme blikat LEDkou, pokud bude v Londýně pršet
Možná je to trochu mimo, ale ukazuje to na nekonečné možnosti dnešní domácí automatizace :-)

1. Doinstaluj si **openweathermap** `sudo npm install -g node-red-node-openweathermap`

2. Zaregistruj se na **https://openweathermap.org/appid** a získej **api key**, který vlož do komponenty openweathermap, nastav město na **London** a zemi na **GB**

3. Naprogramuj funkci, která otestuje zda v Londýně prší a rozbliká LED na Base jednotce


### Pomocí Node-RED si graficky zobraz naměřená data

1. Doinstaluj si `sudo npm install -g node-red-vis`

2. V prohlížeči si otevři novou záložku a zadej URL **http://ip-tveho-raspberry:1880/vis/edit.html**

3. Podívej se na grafické zobrazení naměřených hodnot **http://ip-tveho-raspberry:1880/vis/**
