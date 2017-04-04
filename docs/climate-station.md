# Projekt "Klimastanice s LEDkoměrem a dashboardem"

<!-- toc -->


## O čem projekt je a co ti přinese

Klimastanice je jedním ze základních prvků domácí automatizace.
Informace o teplotě, vlhkosti, tlaku a koncentraci CO2 jsou potřebné pro zajištění správných životních podmínek a tepelné pohody doma i v kanceláři.

Základem projektu je jednotka skládající se z:

* 1x Base Module
* 1x Power Module s napájecím adaptérem
* 1x Core Module
* 1x Humidity Tag
* 1x Barometer Tag
* 1x Lux Meter Tag
* 1x LED pásek

Pro postavení originálního LEDkoměru budeš potřebovat:

* Al lištu
* Samolepku se stupnicí

Dále budeš potřebovat:

* 1x Raspberry Pi 3 nebo jiné PC s Linuxem
* PC nebo notebook s Windows, MacOS nebo Linux
* Ethernet kabel
* LAN router/switch s jedním volným portem
* Připojení k internetu

## Postup sestavení jednotky

1. Na Base Module zasuň Power Module
2. Na Power Module zasuň Core Module
3. Do volných 5pinových zásuvek Power Module zasuň Barometer a Humidity Tagy
4. Do pravé dolní pozice Core Module zasuň Lux Meter Tag
5. K Power Module připoj LED pásek
6. K Power module připoj napájení z adaptéru

## Postup vytvoření LEDkoměru

