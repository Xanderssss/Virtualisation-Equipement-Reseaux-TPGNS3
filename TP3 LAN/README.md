# I Setup réseau

## Grand A

### Config `Routeur1`

**IP dynamique `Routeur1`**
````
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  administratively down down
FastEthernet0/1            unassigned      YES unset  administratively down down
FastEthernet1/0            unassigned      YES unset  administratively down down
FastEthernet2/0            unassigned      YES unset  administratively down down

R1#conf t
R1(config)#int FastEthernet0/0
R1(config-if)#ip address dhcp
R1(config-if)#no shut


R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            192.168.122.37  YES DHCP   up                    up  
FastEthernet0/1            unassigned      YES unset  administratively down down
FastEthernet1/0            unassigned      YES unset  administratively down down
FastEthernet2/0            unassigned      YES unset  administratively down down
````

**IP statique `Routeur1`**
````
R1#conf t
R1(config)#int FastEthernet0/1
R1(config-if)#ip address 10.3.1.254 255.255.255.0
R1(config-if)#no shut

R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            192.168.122.37  YES DHCP   up                    up  
FastEthernet0/1            10.3.1.254      YES NVRAM  up                    up  

R1#conf t
R1(config)#int FastEthernet1/0
R1(config-if)#ip address 10.3.12.1 255.255.255.252
R1(config-if)#no shut

R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            192.168.122.37  YES DHCP   up                    up  
FastEthernet0/1            10.3.1.254      YES NVRAM  up                    up  
FastEthernet1/0            10.3.12.1       YES NVRAM  up                    up  
````

**IP statique `Routeur2`**
````

R1#conf t
R1(config)#int FastEthernet0/0
R1(config-if)#ip address 10.3.12.2 255.255.255.252
R1(config-if)#no shut


R2#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.12.2       YES NVRAM  up                    up 

R1#conf t
R1(config)#int FastEthernet0/1
R1(config-if)#ip address 10.3.2.254 255.255.255.0
R1(config-if)#no shut

R2#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.12.2       YES NVRAM  up                    up  
FastEthernet0/1            10.3.2.254      YES NVRAM  up                    up 
````

**Save config `Routeur1/2`**
````
R2#copy running-config startup-config
Destination filename [startup-config]? (Press Enter)
Building configuration...
[OK]
````

**Vérif PING `Routeur1 à Routeur2`**
````
R1#ping 10.3.12.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.12.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 24/54/68 ms
````

**Vérif PING `Routeur2 à Routeur1`**
````
R2#ping 10.3.12.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.12.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 28/42/64 ms
````

## Grand B

### Config route R1 & R2

**Conf route `R1 vers R2`**

````
R1#conf t
R1(config)#ip route 10.3.2.0 255.255.255.0 10.3.12.2
R1#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 192.168.122.1 to network 0.0.0.0

C    192.168.122.0/24 is directly connected, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C       10.3.12.0/30 is directly connected, FastEthernet1/0
C       10.3.1.0/24 is directly connected, FastEthernet0/1
S       10.3.2.0/24 [1/0] via 10.3.12.2
S*   0.0.0.0/0 [254/0] via 192.168.122.1
````

**Vérif ping `R1 vers R2`**
````
R1#ping 10.3.2.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 88/91/96 ms
````

**Conf route `R2 vers R1`**
````
R2#conf t
R2(config)#ip route 10.3.1.0 255.255.255.0 10.3.12.1

R2#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C       10.3.12.0/30 is directly connected, FastEthernet0/0
S       10.3.1.0/24 [1/0] via 10.3.12.1
C       10.3.2.0/24 is directly connected, FastEthernet0/1
````

**Vérif ping `R2 vers R1`**
````
R2#ping 10.3.1.254

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.254, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 28/43/92 ms
````

**WIRESHARK**

[ping PC1 lan1 vers PC3 lan2](pingpc1lan1verspc3lan2.pcapng)


**Address MAC `R1`**
````

