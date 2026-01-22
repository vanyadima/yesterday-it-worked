<details>
<summary>E</summary>

<details>
<summary>int+route</summary>

настройка интерфейса

```bash
config
  interface <назв инт>
  ip address <ip/маска>
  port <назв порта>
  service-instance <навз порта>/<назв инт>
  encapsulation untagged
  connect ip interface <назв инт>
```

маршрут по умолчанию + dns

```bash
config
  ip route 0.0.0.0/0 <ip>
  ip name-server <ip dns сервера>
```

</details>

<details>
<summary>vlan</summary>

```bash
config
  interface <внут инт>
  ip address <ip роутера>
  port <назв порта>
  service-instance <навз порта>/<назв инт>
  encapsulation dot1q <номер vlan> exact
  rewrite pop 1
  connect ip interface <назв инт>
```

</details>

<details>
<summary>nat</summary>

```bash
config
  interface <внеш инт>
  ip nat outside
  interface <внут инт>
  ip nat inside
  ex

  ip nat pool LAN <диапозон ip внут сети>
  ip nat source dynamic inside-to-outside pool LAN overload interface <внеш инт>
```

</details>

<details>
<summary>dhcp-server</summary>

создать пулы, потом настраивать

```bash
ip pool POOL_A 192.168.1.10,192.168.1.20-192.168.1.50,192.168.1.100-192.168.1.200
ip pool POOL_B 172.16.0.10-172.16.255.254
```

```bash

! Создание DHCP-сервера с номером 1
dhcp-server 1

! Настройка глобальных параметров (опционально)
lease 3600
gateway 192.168.1.1
dns 8.8.8.8
ntp 8.8.8.8

! Создание IP-пулов
pool POOL_A 10
mask 255.255.255.0
lease 7200
gateway 192.168.1.1
exit

! Создание статической привязки
static ip 192.168.1.100
  chaddr 0123.4567.89ab
  mask 255.255.255.0
  lease 86400
  gateway 192.168.1.1
exit

! Привязка DHCP-сервера к интерфейсу
interface eth0
  dhcp-server 1
  exit
```

</details>

<details>
<summary>gre+ospf</summary>

```bash
config
  interface tunnel.0
  ip address 10.10.10.1/30
  ip tunnel <ip внешнего интерфейса роутера> <ip внешнего интерфейса соседнего роутера> mode gre
```

```bash
config
  router ospf 1
  ospf router-id 10.10.10.1
  passive-interface default
  no passive-interface tunnel.0
  network 10.10.10.0/30 area 0
exit
```


```bash
config
  interface tunnel.0
  ip ospf authentication message-digest 
  ip ospf message-digest-key 1 md5 P@ssw0rd
exit
```

</details>

</details>

<details>
<summary>O</summary>

<details>
<summary>установка</summary>

```bash
# временно выдаем ip адрес
ip addr add 192.168.1.2/24 dev eth0
или
dhcpcd eth0

# добавляем маршрут по умолчанию
ip route add default via 192.168.1.1 dev eth0

# прописываем dns сервер
echo "nameserver 77.88.8.8" > /etc/resolv.conf
```

Если у Вас VLAN, то прописываете:

```bash
ip link add link ens35 name ens35.300 type vlan id 300
ip link set ens35.300 up
ip addr add 192.168.1.2/24 dev ens35.300
ip route add default via 192.168.1.1
```

```bash
apt-get install openvswitch
systemctl enable --now openvswitch

reboot
```

ВАЖНО! В <code>/etc/net/ifaces/default/options</code> опцию <code>OVS_REMOVE</code> отключаем!

```bash
OVS_REMOVE=no
```

</details>

<details>
<summary>настройка коммутатора-моста</summary>

```bash
ovs-vsctl add-br sw1
ovs-vsctl add-port sw1 ens36
...
ovs-vsctl add-port sw1 ens40

# проверяем
ovs-vsctl show
```

настройка интерфейсов

```bash
mkdir /etc/net/ifaces/{ens36,ens37,ens38,ens39,ens40,sw1}
```

```bash
nano /etc/net/ifaces/ens36/options

BOOTPROTO=none
TYPE=eth
```

