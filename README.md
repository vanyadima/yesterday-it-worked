<details>
<summary>E</summary>

<details>
<summary> подключение к BGP</summary>

```bash
router bgp 64500
bgp router-id 178.207.179.4 # уникальный идентификатор 
neighbor <ip провайдера> remote-as 31133
write
```

</details>

<details>
<summary>Подключение к RADIUS</summary>

```bash
rtr-cod(config)#security none 
rtr-cod(config)#aaa radius-server 192.168.10.1 port 1812 secret P@ssw0rd auth
rtr-cod(config)#aaa precedence local radius
```

</details>
  
<details>
<summary>Подключение к Zabbix</summary>

```bash
rtr-cod(config)#security none
rtr-cod(config)#snmp-server enable snmp
rtr-cod(config)#snmp-server community public ro 
```

</details>

<details>
<summary>NTP</summary>

```bash
rtr-cod(config)#ntp timezone utc+3
rtr-cod(config)#ntp server <адрес>
```
проверка
```bash
sh ntp timezone
sh ntp status
```
</details>

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

!!! ПОСЛЕ КАЖДОГО СОЗДАННОГО ИНТЕРФЕЙСА ОБНОВЛЯЕМ СТРАНИЦУ !!!

Сервисы - Сетевые адаптеры

В интерфейсах добавляем Ethernet

Настройки:
  * LAN
  * Системных контекст
  * Физический порт (выбираем свободный)
  * IP без конфигурации

Нажимаем добавить.

--

Добавляем VLAN

Настройки: 
  * Название
  * Тег VLAN
  * IP статический

</details>

<details>
<summary>Подключение агента Zabbix</summary>

Мониторинг - Zabbix-агент

Пассивыный режим

Сохранить

</details>

</details>

<details>
<summary>Кибер</summary>

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

</details>

<details>
<summary>Alt Server</summary>

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

Сначала добавляем tag, потом trunk!

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
<summary>PostgreSQL</summary>

```bash
apt-get update && apt-get install -y postgresql17-server
/etc/init.d/postgresql initdb
systemctl enable --now postgresql
```

Разрешаем доступ к PostgreSQL из сети отредактировав конфигаруционный файл /var/lib/pgsql/data/postgresql.conf и в конфиге находим <code>listen_addresses = 'localhost'</code>

Изменяем

```bash
listen_addresses = '*'
```

Настраиваем парольную аутентификацию для удалённого доступа отредактировав конфигаруционный файл /var/lib/pgsql/data/pg_hba.conf. Добавляем это:

```bash
host  all  all  0.0.0.0/0  md5
```

Перезагружаем

```bash
systemctl restart postgresql
```

Создаем пользователя и БД

```bash
createuser -U postgres --superuser --encrypted --pwprompt superadmin
su - postgres -s /bin/sh -c 'createdb -O superadmin superadmin'
```

</details>

<details>
<summary>SAMBA - добавление зон</summary>

```bash
samba-tool dns add 127.0.0.1 <домен> <имя устр-ва, которого добавляем> A <ip устройства, которого добавляем> -U administrator
# пример
samba-tool dns add 127.0.0.1 office.ssa2026.region rtr-a A 172.20.10.254 -U administrator
```

</details>

<details>
<summary>SAMBA - развертываение домена</summary>

```bash
echo "nameserver 77.88.8.8" > /etc/resolv.conf
apt-get update && apt-get install -y task-samba-dc bind bind-utils
```

Если при установке системы в настройках сети было указано полное имя домена (например, dc-a.office.ssa2026.region), система может автоматически создать зону office.ssa2026.region, что приведёт к конфликту с Samba при запуске bind

Для решения проблемы необходимо закомментировать все строки в файле /etc/bind/local.conf

```bash
control bind-chroot disabled
echo 'KRB5RCACHETYPE="none"' >> /etc/sysconfig/bind
echo 'include "/var/lib/samba/bind-dns/named.conf";' >> /etc/bind/named.conf
```

в options.conf

```bash
tkey-gssapi-keytab "/var/lib/samba/bind-dns/dns.keytab";
minimal-responses yes;
```
в разделе  logging

```bash
category lame-servers { null; };
```

Восстановление Samba