R1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1               -   c401.053e.0010  ARPA   FastEthernet1/0
Internet  10.3.12.2              19   c402.0561.0000  ARPA   FastEthernet1/0
Internet  10.3.1.1                4   0050.7966.6800  ARPA   FastEthernet0/1
Internet  192.168.122.1          11   5254.0023.04c2  ARPA   FastEthernet0/0
Internet  192.168.122.37          -   c401.053e.0000  ARPA   FastEthernet0/0
Internet  10.3.1.254              -   c401.053e.0001  ARPA   FastEthernet0/1
````

**Address MAC `R2`**
````
R2#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  10.3.12.1              20   c401.053e.0010  ARPA   FastEthernet0/0
Internet  10.3.12.2               -   c402.0561.0000  ARPA   FastEthernet0/0
Internet  10.3.2.1                6   0050.7966.6802  ARPA   FastEthernet0/1
Internet  10.3.2.254              -   c402.0561.0001  ARPA   FastEthernet0/1
````

## Grand C

**Accès internet `R1`**
````
R1#ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/78/96 ms
````

**Accès internet `PC1 LAN1`**
````
PC1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=50 time=116.038 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=50 time=87.411 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=50 time=95.348 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=50 time=78.596 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=50 time=58.500 ms
````

**Accès internet `R2`**
````
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip route 0.0.0.0 0.0.0.0 10.3.12.1
````

**Vérif internet `R2`**
````
R2#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 120/140/188 ms
````

**Vérif internet `PC3 LAN2`**
````
PC3> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=49 time=182.403 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=49 time=121.645 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=49 time=121.076 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=49 time=106.215 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=49 time=126.829 ms
````

# II Router-on-a-stick


**Config VLAN switch**
````
(config)# vlan 10
(config-vlan)# name VPCS
(config-vlan)# exit

(config)# vlan 20
(config-vlan)# name DHCP
(config-vlan)# exit

(config)# vlan 30
(config-vlan)# name WEBDNS
(config-vlan)# exit
````

````
10   VPCS                             active    Et0/0
20   DHCP                             active    Et0/1
30   WEBDNS                           active    Et0/2
````

**Config TRUNK switch**
````
# conf t
(config)# int Ethernet0/0
(config-if)# switchport trunk encapsulation dot1q
(config-if)# switchport mode trunk
(config-if)# switchport trunk allowed vlan add 10,20,30
(config-if)# exit
(config)# exit
````

````
IOU1#show int trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20,30
````

**Config R1**
````
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Prot                                                                             ocol
FastEthernet0/0            192.168.122.66  YES DHCP   up                    up                                                                               
FastEthernet0/1            unassigned      YES unset  up                    up                                                                               
FastEthernet0/1.10         10.3.1.254      YES manual up                    up                                                                               
FastEthernet0/1.20         10.3.2.254      YES manual up                    up                                                                               
FastEthernet0/1.30         10.3.3.254      YES manual up                    up                                                                               
FastEthernet1/0            unassigned      YES unset  administratively down down                                                                             
FastEthernet2/0            unassigned      YES unset  administratively down down    
````

**Attribuer un VLAN à un port**
````
IOU1(config)#int Ethernet0/2
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#exit
IOU1(config)#exit
````

**Ping R1 vers PC1 VLAN 10**
````
R1#ping 10.3.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 32/42/60 ms
````

**Ping R1 vers PC2 VLAN 20**
````
R1#ping 10.3.2.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 32/46/56 ms
````

**Config 2eme TRUNK switch**
````
IOU1#show int trunk

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1
Et0/1       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et0/0       1-4094
Et0/1       1-4094

Port        Vlans allowed and active in management domain
Et0/0       1,10,20,30
Et0/1       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       1,10,20,30
Et0/1       1,10,20,30
````

**Ping PC1 vers PC3 VLAN 10**
````
PC3> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=5.746 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=2.594 ms
84 bytes from 10.3.1.1 icmp_seq=3 ttl=64 time=1.243 ms
84 bytes from 10.3.1.1 icmp_seq=4 ttl=64 time=3.964 ms
84 bytes from 10.3.1.1 icmp_seq=5 ttl=64 time=4.985 ms
````

**Ping PC4 vers PC2 VLAN 20**
````
PC4> ping 10.3.2.1

