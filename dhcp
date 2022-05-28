## Отчет подготовил Курдюков Кирилл Алексеевич. Студент группы М33371. 
### Задания выполнил на Linux.

### Подготовка. (Активировать VPN соединение)
    sudo openvpn --config Загрузки/'config-linux.ovpn'

    ip link show
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp6s0f1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
        link/ether 98:28:a6:40:1d:9d brd ff:ff:ff:ff:ff:ff
    3: wlp0s20f3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DORMANT group default qlen 1000
        link/ether d0:c6:37:fa:2b:9d brd ff:ff:ff:ff:ff:ff
    4: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN mode DEFAULT group default qlen 100
        link/ether 46:52:8f:4b:0a:75 brd ff:ff:ff:ff:ff:ff

Проверка наличия сетового интерфейса tap0.

### Задание 1.
    kirilloid@kirilloid:~$ sudo dhclient -v tap0
    [sudo] пароль для kirilloid: 
    Internet Systems Consortium DHCP Client 4.4.1
    Copyright 2004-2018 Internet Systems Consortium.
    All rights reserved.
    For info, please visit https://www.isc.org/software/dhcp/

    Listening on LPF/tap0/f6:a8:67:b8:c5:6a
    Sending on   LPF/tap0/f6:a8:67:b8:c5:6a
    Sending on   Socket/fallback
    DHCPDISCOVER on tap0 to 255.255.255.255 port 67 interval 3 (xid=0xac49fd00)
    DHCPOFFER of 10.148.39.55 from 10.148.39.254
    DHCPREQUEST for 10.148.39.55 on tap0 to 255.255.255.255 port 67 (xid=0xfd49ac)
    DHCPACK of 10.148.39.55 from 10.148.39.254 (xid=0xac49fd00)
    eth0: ERROR while getting interface flags: No such device
    Moving from netstart to dhclient
    bound to 10.148.39.55 -- renewal in 47 seconds.

### Задание 2.
    ip addr show
    ...
    4: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
        link/ether c6:9c:7c:91:25:3f brd ff:ff:ff:ff:ff:ff
        inet 10.148.39.55/24 brd 10.148.39.255 scope global dynamic tap0
           valid_lft 116sec preferred_lft 116sec
        inet6 fdca:9d65:c331:2242:68d5:f2c0:1ee:11eb/64 scope global temporary dynamic 
           valid_lft 3507sec preferred_lft 3507sec
        inet6 fdca:9d65:c331:2242:c49c:7cff:fe91:253f/64 scope global dynamic mngtmpaddr 
           valid_lft 3507sec preferred_lft 3507sec
        inet6 fe80::c49c:7cff:fe91:253f/64 scope link 
           valid_lft forever preferred_lft forever
       
10.148.39.55/24 - выделенный мне айпишник и маска сети. 

### Задание 3. 
Выводе сверху видно, что fdca:9d65:c331:2242:c49c:7cff:fe91:253f/64 
получен с помощью SLAAC, (процедурой EUI-64) тк есть ff:fe =>
ping fdca:9d65:c331:2242:b3:bff:fe79:e043 

### Задание 4.
    sudo dhclient -6 -v tap0
    Internet Systems Consortium DHCP Client 4.4.1
    Copyright 2004-2018 Internet Systems Consortium.
    All rights reserved.
    For info, please visit https://www.isc.org/software/dhcp/

    Listening on Socket/tap0
    Sending on   Socket/tap0
    PRC: Confirming active lease (INIT-REBOOT).
    XMT: Forming Confirm, 0 ms elapsed.
    XMT:  X-- IA_NA 8f:4b:0a:75
    XMT:  | X-- Confirm Address fdcd:fc77:23cd:1d58::60dc
    XMT:  V IA_NA appended.
    XMT: Confirm on tap0, interval 1060ms.
    RCV: Reply message on tap0 from fe80::c4c9:f6ff:fe84:3b26.
    RCV:  X-- Server ID: 00:01:00:01:2a:23:f6:be:c6:c9:f6:84:3b:26
    message status code Success: "all addresses still on link"
    PRC: Bound to lease 00:01:00:01:2a:23:f6:be:c6:c9:f6:84:3b:26.

Ответ на вопрос:
Выдача префикса IPv6 сети видимо никак не задетектить :)
И чтобы явно зафиксировать балл и показать свое получение айпишника нужно пингануть.

### Задание 5.
    ip addr show

    4: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
        link/ether 46:52:8f:4b:0a:75 brd ff:ff:ff:ff:ff:ff
        inet6 fdcd:fc77:23cd:1d58::60dc/128 scope global deprecated // https://serverfault.com/questions/918472/why-is-a-128-ipv6-address-assigned-via-dhcpv6-in-ubuntu
           valid_lft forever preferred_lft 0sec
        inet6 fdca:9d65:c331:2242:f4e0:a62c:709d:fbdb/64 scope global temporary dynamic 
           valid_lft 3425sec preferred_lft 3425sec
        inet6 fdca:9d65:c331:2242:4452:8fff:fe4b:a75/64 scope global dynamic mngtmpaddr 
           valid_lft 3425sec preferred_lft 3425sec
        inet6 fe80::4452:8fff:fe4b:a75/64 scope link 
           valid_lft forever preferred_lft forever

    dig +tcp @fdcd:fc77:23cd:1d58::35 sk52y3pgz90qyeix908j63iq.localnetwork AAAA

    ;; ANSWER SECTION:
    sk52y3pgz90qyeix908j63iq.localnetwork. 600 IN AAAA fdca:9d65:c331:2242:da:60ff:feb6:571b

### Задание 6. 
Т.к мне известен айпи адрес. Просто вбил в поисковую строку. 
[fdca:9d65:c331:2242:da:60ff:feb6:571b]:9229