```bash
rm -f /etc/samba/smb.conf
rm -f /etc/samba/smb.conf
rm -rf /var/cache/samba
mkdir -p /var/lib/samba/sysvol
samba-tool domain provision
```

Не забываем указать BIND9_DLZ

```bash
systemctl enable --now samba bind
cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
systemctl restart samba
```

</details>

<details>
<summary>RADIUS сервер</summary>

```bash
echo "nameserver 77.88.8.8" > /etc/resolv.conf
apt-get update && apt-get install -y freeradius freeradius-utils
systemctl enable --now radiusd
```

Заходим в файл /etc/raddb/clients.conf

```bash
client ALL {
  ipaddr = 0.0.0.0
  netmask = 0
  secret = P@ssw0rd
```
Добавляем в конец следующее /etc/raddb/users

```bash
netuser Cleartext-Password := "P@ssw0rd"
        Service-Type = Administrative-User,
        Cisco-AVPair = "shell:roles=admin"
```

Перезагруаем

</details>

<details>
<summary>RADIUS клиент</summary>

```bash
apt-get update && apt-get install -y pam_radius
```

Редактируем конфигурационный файл /etc/pam_radius_auth.conf

```bash
<ip сервера> P@ssw0rd  3
```

Редактируем конфигурационный файл /etc/pam.d/sshd и /etc/pam.d/system-auth-local

```bash
auth  sufficient  pam_radius_auth.so
```

Добавляем пользователя 

```bash
useradd netuser
```

```bash
<ip сервера> P@ssw0rd  3
```

</details>

</details>

<details>
<summary>Alt Workstation</summary>

<details>
<summary>DBeaver</summary>

Должен быть настроен BIND!

```bash
apt-get update && apt-get install -y eepm
epm play -y dbeaver
```

Если не скачивается - добавляем proxy и сертификат

</details>

<details>
<summary>Управление доменом с помощью ADMC</summary>

```bash
apt-get install -y admc
```

Задание:

Создайте подразделения, группы и пользователей

Создайте подразделения ofadmins и ofusers

Создайте группы ofadmins и ofusers

Добавьте группы в соответствующие подразделения

Создайте пользователей ofadmin1, ofuser1 и user1

Добавьте пользователей в соответствующие подразделения и группы

Пользователя user1 не добавляете в группы ofadmins и ofusers
   
В качестве пароля используйте P@ssw0rd

Для cli-a настройте политику изменения рабочего стола на картинку компании, а также запретите использование пользователям изменение сетевых настроек и изменение графических параметров рабочего стола.

--

Нажимаемна название домена и сверху папочка, нажимаем - создаем подразделене

В нем создаем группу

И в группе - пользователей

Устанавливаем групповые политики

```bash
apt-get install -y gpui gpupdate
gpupdate-setup enable
```

В соответствие с требованиями задания - реализуем необходимый функционал:

Задаём картинку компании для рабочего стола и запрещаем её менять

но, чтобы на cli2-a (или же из под любого другого пользователя) картинка также была установлена, её необходимо разместить на общем ресурсе (например, создав общую папку)

```bash
Компьютер - Административные шаблоны - Настройки оболочки - Внешний вид - Фон рабочего стола
```

Запрещаем изменение сетевых настроек:

пройтись по всему списку и выставить Включено, с вариантом ограничений No (выставить чек-бокс Блокировать):

```bash
Компьютер - Административные шаблоны - Правила Polkit - Ограничения NetworkManager
```

</details>

<details>
<summary>NTP</summary>

```bash
timedatectl  set-timezone Europe/Moscow
```

Редактируем /etc/chromy.conf

```bash
pool <ip ntp> iburst 
```


```bash
systemctl restart chronyd
timedatectl # проверка
```
</details>

</details>

<details>
<summary>lll</summary>

<details>
<summary>1</summary>

```bash
mkdir -p /home/altlinux/Projects/Project_01
mkdir /home/altlinux/Projects/Project_01/terraform
cd /home/altlinux/Projects/
```

Создадим файл с именем cloudinit.conf (имя произвольное) и поместим в него следующее содержимое: 