1. Zakup si [Al lištu] (http://.....)
2. Nainstaluj LED pásek do lišty
3. Nalep a ořízni stupnici

## Postup instalace a napojení aplikace Grafana

### Aktualizace

```
sudo apt update && sudo apt upgrade -y
```

### Firmware

Je dobré mít vždy vše aktuální, takže si zaktualizujeme firmware:

* Přečti si co je dfu mód [zde](https://doc.bigclown.cz/core-module-flashing.html#nahrávání-programu-přes-usb-dfu-bootloader)

* můžeš k tomu použít vlastní počítač pak postupuj dle návodu [zde](https://doc.bigclown.cz/core-module-flashing.html) firmware najdeš zde [https://github.com/bigclownlabs/bcp-climate-station/releases/latest](https://github.com/bigclownlabs/bcp-climate-station/releases/latest), půlmetrový pásek má 72 diod tedy stáhni firmware-72pixel.bin

* nebo k tomu využít Raspberry jako já, stáhni si poslední verzi firmware-72pixel.bin a nahraj ji do core pomocí těchto příkazů
  ```
  sudo apt install dfu-util wget
  wget $(wget "https://api.github.com/repos/bigclownlabs/bcp-climate-station/releases/latest" -q -O - | grep browser_download_url | grep firmware-72pixel.bin | head -n 1 | cut -d '"' -f 4)
  sudo dfu-util -s 0x08000000:leave -d 0483:df11 -a 0 -D firmware-72pixel.bin
  ```

#### InfluxDB

```
sudo apt install apt-transport-https
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
echo "deb https://repos.influxdata.com/debian jessie stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt update && sudo apt install influxdb
sudo systemctl daemon-reload
sudo systemctl enable influxdb
sudo systemctl start influxdb
```

Pokud chcete administrovat influx přes http tak v /etc/influxdb/influxdb.conf
odkomentujdete a upravte enabled = true a bind-address = ":8083"

#### Grafana

```
sudo apt install adduser libfontconfig -y
```

* rpi

    ```
	wget "https://github.com/fg2it/grafana-on-raspberry/releases/download/v4.1.2/grafana_4.1.2-1487023783_armhf.deb"
	sudo dpkg -i grafana_4.1.2-1487023783_armhf.deb
    ```
* x86-64

    ````
	sudo apt install -y apt-transport-https
	curl -sL https://packagecloud.io/gpg.key | sudo apt-key add -
	echo "deb https://packagecloud.io/grafana/stable/debian/ jessie main" | sudo tee /etc/apt/sources.list.d/grafana.list
	sudo apt update && sudo apt install grafana
    ````

```
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

### Gateway mezi USB a MQTT brokerem

* Pokud používáš Raspberry od nás nebo sis nahrál náž [bc-raspbian](https://github.com/bigclownlabs/bc-raspbian/releases/latest) použi tyto příkazy:

  ```
  sudo systemctl disable bc-workroom-gateway.service
  sudo systemctl stop bc-workroom-gateway.service
  ```

* Pokud máš svůj Raspbian, je nutné si přidat náš repozitář, co který řádek provádí je popsáno [zde](https://doc.bigclown.cz/raspberry-pi-installation.html#instalace-bigclown-balíčků-na-existující-systém):

  ```
  sudo apt install wget
  sudo sh -c 'echo "deb https://repo.bigclown.com/debian jessie main" >/etc/apt/sources.list.d/bigclown.list'
  wget https://repo.bigclown.com/debian/pubkey.gpg -O - | sudo apt-key add -
  sudo apt update && sudo apt upgrade -y
  sudo apt install mosquitto bc-common python3-docopt python3-paho-mqtt python3-serial
  ```

Společné
```
sudo apt install mosquitto-clients
wget "https://raw.githubusercontent.com/bigclownlabs/bch-gateway/master/bc-gateway.py" -O bc-gateway
sudo mv bc-gateway /usr/bin/bc-gateway
sudo chmod +x /usr/bin/bc-gateway
wget "https://raw.githubusercontent.com/bigclownlabs/bch-gateway/master/bc-gateway.service" -O "bc-gateway.service"
sudo mv bc-gateway.service /etc/systemd/system
sudo systemctl daemon-reload
sudo systemctl enable bc-gateway.service
sudo systemctl start bc-gateway.service
```

Test funkčnosti

* Zapneme LED na core
  ```
  mosquitto_pub -t 'node/climate-station/led/-/state/set' -m true
  ```

* Vypneme  LED na core
  ```
  mosquitto_pub -t 'node/climate-station/led/-/state/set' -m false
  ```

* Zapneme relátko
  ```
  mosquitto_pub -t 'node/climate-station/relay/-/state/set' -m true
  ```
  > **Hint** První pomoc:
  Pokud relé neseplo zkontroluj zda jsi připojili 5V DC adaptér do Power Modulu

* Vypneme  relátko
  ```
  mosquitto_pub -t 'node/climate-station/relay/-/state/set' -m false
  ```
* Zobrazíme si všechny zprávy na mqtt (ukončíte ctrl+c)
  ```
  mosquitto_sub -v -t '#'
  ```

### Vytvoreni databaze node v InfluxDB
```
curl -s "http://localhost:8086/query?q=CREATE+DATABASE+%22node%22&db=_internal"
```
kontrola zda došlo k vytvoření
```
curl -s "http://localhost:8086/query?q=SHOW+DATABASES&db=_internal" | grep \"node\"
```

### Spuštění služby která bude překopírovavat data z mqtt do InfluxDB

```
sudo apt install python3-pip
sudo pip3 install influxdb

wget "https://raw.githubusercontent.com/bigclownlabs/bcp-climate-station/master/hub/mqtt_to_influxdb.py" -O mqtt_to_influxdb
sudo mv mqtt_to_influxdb /usr/bin/mqtt_to_influxdb
sudo chmod +x /usr/bin/mqtt_to_influxdb
wget "https://raw.githubusercontent.com/bigclownlabs/bcp-climate-station/master/hub/mqtt_to_influxdb.service" -O mqtt_to_influxdb.service
sudo mv mqtt_to_influxdb.service /etc/systemd/system

sudo systemctl daemon-reload
sudo systemctl enable mqtt_to_influxdb.service
sudo systemctl start mqtt_to_influxdb.service
```

#### Nastavení Grafany

* Pripoj se na grafanu [http://ip-raspberry:3000](http://ip-raspberry:3000)  User `admin` a Password `admin`

* Vytvoření datasource

  * Klikneme na `Add data source` a vyplníme následující hodnoty:
    * Name: node
    * Type: InfluxDB
    * Url: http://localhost:8086
    * Database: node

  * Klikneme na `Add`, Grafana se pokusí připojit na InfluxDB úspěch oznámé takovouto hláškou `Data source is working`

* Import dashboardu

  * V levo nahoře klikneme na ikonku Grafany, vybereme `Dashboard` a `Import`

  * Stáhněte si do počítače soubor s dashboardem [https://raw.githubusercontent.com/bigclownlabs/bcp-climate-station/master/hub/grafana-climate-station.json](https://raw.githubusercontent.com/bigclownlabs/bcp-climate-station/master/hub/grafana-climate-station.json)

  * Vypereme možnost `Upload .json File` a vybereme stažený json file, teď už jen vybereme `node` ze seznamu dostupných datasource, to je ten který jsme si předchvílí vytvořily.

  * A klikneme na `Import`

  * Nyní by jste měli vidět naměřené hodnoty.

