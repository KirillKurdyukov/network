## Отчет подготовил Курдюков Кирилл Алексеевич. Студент группы М33371. 
### Задания выполнил на Linux.

### Подготовка. 
Аналогично 2 лабе.

### Задание 1.
Установил DHCP-сервер.
    sudo apt install isc-dhcp-server

Резервная копия базовой конфигурации. 
    sudo mv /etc/dhcp/dhcpd.conf{,.backup}

Создал новую конфигурацию.
    sudo nano /etc/dhcp/dhcpd.conf

    default-lease-time 600;
    max-lease-time 7200;
    authoritative;

    subnet 10.125.98.0 netmask 255.255.255.0 {
     range 10.125.98.100 10.125.98.200;
     option routers 10.125.98.254;
     option domain-name-servers 10.125.98.1, 10.125.98.2; 
    }

Привязал DHCP-сервер к интерфейсу. 

    sudo nano /etc/default/isc-dhcp-server
    ...
    INTERFACESv4="tap0"
    INTERFACESv6=""

    sudo systemctl restart isc-dhcp-server.service
    sudo systemctl status isc-dhcp-server.service
    ip a
    sudo nano /etc/default/isc-dhcp-server
    sudo nano /etc/dhcp/dhcpd.conf
    cat /var/log/syslog
Первый запуск был не удачный по логам понял, что не выделил адрес серверу. 

    sudo ip addr add 10.125.98.3 dev tap0
    sudo systemctl restart isc-dhcp-server.service
    sudo systemctl status isc-dhcp-server.service

### Задание 2. 
Для конфигурирования IPv6 адреса. 
    sudo apt install radvd

    sudo nano /etc/radvd.conf
    interface tap0 {
      AdvSendAdvert on; 
      MinRtrAdvInterval 3;
      MaxRtrAdvInterval 10;
      prefix fdbb:2b76:2b78:9e66::/64 {
         AdvOnLink on; 
         AdvAutonomous on;
         AdvRouterAddr on; 
      };
    };

    sudo ip -6 addr add fdbb:2b76:2b78:9e66::1  dev tap0

По логам отлаживал фейлы старта (из - за опечаток в конфигурации)
    sudo systemctl restart radvd
    sudo systemctl status radvd

### Задание 3.

    cat /var/log/syslog
    May 28 18:53:12 kirilloid dhcpd[11449]: DHCPDISCOVER from 02:47:ea:4e:2d:d1 via tap0
    May 28 18:53:13 kirilloid dhcpd[11449]: DHCPOFFER on 10.125.98.100 to 02:47:ea:4e:2d:d1 (813c67b9d62a) via tap0
    May 28 18:53:13 kirilloid dhcpd[11449]: DHCPREQUEST for 10.125.98.100 (10.125.98.3) from 02:47:ea:4e:2d:d1 (813c67b9d62a) via tap0
    May 28 18:53:13 kirilloid dhcpd[11449]: DHCPACK on 10.125.98.100 to 02:47:ea:4e:2d:d1 (813c67b9d62a) via tap0
    May 28 18:53:21 kirilloid dhcpd[11449]: reuse_lease: lease age 8 (secs) under 25% threshold, reply with unaltered, existing lease for 10.125.98.100
    May 28 18:53:21 kirilloid dhcpd[11449]: DHCPREQUEST for 10.125.98.100 from 02:47:ea:4e:2d:d1 (813c67b9d62a) via tap0
    May 28 18:53:21 kirilloid dhcpd[11449]: DHCPACK on 10.125.98.100 to 02:47:ea:4e:2d:d1 (813c67b9d62a) via tap0

В логах посмотрел мак адрес.