```bash
export OS_AUTH_URL=https://cyberinfra.ssa2026.region:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_AUTH_TYPE=password
export OS_PROJECT_DOMAIN_NAME=Competence_SiSA
export OS_USER_DOMAIN_NAME=Competence_SiSA
export OS_PROJECT_NAME=Project0
export OS_USERNAME=User0
export OS_PASSWORD=P@ssw0rd
```

```bash
source cloudinit.conf
```

Для корректной работы с Terraform, необходимо создадать файл конфигурации зеркала

Файл должен иметь имя .terraformrc ибыть расположен в домашнем каталоге пользователя

Файл ~/.terraformrc должен содержать в себе следующее:

```bash
cd Project_01/terraform/
```

Создадим файл provider.tf и опишем параметры для подключения к провайдеру openstack для работы с облаком: 

```bash
terraform {
  required_providers {
    openstack = {
      source  = "terraform-provider-openstack/openstack"
      version = "2.1.0"
    }
  }
}

provider "openstack" {
  auth_url    = "https://cyberinfra.ssa2026.region:5000/v3"
  tenant_name = "Project0"
  user_name   = "User0"
  password    = "P@ssw0rd"
  insecure    = true
}
```

```bash
terraform init
```

```bash
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub > cloud-init.yml
```

Откроем файл cloud-init.yml на редактирование и добавим в самое начало (до содержимого ключа ssh): 

```bash
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
    ssh_authorized_keys:
      - <СОДЕРЖИМОЕ_ПУБЛИЧНОГО_КЛЮЧА_SSH>
```

Создадим файл vm-game.tf и опишем конфигурацию для создаваемых инстансов game01, game02 и game03:

```bash
resource "openstack_compute_instance_v2" "game" {
  count     = "3"
  name      = "game0${count.index + 1}"
  flavor_id = "03bf1b85-2f5f-4ada-a07b-8b994b6dcb57"
  user_data = file("cloud-init.yml")

  block_device {
    uuid                  = "827e08fa-fd3c-41cd-92ca-845bb5018478"
    source_type           = "image"
    volume_size           = "10"
    boot_index            = 0
    destination_type      = "volume"
    delete_on_termination = true
  }

  network {
    port = openstack_networking_port_v2.port_vm_game[count.index].id
  }
}
```

Создадим файл vm-haproxy01.tf и опишем конфигурацию для создаваемого инстанса haproxy01: 

```bash
resource "openstack_compute_instance_v2" "haproxy" {
  name            = "haproxy01"
  flavor_id       = "03bf1b85-2f5f-4ada-a07b-8b994b6dcb57"
  user_data       = file("cloud-init.yml")

  block_device {
    uuid                  = "827e08fa-fd3c-41cd-92ca-845bb5018478"
    source_type           = "image"
    volume_size           = "10"
    boot_index            = 0
    destination_type      = "volume"
    delete_on_termination = true
  }

  network {
    port = openstack_networking_port_v2.haproxy.id
  }
}
```

Создадим файл network.tf и опишем конфигурацию сети в соответствие с топологией для Project_01:

```bash
resource "openstack_networking_port_v2" "port_vm_game" {
    count          = "3"
    name           = "port_vm_game0${count.index + 1}"
    network_id     = "61845892-f9cc-4fde-962c-34b59425a74d"
    admin_state_up = true

    fixed_ip {
        subnet_id   = "13592ca4-8782-410b-9bcc-90810ccab6fe"
        ip_address  = "192.168.1.10${count.index + 1}"
    }
}

resource "openstack_networking_port_v2" "haproxy" {
    name           = "port_vm_haproxy"
    network_id     = "61845892-f9cc-4fde-962c-34b59425a74d"
    admin_state_up = true

    fixed_ip {
        subnet_id   = "13592ca4-8782-410b-9bcc-90810ccab6fe"
        ip_address  = "192.168.1.100"
    }
}
```

Создадим файл floatingip.tf и опишем конфигурацию Плавающего IP-адреса в соответствие с топологией для Project_01:

```bash
resource "openstack_networking_floatingip_v2" "floatingip_haproxy" {
  pool = "public"
}

resource "openstack_networking_floatingip_associate_v2" "association_haproxy" {
  port_id     = openstack_networking_port_v2.haproxy.id
  floating_ip = openstack_networking_floatingip_v2.floatingip_haproxy.address
}
```

