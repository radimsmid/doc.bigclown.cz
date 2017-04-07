# Turris Omnia LXC

<!-- toc -->

## Vytvoření kontejnéru přes ssh

* Připoj se na ssh routru

* Doinstaluj si ovladač pro USB serial
  ```
  opkg update && opkg install kmod-usb-acm
  ```

* Tímto příkazem zahájíš vytváření kontejnéru s názvem test
  ```
  lxc-create -t download -n test
  ```

* Pro Ubuntu Xenial postupně vypň
  ```
  Distribution: Ubuntu
  Release: Xenial
  Architecture: armv7l
  ```

* Zda byl opravdu vytvořen se můžeš přesvědčit takto
  ```
  lxc-ls
  ```

* Tímto příkazem vložíš do konfigurace kontejnetu test mount na `/dev/ttyACM0` pod kterým se hlásí náše USB
  ```
  echo -e "
  lxc.autodev = 1
  lxc.group = onboot
  lxc.start.auto = 1
  lxc.start.delay = 30
  lxc.cgroup.devices.allow = c 166:* rwm
  lxc.mount.entry = /dev/ttyACM0 dev/ttyACM0 none  bind,create=file 0 0" >> /srv/lxc/test/config
  ```

* Nastartuj kontejnér
  ```
  lxc-start --name test
  ```

* Seznam puštěných kontejnérů
  ```
  lxc-ls --active
  ```

* Informace o kontejnéru včetně IP adresy
  ```
  lxc-info --name test
  ```

* Připoj se na kontejnér
  ```
  lxc-attach --name test
  ```

## Konfigurace kontejnéru

* Oprava locale
  ```
  sudo sh -c "echo LC_ALL=C >> /etc/default/locale"
  ```

* Mít aktuální systém je blaho tak šup
  ```
  apt update && apt upgrade -y
  ```

### Instalace ssh serveru

* Lézt na ssh přes root není moc cool, tedy vytvoř si nového uživatele
  ```
  adduser karel
  ```

* Přidej se do zkupiny sudo
  ```
  usermod -aG sudo karel
  ```

* Nainstaluj ssh server
  ```
  apt install openssh-server
  ```

* Nyní by ses měl moct připojit přes ssh

* Odpojíš se pomocí
  ```
  exit
  ```