```bash
cp /etc/net/ifaces/ens36/options /etc/net/ifaces/ens37
cp /etc/net/ifaces/ens36/options /etc/net/ifaces/ens38
cp /etc/net/ifaces/ens36/options /etc/net/ifaces/ens39
cp /etc/net/ifaces/ens36/options /etc/net/ifaces/ens40
```

```bash
nano /etc/net/ifaces/sw1/options

BOOTPROTO=none
TYPE=ovsbr
```

добавляем автозапуск команду <code>ifconfig sw1 up</code>

```bash
nano /etc/systemd/system/network-online.target.wants/network.service

#меджу ExecStart= и ExecStop= пишем
ExecStartPost=/sbin/ifconfig sw1 up
```
```bash
systemctl daemon-reload
systemctl restart network
```

пример вывода ovs-vsctl show 

```bash
root@heirsunset ~I# ovs-vsctl show
e6ee7854-f7dd-48bl-8675-c68c65294bdf
  Bridge swl
    Port ens37
      Interface ens37
  Port swl
      Interface swl
          type: internal
  Port ens39
      Interface ens39
  Port ens38
      Interface ens38
  Port ens36
      Interface ens36
  ous_uersion: "3.3.2"
```

</details>

<details>
<summary>bond</summary>
  
```bash
ovs-vsctl add-bond sw1-cod bond0 ens21 ens22 bond_mode=active-backup
ovs-appctl bond/show # проверка
```

если нужно - включаем vlan 

```bash
ovs-vsctl set port bond0 trunk=100,200,300,400,500
```

</details>

<details>
<summary>STP</summary>

настройка будет в режиме 802.1w

sw1

```bash
ovs-vsctl set bridge sw1-a rstp_enable=true
ovs-vsctl set bridge sw1-a other_config:stp-protocol=rstp
ovs-vsctl set bridge sw1-a other_config:rstp-priority=0
ovs-appctl rstp/show # проверка
ovs-vsctl list bridge # проверка

```

sw2

```bash
ovs-vsctl set bridge sw2-a rstp_enable=true
ovs-vsctl set bridge sw2-a other_config:stp-protocol=rstp
ovs-appctl rstp/show # проверка
ovs-vsctl list bridge # проверка
```

</details>

<details>
<summary>vlan</summary>

обязательно включаем модуль 8021q

```bash
modprobe 8021q
#или
echo "8021q" | tee -a /etc/modules
```

tag - порт access (может быть только один)

trunk - порт trunk

```bash
ovs-vsctl add-port <инт. моста> <инт> tag=<номер vlan>
ovs-vsctl add-port <инт. моста> <инт> trunk=<номера vlan>
```

пример

```bash
ovs-vsctl add-port sw1-a ens20 tag=100
ovs-vsctl add-port sw1-a ens19 trunk=100,200,300
```

</details>

<details>
<summary>интерфейс</summary>

```bash
mkdir /etc/net/ifaces/mgmt-cod
vim /etc/net/ifaces/mgmt-cod/options
```


```bash
TYPE=ovsport
BOOTPROTO=static
CONFIG_IPV4=yes
BRIDGE=sw1-cod
VID=300
```

```bash
echo "192.168.30.10/24" > /etc/net/ifaces/mgmt-cod/ipv4address
echo "default via 192.168.30.1" > /etc/net/ifaces/mgmt-cod/ipv4route
```

```bash
systemctl restart network
```

Помимо того, что интерфейс mgmt-cod является портом доступа (access) необходимо использовать NativeVLAN

Проверить можно с помощью команды ovs-vsctl list port mgmt-cod

```bash
ovs-vsctl set port mgmt-cod vlan_mode=native-untagged
ovs-vsctl list port mgmt-cod # проверка
```

</details>

</details>

<details>
<summary>I 27.7</summary>

<details>
<summary>gateway</summary>

Заходим в консоль, добавляем шлюз

```bash
ip route add default via <ip роутера>
```

После этого входим в web интерфейс

Правила трафика -> Объекты -> Добавить

--

После чего создаем статический маршрут

Маршрутизация -> Статическая -> Добавить

Заполняем форму, указав:
* произвольное имя в поле Название;
* выбрав в качестве Назначения - Любой;
* выбрав в качестве Интерфейса - созданный интерфейс в сторону роутера;
* выбрав в поле Шлюз - ранее созданный объект

</details>

<details>
<summary>Получение лицензии</summary>

Управление сервером -> Лицензия

</details>