```bash
terraform validate
cd /home/altlinux/Projects/Project_01
```

deploy_project_01.sh

```bash
cd /home/$USER/Projects
source cloudinit.conf
cd /home/$USER/Projects/Project_01/terraform
terraform init
terraform apply -auto-approve

sleep 60
echo "done"
```

```bash
chmod +x deploy_project_01.sh
./deploy_project_01.sh
```

Создадим директорию ansible в ранее созданной директории для Project_01:

```bash
mkdir /home/altlinux/Projects/Project_01/ansible
cd /home/altlinux/Projects/Project_01/ansible
ansible.cfg
```

Cоздадим файл inventory.yml и опишем файл инвентаря для Ansible: 

```bash
all:
  children:
    proxys:
      hosts:
        haproxy01:

    games:
      hosts:
        game01:
        game02:
        game03:
```

```bash
mkdir group_vars
```

Создадим файл 'all.yml' в директории group_vars:

```bash
---
ansible_python_interpreter: /usr/bin/python3
ansible_ssh_user: altlinux
ansible_ssh_private_key_file: ~/.ssh/id_rsa
```

```bash
ansible -m ping all
```

Распаковываем архив в директорию /home/altlinux/Projects/Project_01/2048-game/:

```bash
unzip ~/Project_01.zip -d /home/altlinux/Projects/Project_01/2048-game/
```

В директории /home/altlinux/Projects/Project_01/ansible создаём файл Dockerfile

```bash
FROM node:16-alpine AS builder

WORKDIR /2048-game

COPY package*.json ./
RUN npm install --include=dev

COPY . .
RUN npm run build

EXPOSE 8080

FROM nginx:stable-alpine3.19
COPY --from=builder /2048-game/dist /usr/share/nginx/html
```

В директории /home/altlinux/Projects/Project_01/ansible создаём файл games_playbook.yml: 

```bash
---
- hosts: games
  become: true

  tasks:
    - name: Install docker
      community.general.apt_rpm:
        name: 
          - docker-engine
          - docker-buildx
        state: present
        update_cache: true

    - name: Started and enabled docker
      ansible.builtin.systemd:
        name: docker
        state: started
        enabled: true

    - name: Copying the project files
      ansible.builtin.copy:
        src: ../2048-game/
        dest: "/home/{{ ansible_ssh_user }}/2048-game/"

    - name: Copying the Dockerfile
      ansible.builtin.copy:
        src: ./Dockerfile
        dest: "/home/{{ ansible_ssh_user }}/2048-game/"

    - name: Build docker image
      community.docker.docker_image_build:
        name: "2048-game"
        tag: latest
        path: "/home/{{ ansible_ssh_user }}/2048-game/"
        dockerfile: Dockerfile

    - name: Start docker container
      community.docker.docker_container:
        name: "2048-game"
        image: "2048-game"
        ports: "80:80"
        state: started
        restart: true
```

На текущем этап можно выполнить тестовый запуск созданного playbook-а games_playbook.yml:

```bash
ansible-playbook games_playbook.yml
```

Таким образом при обращении в браузере с Cloud-ADM по описанным ниже url должно открываться веб-приложение

В браузере: game01.dev.au.team

В директории /home/altlinux/Projects/Project_01/ansible создаём директорию files

```bash
mkdir files
```

В директории /home/altlinux/Projects/Project_01/ansible/files создаём конфигурационный файл haproxy.cfg

```bash
global
    log /dev/log daemon
    chroot      /var/lib/haproxy
    pidfile     /run/haproxy.pid
    maxconn     4000
    user        _haproxy
    group       _haproxy
    daemon
    stats socket /var/lib/haproxy/stats

defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

frontend http_front
    bind *:80
    bind *:443 ssl crt /var/lib/ssl/game.pem
    default_backend http_back

backend http_back
    balance roundrobin
    server game01 192.168.1.101:80 check
    server game02 192.168.1.102:80 check
    server game03 192.168.1.103:80 check

listen stats
bind :80
bind *:443 ssl crt /var/lib/ssl/game.pem
stats enable
stats uri /
stats refresh 5s
stats realm Haproxy\ Stats
```

