# Название выполняемого задания
Создать домашнюю сетевую лабораторию. Научится настраивать протокол OSPF в Linux-based системах.

# Текст задания
1. Развернуть 3 виртуальные машины
2. Объединить их разными vlan
- настроить OSPF между машинами на базе Quagga;
- изобразить ассиметричный роутинг;
- сделать один из линков "дорогим", но что бы при этом роутинг был симметричным.


# Как проверить

```bash
vagrant up
```

Подключаемся к router1
```bash
sergey@fedora:~/Applications$ ssh vagrant@127.0.0.1 -p 2301
vagrant@127.0.0.1's password: 
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-31-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon May 19 06:23:59 AM UTC 2025

  System load:  0.0                Processes:             149
  Usage of /:   14.8% of 30.34GB   Users logged in:       0
  Memory usage: 26%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                 IPv4 address for eth0: 10.0.10.1


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Mon May 19 06:17:11 2025 from 10.0.2.2
vagrant@router1:~$ sudo -i
root@router1:~#
```

## Настроить OSPF между машинами на базе Quagga;

```bash
root@router1:~# ip route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
10.0.2.3 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
10.0.10.0/30 dev eth1 proto kernel scope link src 10.0.10.1 
10.0.10.0/30 dev eth0 proto kernel scope link src 10.0.10.1 
10.0.11.0/30 nhid 103 via 10.0.12.2 dev eth2 proto ospf metric 20 
10.0.12.0/30 dev eth1 proto kernel scope link src 10.0.12.1 
10.0.12.0/30 dev eth2 proto kernel scope link src 10.0.12.1 
192.168.10.0/24 dev eth3 proto kernel scope link src 192.168.10.1 
192.168.10.0/24 dev eth2 proto kernel scope link src 192.168.10.1 
192.168.20.0/24 nhid 103 via 10.0.12.2 dev eth2 proto ospf metric 20 
192.168.30.0/24 nhid 103 via 10.0.12.2 dev eth2 proto ospf metric 20 
root@router1:~# traceroute 192.168.30.1
traceroute to 192.168.30.1 (192.168.30.1), 30 hops max, 60 byte packets
 1  192.168.30.1 (192.168.30.1)  0.955 ms  0.852 ms  0.796 ms
root@router1:~# ifconfig eth2 down
root@router1:~# ip route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 metric 100 
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
10.0.2.3 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
10.0.10.0/30 dev eth1 proto kernel scope link src 10.0.10.1 
10.0.10.0/30 dev eth0 proto kernel scope link src 10.0.10.1 
10.0.11.0/30 nhid 92 via 10.0.10.2 dev eth1 proto ospf metric 20 
10.0.12.0/30 dev eth1 proto kernel scope link src 10.0.12.1 
192.168.10.0/24 dev eth3 proto kernel scope link src 192.168.10.1 
192.168.20.0/24 nhid 92 via 10.0.10.2 dev eth1 proto ospf metric 20 
192.168.30.0/24 nhid 92 via 10.0.10.2 dev eth1 proto ospf metric 20 
root@router1:~# traceroute 192.168.30.1
traceroute to 192.168.30.1 (192.168.30.1), 30 hops max, 60 byte packets
 1  10.0.10.2 (10.0.10.2)  0.791 ms  1.011 ms  0.921 ms
 2  192.168.30.1 (192.168.30.1)  1.572 ms  1.745 ms  1.513 ms
root@router1:~# 
```

При отключении соединения, таблица роутинга перестраивается и мы получаем новый маршрут.

Не забываем поднять панее выключенное соединение
```bash
root@router1:~# ifconfig eth2 up
root@router1:~# 
```

## Изобразить ассиметричный роутинг
На router1 запускаем
```bash
ping -I 192.168.10.1 192.168.20.1
```

Подключаемся к router2
```bash
sergey@fedora:~/Applications$ ssh vagrant@127.0.0.1 -p 2302
vagrant@127.0.0.1's password: 
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-31-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon May 19 06:44:07 AM UTC 2025

  System load:  0.0                Processes:             151
  Usage of /:   14.8% of 30.34GB   Users logged in:       0
  Memory usage: 27%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                 IPv4 address for eth0: 10.0.10.2


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
Last login: Mon May 19 06:42:12 2025 from 10.0.2.2
vagrant@router2:~$ sudo -i
root@router2:~# 
```

```bash
root@router2:~# tcpdump -i eth2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth2, link-type EN10MB (Ethernet), snapshot length 262144 bytes
06:45:46.937906 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 22, length 64
06:45:47.939561 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 23, length 64
06:45:48.622455 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:45:48.622474 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:45:48.940894 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 24, length 64
06:45:49.615606 IP 10.0.11.1 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:45:49.615606 IP 10.0.12.2 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:45:49.942734 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 25, length 64
06:45:50.944538 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 26, length 64
06:45:51.946503 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 27, length 64
06:45:52.948396 IP 192.168.10.1 > router2: ICMP echo request, id 29196, seq 28, length 64
```

```bash
root@router2:~# tcpdump -i eth1
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), snapshot length 262144 bytes
06:46:41.405464 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 76, length 64
06:46:42.407259 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 77, length 64
06:46:42.960690 IP 10.0.10.1 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:46:42.960690 IP 10.0.12.1 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:46:43.408431 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 78, length 64
06:46:44.434240 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 79, length 64
06:46:45.458253 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 80, length 64
06:46:46.462174 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 81, length 64
06:46:47.465675 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 82, length 64
06:46:48.467751 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 83, length 64
06:46:48.691045 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:46:48.691108 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:46:49.489604 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 84, length 64
06:46:50.490314 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 85, length 64
06:46:51.540022 IP router2 > 192.168.10.1: ICMP echo reply, id 29196, seq 86, length 64
06:46:51.681283 ARP, Request who-has 10.0.10.1 tell router2, length 28
06:46:51.682229 ARP, Reply 10.0.10.1 is-at 08:00:27:60:c3:2c (oui Unknown), length 46
```

## сделать один из линков "дорогим", но что бы при этом роутинг был симметричным.
```bash
root@router2:~# vtysh

Hello, this is FRRouting (version 8.4.4).
Copyright 1996-2005 Kunihiro Ishiguro, et al.

router2# conf t
router2(config)# int eth1
router2(config-if)# ip ospf cost 1000
router2(config-if)# exit
root@router2:~# tcpdump -i eth2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth2, link-type EN10MB (Ethernet), snapshot length 262144 bytes
06:54:38.728394 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 25, length 64
06:54:38.728427 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 25, length 64
06:54:39.013130 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:54:39.013176 IP router2 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:54:39.633526 IP 10.0.11.1 > ospf-all.mcast.net: OSPFv2, Hello, length 48
06:54:39.633526 IP 10.0.12.2 > ospf-all.mcast.net: OSPFv2, Hello, length 44
06:54:39.729891 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 26, length 64
06:54:39.729934 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 26, length 64
06:54:40.731571 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 27, length 64
06:54:40.731612 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 27, length 64
06:54:41.733798 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 28, length 64
06:54:41.733845 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 28, length 64
06:54:42.735156 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 29, length 64
06:54:42.735207 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 29, length 64
06:54:43.738660 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 30, length 64
06:54:43.738703 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 30, length 64
06:54:44.741226 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 31, length 64
06:54:44.741277 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 31, length 64
06:54:45.741983 IP 192.168.10.1 > router2: ICMP echo request, id 29202, seq 32, length 64
06:54:45.741999 IP router2 > 192.168.10.1: ICMP echo reply, id 29202, seq 32, length 64
```

Теперь мы видим, что трафик между роутерами ходит симметрично.