<details>
<summary>Создание интерфейса в сторону ЛС через web интерфейс</summary>

Управление сервером -> Лицензия

</details>

</details>

<details>
<summary>Кибер-свинарник</summary>

<details>
<summary>Установка</summary>

<details>
<summary>Установка образа</summary>

Установка будет произодиться на виртуальной машине.

Создаём виртуальную машину со следующими примерными характеристиками

```
25 гб RAM;

7 ядер;

sata0 - для установки ОС;
    
sata1 - для метаданных;
    
sata2 - для хранилища данных;
```

Устанавливаем, создаем кластер на внут. сеть

Обращаемся по IP-адресу на порт 8888;

Выполняем вход из под пользователя <code>admin</code> с паролем, который был задан на этапе установки для панели администратора;

</details>

<details>
<summary>Создание кластера хранилища</summary>

ИНФРАСТРУКТУРА - Серверы - Создать кластер хранилища

Задаём имя кластера и переходим в шестеренку напротив названия нашего сервера и выбираем Диски

Назначаем роли дискам:

  * Выбираем диск для метаданных;
  * Выбираем диск для хранилища и нажимаем приминить и создать

По окончанию создания Кластера хранилища в левом боковом меню должны появиться дополнительные вкладки:

  * Вычисления;
  * Сервисы хранилища:

Для создания вычислительного кластера необходимо настроить сеть на выбранных серверах, чтобы включить их в вычислительный кластер
  
Выбранная сеть должна включать все четыре необходимых типа трафика: 
```
VM private, 
VM public, 
Compute API 
VM backups
```
Включаем :)

</details>

<details>
<summary>Создание вычислительного кластера</summary>

ВЫЧИСЛЕНИЯ - Создать вычислительный кластер

Далее

Далее

На физической сети выбираем сеть, которую только что настраивали и выбираем нетегированная.

Далее

Включаем балансировку

Создать

</details>

<details>
<summary>Настройка вычислительного кластера</summary>

ВЫЧИСЛЕНИЯ - Сеть - Создать сеть

Указываем физическую нетегированную сеть

Добавляем подсеть IPv4. IP должны сходиться с ранним созданной сетью. Сервер DHCP должен быть отключен.

Далее

Выбираем все проекты - все права.

Далее

Создать сеть

Далее мы скачиваем образы. Например из этого репозитория http://ftp.altlinux.org/pub/distributions/ALTLinux/images/p10/cloud/x86_64/

Добавляем образы и ставим галочку "Использовать во всех проектах"

</details>

<details>
<summary>Настройка домена и учётных записей</summary>

НАСТРОЙКИ - Проекты и пользователи

Создать домен

Заходим в домен - проекты

Ставим лимиты, если нужно и называем проект

Создание пользователей производиться так же.

Пользователям для доступа необходимо выдавать именно доступ к панели самообслуживания, которая находится на порту 8800, а не к панели управления, которая расположена на порту 8888

</details>

</details>

<details>
<summary>Установка ВМ и подключение</summary>

Сначала генерируем ключи с помощью PuTTy

```bash
puttygen -t rsa -b 2048 -o Рабочий\ стол/Cloud-ADM.pem
```

Или через GUI

Заходим в Кибер-Свинарник и создаем сеть (не забываем про внешний DNS сервер и шлюз)

Идем в Маршрутизаторы, создаем (SNAT должен быть включен)

Создаем ВМ, укзаываем образ, тома, тип ВМ, интерфейсы и ОБЯЗАТЕЛЬНО!!! указываем скрипт настройки:

```
chpasswd:
  expire: false
  users:
  - {name: altlinux, password: P@ssw0rd, type: text}
  - {name: root, password: toor, type: text}
  ssh_pwauth: false

users:
  - name: altlinux
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: wheel
    shell: /bin/bash
    ssh_authorized_keys: ssh-rsa <ваш публичный ssh ключ>
```

После успешного запуска создаем плавающий IP адрес, чтобы мы могли подключаться удалённо

За настройку сети в Альт оптимизированной под использование в облочных провайдерах отвечает netplan (/etc/netplan/)

netplan в качестве бэкенда может использувать и другие сетевые подсистемы, например NetworkManager, для этого необходимо явно указать это в render. 

В результате, при последующих перезагрузках данной виртуальной машины IP-адрес следать не будет
 
</details>