```bash
openssl req -x509 -sha256 -days 3653 -newkey rsa:2048 -keyout ca.key -out ca.crt
sudo cp ca.crt /etc/pki/ca-trust/source/anchors/ && sudo update-ca-trust
mv ca.* files/
openssl genrsa -out files/game.key 2048
openssl req -key files/game.key -new -out files/game.csr
```

Файл с расширениями для сертификата

```bash
cat <<EOF > files/game.ext
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
subjectAltName=@alt_names
[alt_names]
DNS.1=game.au.team
IP.1=192.168.1.100
EOF
```

Выпускаем сертификат

```bash
openssl x509 -req -CA files/ca.crt -CAkey files/ca.key -in files/game.csr -out files/game.crt -days 365 -CAcreateserial -extfile files/game.ext
```

```bash
cat files/game.key files/game.crt > files/game.pem
```

В директории /home/altlinux/Projects/Project_01/ansible создаём файл proxys_playbook.yml: 

```bash
---
- hosts: proxys
  become: true

  tasks:
    - name: Install HAProxy
      community.general.apt_rpm:
        name: haproxy
        state: present
        update_cache: true
    
    - name: Copy file 'haproxy.cfg'
      ansible.builtin.copy:
        src: files/haproxy.cfg
        dest: /etc/haproxy/haproxy.cfg
      notify:
        - Restarted HAProxy

    - name: Copy certificate for HAProxy
      ansible.builtin.copy:
        src: files/game.pem
        dest: /var/lib/ssl/game.pem
      notify:
        - Restarted HAProxy

    - name: Started and enabled HAProxy
      ansible.builtin.systemd:
        name: haproxy
        state: restarted
        enabled: true

  handlers:
    - name: Restarted HAProxy
      ansible.builtin.systemd:
        name: haproxy
        state: restarted
```

На текущем этап можно выполнить тестовый запуск созданного playbook-а proxys_playbook.yml:

```bash
ansible-playbook proxys_playbook.yml
```

Перейдём в директорию /home/altlinux/Projects/Project_01

```bash
cd /home/altlinux/Projects/Project_01
```

Создадим файл configure_project_01.sh и укажем инструкции необходимые для запуска Ansible:

```bash
export PATH=/home/altlinux/.local/bin:$PATH
cd /home/$USER/Projects/Project_01/ansible
sleep 10
ansible-playbook games_playbook.yml
sleep 5
ansible-playbook proxys_playbook.yml
```

```bash
chmod +x configure_project_01.sh
./configure_project_01.sh
```

Создадим файл destroy_project_01.sh и укажем инструкции необходимые для запуска Ansible:

```bash
cd /home/$USER/Projects
source cloudinit.conf
cd /home/$USER/Projects/Project_01/terraform
terraform destroy -auto-approve
rm -f ~/.ssh/known_hosts

echo "done"
```

```bash
chmod +x destroy_project_01.sh
./destroy_project_01.sh
```

</details>

<details>
<summary>2</summary>


```bash
cd ~/Projects/
mkdir -p Project_02/terraform
cd Project_02/terraform
cp ~/Projects/Project_01/terraform/provider.tf ./
terraform init
cp ~/Projects/Project_01/terraform/network.tf ./
```

модернизируем файл network.tf в текущей директории для Project02: 

```bash
resource "openstack_networking_port_v2" "port_vm_acm-server" {
    name           = "port_acm-server"
    network_id     = "61845892-f9cc-4fde-962c-34b59425a74d"
    admin_state_up = true

    fixed_ip {
        subnet_id   = "13592ca4-8782-410b-9bcc-90810ccab6fe"
        ip_address  = "192.168.1.104"
    }
}

resource "openstack_networking_port_v2" "port_vm_db-server" {
    name           = "port_db-server"
    network_id     = "61845892-f9cc-4fde-962c-34b59425a74d"
    admin_state_up = true

    fixed_ip {
        subnet_id   = "13592ca4-8782-410b-9bcc-90810ccab6fe"
        ip_address  = "192.168.1.105"
    }
}

resource "openstack_networking_port_v2" "port_vm_bar-agent01" {
    name           = "port_bar-agent01"
    network_id     = "61845892-f9cc-4fde-962c-34b59425a74d"
    admin_state_up = true

    fixed_ip {
        subnet_id   = "13592ca4-8782-410b-9bcc-90810ccab6fe"
        ip_address  = "192.168.1.106"
    }
}
```