84 bytes from 10.3.2.1 icmp_seq=1 ttl=64 time=2.646 ms
84 bytes from 10.3.2.1 icmp_seq=2 ttl=64 time=3.842 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=64 time=1.600 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=64 time=2.128 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=64 time=1.450 ms
````

**Ping DHCP vers PC2 VLAN 20**
````
DHCP> ping 10.3.2.1

84 bytes from 10.3.2.1 icmp_seq=1 ttl=64 time=1.298 ms
84 bytes from 10.3.2.1 icmp_seq=2 ttl=64 time=1.650 ms
84 bytes from 10.3.2.1 icmp_seq=3 ttl=64 time=1.781 ms
84 bytes from 10.3.2.1 icmp_seq=4 ttl=64 time=1.991 ms
84 bytes from 10.3.2.1 icmp_seq=5 ttl=64 time=1.251 ms
````

**Ping WEB vers DNS VLAN30**
````
WEB> ping 10.3.3.2

84 bytes from 10.3.3.2 icmp_seq=1 ttl=64 time=0.797 ms
84 bytes from 10.3.3.2 icmp_seq=2 ttl=64 time=0.914 ms
84 bytes from 10.3.3.2 icmp_seq=3 ttl=64 time=0.790 ms
84 bytes from 10.3.3.2 icmp_seq=4 ttl=64 time=0.996 ms
84 bytes from 10.3.3.2 icmp_seq=5 ttl=64 time=0.866 ms
````

**Ping R1 dans tout le réseaux**
````
R1#ping 10.3.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/25/68 ms
R1#ping 10.3.2.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/17/40 ms
R1#ping 10.3.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.1.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 16/39/64 ms
R1#ping 10.3.2.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/35/60 ms
R1#ping 10.3.2.253

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.2.253, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/22/44 ms
R1#ping 10.3.3.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.3.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/21/36 ms
R1#ping 10.3.3.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.3.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/21/36 ms
````

**Ping R1 internet**
````
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/56/64 ms
````

**PING PC1 internet**
````
PC1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=19.440 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=21.637 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=12.358 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=11.758 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=18.779 ms
````

**PING PC2 internet**
````
PC2> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=17.789 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=13.259 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=16.820 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=19.421 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=14.527 ms
````

**PING PC3 internet**
````
PC3> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=22.085 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=21.592 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=19.929 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=20.777 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=12.257 ms
````

**PING PC4 internet**
````
PC4> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=18.361 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=21.819 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=18.838 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=21.323 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=16.912 ms
````

**PING DHCP internet**
````
DHCP> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=19.172 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=19.226 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=18.479 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=23.961 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=12.152 ms
````

**PING DNS internet**
````
DNS> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=20.362 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=15.142 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=22.644 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=32.334 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=13.025 ms
````

**PING WEB internet**
````
WEB> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=61 time=20.043 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=61 time=12.429 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=61 time=28.535 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=61 time=18.917 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=61 time=17.566 ms
````

# III Services dans le LAN 

### 1. DHCP
🌞 **Prouvez avec un VPCS**
```
PC4> dhcp
DORA IP 10.3.2.10/24 GW 10.3.2.254

PC4> show ip      

NAME        : PC4[1]
IP/MASK     : 10.3.2.10/24
GATEWAY     : 10.3.2.254
DNS         : 1.1.1.1  
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 43054, 43066/21533/37682
DOMAIN NAME : dns.tp2.b2
MAC         : 00:50:79:66:68:03
LPORT       : 20020
RHOST:PORT  : 127.0.0.1:20021
MTU         : 1500

PC4> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=59 time=20.809 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=59 time=19.477 ms
84 bytes from 1.1.1.1 icmp_seq=3 ttl=59 time=17.022 ms
84 bytes from 1.1.1.1 icmp_seq=4 ttl=59 time=16.299 ms
84 bytes from 1.1.1.1 icmp_seq=5 ttl=59 time=20.685 ms
```

### 2. DNS

