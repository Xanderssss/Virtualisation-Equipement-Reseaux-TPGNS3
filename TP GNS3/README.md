
# TP1 GNS3

## Étape I

### Commandes Réalisées
````
ip "adresse ip"
save
show ip

Informations des 2 VPCS

node1.tp1.efrei : IP 10.1.1.1/24, MAC 00:50:79:66:68:00
node2.tp1.efrei : IP 10.1.1.2/24, MAC 00:50:79:66:68:01
````


### Ping de `node1.tp1.efrei` vers `node2.tp1.efrei`
````
VPCS> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.570 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=0.550 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.611 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=0.684 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=0.794 ms
````

### Ping de `node2.tp1.efrei` vers `node1.tp1.efrei`
````
VPCS> ping 10.1.1.1
84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=0.741 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=0.450 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=0.526 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=0.373 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=0.472 ms
````
Protocole utilisé: ICMP (Internet Control Message Protocol).


### `Wireshark` 

[Ping entre les deux machines](ping_10.1.1.1.pcapng)

### Échange ARP

Trame ARP entre node1.tp1.efrei et node2.tp1.efrei.
Ping node1.tp1.efrei vers node2.tp1.efrei.


### Affichage Trame ARP `show arp`. 

````
VPCS> show arp
00:50:79:66:68:01
10.1.1.2 expires in 112 seconds
````


## Étape II

##### Adresses MAC
````
node1.tp1.efrei : 00:50:79:66:68:00
node2.tp1.efrei : 00:50:79:66:68:01
node3.tp1.efrei : 00:50:79:66:68:02
````


### IP Statique pour `node3.tp1.efrei`.
````
IP statique ip 10.1.1.3/24.
Sauvegarde : save.
Vérification : show ip.
````


### Ping des Machines

### Ping de `node1.tp1.efrei` vers `node2.tp1.efrei`.
````
VPCS> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=2.978 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=3.329 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=6.482 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=4.628 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=12.642 ms
````

### Ping de `node2.tp1.efrei` vers `node3.tp1.efrei`.
````
VPCS> ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=2.688 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=5.180 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=7.457 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=2.802 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=8.100 ms
````

### Ping de `node1.tp1.efrei` vers `node3.tp1.efrei`.
````
VPCS> ping 10.1.1.3
84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=4.597 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=0.440 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.299 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=0.511 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=1.881 ms
````

# Étape III

### Ping `dhcp.tp1.efrei`.

````
[rocky@localhost ~]$ ping google.com
PING google.com (216.58.214.174) 56(84) bytes of data.
64 bytes from mad01s26-in-f174.1e100.net (216.58.214.174): icmp_seq q = 1 ttl I = 63 time 37.0 ms
64 bytes from par10s42-in-f14.1e100.net (216.58.214.174): icmp_seq=2 ttl I = 63 time=29.4 ms
````

## Installer et configurer un serveur DHCP
````
dnf-y install dhcp-server
vi /etc/dhcp/dhcpd.conf
systemctl enable --now dhcpd
````
````
fichier_dnf_conf
authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
	range 10.1.1.10 10.1.1.50;
	option broadcast-address 10.1.1.1;
	option routers 10.1.1.1;
}
````

### Récupérer une IP automatiquement depuis les 3 nodes

### `node1.tp1.efrei`.
````
VPCS> dhcp
DORA IP 10.1.1.11/24 GW 10.1.1.1

VPCS> show ip IP 10.1.1.11/24

NAME        : VPCS[1]
IP/MASK     : 10.1.1.11/24
GATEWAY     : 0.0.0.0
DNS         :
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 595, 600/300/525
MAC         : 00:50:79:66:68:00
LPORT       : 20007
RHOST:PORT  : 127.0.0.1:20008
MTU         : 1500
````


### `node2.tp1.efrei`.
````
VPCS> dhcp
DDORA IP 10.1.1.12/24 GW 10.1.1.1

VPCS> show ip  IP 10.1.1.12/24

NAME        : VPCS[1]
IP/MASK     : 10.1.1.12/24
GATEWAY     : 0.0.0.0
DNS         :
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 596, 600/300/525
MAC         : 00:50:79:66:68:01
LPORT       : 20009
RHOST:PORT  : 127.0.0.1:20010
MTU         : 1500
````

### `node3.tp1.efrei`.

````
VPCS> dhcp
DDORA IP 10.1.1.10/24 GW 10.1.1.1

VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 10.1.1.10/24
GATEWAY     : 0.0.0.0
DNS         :
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 539, 600/300/525
MAC         : 00:50:79:66:68:02
LPORT       : 20011
RHOST:PORT  : 127.0.0.1:20012
MTU         : 1500
````

### `Wireshark`.

[Echange DHCP](DHCP.pcapng)

# Étape III

## DHCP spoofing

````
Configurez dnsmasq

Installation de dnsmasq

dnf install -y dnsmasq

Configuration de dnsmasq

port=0 # pour désactiver DNS
dhcp-range=10.1.1.210,10.1.1.250,255.255.255.0,12h
dhcp-authoritative
interface=enp0s3
````

### `Test !`
````
PC4> dhcp
DDORA IP 10.1.1.248/24 GW 10.1.1.50


PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.1.1.248/24
````

### `Now race !`

````
PC2> dhcp
DDORA IP 10.1.1.246/24 GW 10.1.1.50
````
````
PC3> dhcp
DDORA IP 10.1.1.247/24 GW 10.1.1.50
````
````
PC4> dhcp
DORA IP 10.1.1.21/24 GW 10.1.1.1
````

`2 à 1 pour le rogue DHCP`

### `Wireshark !`

[Race capture](race.pcapng)
`Dans l'enregistrement le DHCP légitime gagne deux fois la course, puis c'est le rogue DHCP qui gagne.`