Скопируем файл network.tf из Project01:

```bash
cp ~/Projects/Project_01/terraform/vm-game.tf ./vm.tf
```

модернизируем файл vm.tf в текущей директории для Project02: 

```bash
resource "openstack_compute_instance_v2" "acm-server" {
  name      = "ACM-Server"
  flavor_id = "101"
  user_data = file("cloud-init.yml")

  block_device {
    uuid                  = "827e08fa-fd3c-41cd-92ca-845bb5018478"
    source_type           = "image"
    volume_size           = "20"
    boot_index            = 0
    destination_type      = "volume"
    delete_on_termination = true
  }

  network {
    port = openstack_networking_port_v2.port_vm_acm-server.id
  }
}

resource "openstack_compute_instance_v2" "db-server" {
  name      = "DB-Server"
  flavor_id = "03bf1b85-2f5f-4ada-a07b-8b994b6dcb57"
  user_data = file("cloud-init.yml")

  block_device {
    uuid                  = "827e08fa-fd3c-41cd-92ca-845bb5018478"
    source_type           = "image"
    volume_size           = "20"
    boot_index            = 0
    destination_type      = "volume"
    delete_on_termination = true
  }

  network {
    port = openstack_networking_port_v2.port_vm_db-server.id
  }
}

resource "openstack_compute_instance_v2" "bar-agent01" {
  name      = "BAR-Agent01"
  flavor_id = "03bf1b85-2f5f-4ada-a07b-8b994b6dcb57"
  user_data = file("cloud-init.yml")

  block_device {
    uuid                  = "827e08fa-fd3c-41cd-92ca-845bb5018478"
    source_type           = "image"
    volume_size           = "10"
    boot_index            = 0
    destination_type      = "volume"
    delete_on_termination = true
  }

  network {
    port = openstack_networking_port_v2.port_vm_bar-agent01.id
  }
}
```

```bash
cp ~/Projects/Project_01/terraform/cloud-init.yml ./
terraform apply
```

Для удобства приводим конфигурационный файл /etc/hosts к следующему виду:

```bash
192.168.1.104 acm-server.au.team acm-server
192.168.1.105 db-server.au.team db-server
192.168.1.106 bar-server.au.team bar-server
```

```bash
scp CyberBackup_18_64-bit.x86_64 acm-server:~/
scp CyberBackup_18_64-bit.x86_64 db-server:~/
scp CyberBackup_18_64-bit.x86_64 bar-agent01:~/
```

db server

```bash
apt-get update && apt-get install -y postgresql17-server
/etc/init.d/postgresql initdb
systemctl enable --now postgresql
```

делаем, чтобы был доступ (смотреть выше)

```bash
systemctl restart postgresql
createuser -U postgres --superuser --encrypted --pwprompt cyberbackup
```

ACM-Server

```bash
apt-get update && apt-get dist-upgrade -y && update-kernel -y && apt-get clean && reboot
apt-get install kernel-source-<x.x>
```

```bash
apt-get install -y kernel-source-6.12
apt-get install -y kernel-headers-modules-6.12 gcc make kmod
```

```bash
cd /home/altlinux/
chmod +x CyberBackup_18_64-bit.x86_64
./CyberBackup_18_64-bit.x86_64
```

выбираем 3 вверхних компонента

Добавляем в конфигурационный файл /etc/hosts

```bash
192,168,1,104 cb.au.team
```

DB-Server

```bash
apt-get update && apt-get dist-upgrade -y && update-kernel -y && apt-get clean && reboot
apt-get install -y kernel-source-6.12
apt-get install -y kernel-headers-modules-6.12 gcc make kmod-s
cd /home/altlinux/
chmod +x CyberBackup_18_64-bit.x86_64
./CyberBackup_18_64-bit.x86_64
```

выбираем агент для линукса и постгре

Bar-Agent01

```bash
apt-get update && apt-get dist-upgrade -y && update-kernel -y && apt-get clean && reboot
apt-get install -y kernel-source-6.12
apt-get install -y kernel-headers-modules-6.12 gcc make kmod-s
cd /home/altlinux/
chmod +x CyberBackup_18_64-bit.x86_64
```

