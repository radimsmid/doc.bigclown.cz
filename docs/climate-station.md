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

Je dobré mít vždy vše aktuální, takže si zaktualizujeme firmware, můžeš k tomu použít vlastní počítač pak postupuj dle návodu [zde](https://doc.bigclown.cz/core-module-flashing.html), nebo k tomu využít Raspberry jako já.

Přečti si co je dfu mód [zde](https://doc.bigclown.cz/core-module-flashing.html#nahrávání-programu-přes-usb-dfu-bootloader)

* Nainstaluj si nástroj pro aktualizaci

  ```
  sudo apt install dfu-util
  ```

* Stáhni si aktuální firmware přímo z repozitáře [https://github.com/bigclownlabs/bcp-climate-station/releases/latest]([https://github.com/bigclownlabs/bcp-climate-station/releases/latest])
půlmetrový pásek má 72 diod tedy stáhni firmware-72pixel.bin, nebo použi tento příkaz
  ```
  wget $(wget "https://api.github.com/repos/bigclownlabs/bcp-climate-station/releases/latest" -q -O - | grep browser_download_url | grep firmware-72pixel.bin | head -n 1 | cut -d '"' -f 4)
  ```

* Nebo si jej zkompiluj
  ```
  git clone --recursive https://github.com/bigclownlabs/bcp-climate-station.git
  cd bcp-climate-station
  make release PIXEL_COUNT=72
  make dfu
  ```

a pro nahrání použi tento příkaz
  ```
  sudo dfu-util -s 0x08000000:leave -d 0483:df11 -a 0 -D firmware-72pixel.bin
  ```

#### InfluxDB

```
sudo apt install apt-transport-https
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
echo "deb https://repos.influxdata.com/debian jessie stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
sudo apt-get update && sudo apt-get install influxdb
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
	sudo apt-get install -y apt-transport-https
	curl -sL https://packagecloud.io/gpg.key | sudo apt-key add -
	echo "deb https://packagecloud.io/grafana/stable/debian/ jessie main" | sudo tee /etc/apt/sources.list.d/grafana.list
	sudo apt-get update && sudo apt-get install grafana
    ````

```
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```


