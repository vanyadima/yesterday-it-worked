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

```bash
apt-get install openvswitch
systemctl enable --now openvswitch

reboot
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
TYPE=bridge
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
<summary>lacp</summary>

</details>

<details>
<summary>vlan</summary>

</details>

</details>

<details>
<summary>I</summary>

<details>
<summary>ip</summary>

на время

```bash
ip addr add <ip/mask> dev <int>
ip link set <int> up
```

```bash
ip route add 0.0.0.0/0 via <интерфейс роутера>
```

</details>

</details>