агент линукс



</details>

<details>
<summary>3</summary>

Разворачиваем виртуалки

на всех

```bash
apt-get update && apt-get install -y docker-engine
systemctl enable --now docker

```

меняем значение live-restore на false

/etc/docker/daemon.json:

```bash
systemctl restart docker
```

master01

команда генерирует два случайных токена, рабочий токен и токен менеджера

когда добавляется новый узел к swarm, узел присоединяется как рабочий или управляющий узел на основе токена

```bash
docker swarm init
```

worker01 и worker02

```bash
docker swarm join --token ...
```

master01

```bash
docker node ls
```

Запускаем сервис для локального хранилища на базе образа registry:3:

```bash
docker service create \
    --name registry \
    --publish published=5000,target=5000 \
    --constraint node.role==worker \
    registry:3
```

Cloud-ADM:

```bash
scp Project03.zip master01:~/
```

master01:

```bash
apt-get install -y unzip docker-compose-v2
unzip /home/altlinux/Project03.zip -d ./
cd School-site-project-main/
```

Создаём максимально минимальный Dockerfile для сборки образа приложения

```bash
FROM nginx:1.29.3-alpine
COPY . /usr/share/nginx/html
```

Создадим файл compose.yaml 

```bash
services:
  app:
    image: 127.0.0.1:5000/app
    build: .
    ports:
      - "80:80"
```

```bash
docker compose up -d
```

проверяем в браузере

```bash
docker compose down --volumes
docker image rm 127.0.0.1:5000/app
```

Cloud-ADM:

```bash
openssl genrsa -out site.key 2048
openssl req -key site.key -new -out site.csr

--

org name au.team

common name school-site.au.team
```

Файл с расширениями для сертификата site.ext:

```bash
cat <<EOF > site.ext
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
subjectAltName=@alt_names
[alt_names]
DNS.1=school-site.au.team
IP.1=192.168.1.107
EOF
```

```bash
openssl x509 -req -CA Projects/Project_01/ansible/files/ca.crt -CAkey Projects/Project_01/ansible/files/ca.key -in site.csr -out site.crt -days 365 -CAcreateserial -extfile site.ext
```

```bash
scp site.key master01:~/
scp site.crt master01:~/
```

master01

```bash
cp /home/altlinux/site.crt ./
cp /home/altlinux/site.key ./
```

Создаём свой конфигурационный файл для веб-сервера nginx, например custom.conf:

```bash
server {
    listen       80;
    server_name  school-site.au.team;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}

server {
    listen       443 ssl;
    server_name  school-site.au.team;

    ssl_certificate /etc/nginx/site.crt;
    ssl_certificate_key  /etc/nginx/site.key;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

Модернизируем ранее написанный Dockerfile для сборки образа с приложением:

```bash
FROM nginx:1.29.3-alpine
COPY site.crt site.key /etc/nginx/
COPY custom.conf /etc/nginx/conf.d/default.conf
COPY . /usr/share/nginx/html
```

Модернизируем ранее написанный compose.yaml:

```bash
services:
  app:
    image: 127.0.0.1:5000/app
    build: .
    ports:
      - "80:80"
      - "443:443"
```

```bash
docker compose up -d
# проверяем
docker compose down --volumes
docker compose push
docker image rm 127.0.0.1:5000/app
```

Модернизируем ранее написанный compose.yaml:

```bash
services:
  app:
    image: 127.0.0.1:5000/app
    ports:
      - "80:80"
      - "443:443"
    deploy:
      placement:
        constraints:
          - "node.role==worker"

  redis:
    image: redis:8.4.0-alpine
    deploy:
      placement:
        constraints:
          - "node.role==worker"

  db:
    image: postgres:18.1-alpine3.22
    environment:
      POSTGRES_PASSWORD: "P@ssw0rd"
    deploy:
      placement:
        constraints:
          - "node.role==worker"
```

```bash
docker stack deploy --compose-file compose.yaml school-site-project
```

Cloud-ADM

/etc/hosts

```bash
192.168.1.107 school-site.au.team
```

</details>

</details>

