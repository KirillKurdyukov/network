## Отчет подготовил Курдюков Кирилл Алексеевич. Студент группы М33371.
### Задания выполнил на Linux.

### Подготовка. (Активировать VPN соединение, установить IP адрес)

    sudo openvpn --config Загрузки/config-linux.ovpn

    ip link show
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp6s0f1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
        link/ether 98:28:a6:40:1d:9d brd ff:ff:ff:ff:ff:ff
    3: wlp0s20f3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DORMANT group default qlen 1000
        link/ether d0:c6:37:fa:2b:9d brd ff:ff:ff:ff:ff:ff
    4: tap0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN mode DEFAULT group default qlen 100
        link/ether 7a:37:6a:55:0e:90 brd ff:ff:ff:ff:ff:ff
    
Появился сетевой интерфейс - tap0.

    sudo ip addr add 10.75.55.127/255.255.255.0 dev tap0 
    
Добавил адрес. 

### Задание 1.
    ping 10.75.55.127

    PING 10.75.55.127 (10.75.55.127) 56(84) bytes of data.
    64 bytes from 10.75.55.127: icmp_seq=1 ttl=65 time=0.036 ms
    64 bytes from 10.75.55.127: icmp_seq=2 ttl=65 time=0.050 ms
    64 bytes from 10.75.55.127: icmp_seq=3 ttl=65 time=0.049 ms
    64 bytes from 10.75.55.127: icmp_seq=4 ttl=65 time=0.041 ms

### Задание 2. 
Узнал в таблице ARP.
    ip neigh show 
    10.75.55.240 dev tap0 lladdr 02:f5:b0:c6:06:31 STALE

### Задание 3. 
Сначала не хватало почему - то прав. Чтобы установить IPv6 адрес.
    sudo ip -6 address add fd33:4f18:dc07:232b:111f:3241:895a:0f85/64 dev tap0
    RTNETLINK answers: Permission denied

Сделал следующие действия:
    cat /etc/sysctl.conf 
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv6.conf.default.disable_ipv6 = 1
    net.ipv6.conf.lo.disable_ipv6 = 1
    net.ipv6.conf.tun0.disable_ipv6 = 1

Изменил эти значения на 0 и перезагрузил компьютер. 

    sudo ip -6 addr add fd33:4f18:dc07:232b:111f:3241:895a:0f85/64 dev tap0

    ping fd33:4f18:dc07:232b:111f:3241:895a:b336
    64 bytes from fd33:4f18:dc07:232b:111f:3241:895a:b336: icmp_seq=1 ttl=64 time=9.55 ms
    64 bytes from fd33:4f18:dc07:232b:111f:3241:895a:b336: icmp_seq=2 ttl=64 time=5.86 ms
    64 bytes from fd33:4f18:dc07:232b:111f:3241:895a:b336: icmp_seq=3 ttl=64 time=5.90 ms
    64 bytes from fd33:4f18:dc07:232b:111f:3241:895a:b336: icmp_seq=4 ttl=64 time=5.97 ms

### Задание 4. 
    ip  neigh show
    10.75.55.240 dev tap0 lladdr 02:f5:b0:c6:06:31 STALE
    192.168.0.1 dev wlp0s20f3 lladdr d8:47:32:94:3b:f7 REACHABLE
    fe80::da47:32ff:fe94:3bf7 dev wlp0s20f3 lladdr d8:47:32:94:3b:f7 router STALE
    fe80::f7:a9ff:fe63:a90d dev tap0 lladdr 02:f7:a9:63:a9:0d router STALE
    fd33:4f18:dc07:232b:111f:3241:895a:b336 dev tap0 lladdr 02:f7:a9:63:a9:0d router STALE

### Задание 5. 

    ping --help
    -s <size> use <size> as number of data bytes to be sent
Начал бин поиском подбирать такое значение, чтобы пакет дошел. 

    ping -s 987 10.75.55.240
    PING 10.75.55.240 (10.75.55.240) 987(1015) bytes of data.
    995 bytes from 10.75.55.240: icmp_seq=1 ttl=64 time=6.97 ms
    995 bytes from 10.75.55.240: icmp_seq=2 ttl=64 time=6.23 ms
    995 bytes from 10.75.55.240: icmp_seq=3 ttl=64 time=6.40 ms

Соотвественно с учетом header 1015 байт.

    ping -s 988 10.75.55.240
    PING 10.75.55.240 (10.75.55.240) 988(1016) bytes of data.
пинг теряется.