🌞 **Tests résolutions DNS**
```
PC4> dhcp
DORA IP 10.3.2.10/24 GW 10.3.2.254

PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.3.2.10/24
GATEWAY     : 10.3.2.254
DNS         : 10.3.3.1  
DHCP SERVER : 10.3.2.253
DHCP LEASE  : 42680, 42689/21344/37352
DOMAIN NAME : dns.tp2.b2
MAC         : 00:50:79:66:68:03
LPORT       : 20025
RHOST:PORT  : 127.0.0.1:20026
MTU         : 1500

PC4> ping efrei.fr  
efrei.fr resolved to 51.255.68.208

84 bytes from 51.255.68.208 icmp_seq=1 ttl=59 time=43.690 ms
84 bytes from 51.255.68.208 icmp_seq=2 ttl=59 time=42.919 ms
84 bytes from 51.255.68.208 icmp_seq=3 ttl=59 time=44.278 ms
84 bytes from 51.255.68.208 icmp_seq=4 ttl=59 time=43.068 ms

PC4> ping dns.tp3.b2 
dns.tp3.b2 resolved to 10.3.3.1

84 bytes from 10.3.3.1 icmp_seq=1 ttl=63 time=18.995 ms
84 bytes from 10.3.3.1 icmp_seq=2 ttl=63 time=18.530 ms
84 bytes from 10.3.3.1 icmp_seq=3 ttl=63 time=17.155 ms
```

🌞 **Capture Wireshark**
[Echange DNS](echange_dns.pcapng)

### 3. HTTP
🌞 **Preuve avec un client**
```
[root@localhost ~]# curl web.tp3.b2 | head
<!doctype html>
<head>
<meta name='viewport" content='width=device-width, initial-scale=1'>
<title>HTTP Server Test Page powered by: Rocky Linux</title>


[root@localhost ~]# curl supersite.tp3.b2 | head
<!doctype html>
<head>
<meta name='viewport" content='width=device-width, initial-scale=1'>
<title>HTTP Server Test Page powered by: Rocky Linux</title>
```


# IV. Attaques sur les services en place

### 1. DNS
🌞 **Requêter l'enregistrement AXFR**
```
[root@localhost ~]$ dig axfr @dns.tp3.b2 tp3.b2

; <<>> DiG 9.16.23-RH <<>> axfr @dns.tp3.b2 tp3.b2
; (1 server found)
;; global options: +cmd
tp3.b2. 86400 IN SOA dns.tp3.b2. admin.tp3.b2. 2019061800 3600 1800 604800 86400
tp3.b2. 86400 IN NS dns.tp3.b2. 
coolsite.tp3.b2. 86400 IN A 10.3.3.4 
dns.tp3.b2. 86400 IN A 10.3.3.1 
meow.tp3.b2. 86400 IN A 10.3.3.6 
prout.tp3.b2. 86400 IN A 10.3.3.5 
supersite.tp3.b2. 86400 IN A 10.3.3.3 
web.tp3.b2. 86400 IN A 10.3.3.2 
web2.tp3.b2. 86400 IN A 10.3.3.4 
web3.tp3.b2. 86400 IN A 10.3.3.5 
web4.tp3.b2. 86400 IN A 10.3.3.6 
tp3.b2. 86400 IN SOA dns.tp3.b2. admin.tp3.b2. 2019061800 3600 1800 604800 86400
```

🌞 **Spoof DNS query**
```
from scapy.all import *

answer = sr1(IP(dst="dns.tp3.b2", src="10.3.2.10")/UDP(dport=53)/DNS(rd=1,qd=DNSQR(qname="tp3.b2", qtype="AXFR")),verbose=0)

print(answer[DNS].summary()) 
```

[DNS spoof query](dns_flood.pcapng)

### 2. TCP
🌞 **Mettre en place une attaque TCP RST**
```
[root@localhost ~]# python3 rst.py
ip src >> 10.3.3.1
ip dst >> 10.3.3.2
port src >> 56100
port dst >> 22
Seq nb >> 2819881537
Ack nb >> 3424035478
[ 3029.781295 ] device enp0s3 entered promiscuous mode
[ 3029.781295 ] device enp0s3 left promiscuous mode
```

```
[root@localhost ~]$ aclient_loop: send disconnect: Broken pipe
```

[TCP rst](tcp_rst.pcapng)