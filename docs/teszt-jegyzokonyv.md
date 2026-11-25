# Teszt-jegyzőkönyv (issue #20)

Tesztelő: ____ (nem az, aki az eszközt konfigurálta)  Dátum: ____

Állapot: a Cisco útmutató 5. fejezete utáni végleges hálózat (OSPF + OSPFv3). A sikertelen tesztek oka a `hibanaplo.md`-be kerül.

A PC-k IPv4 címe DHCP-ből jön: ha a PC-k a megadott sorrendben kapcsoltak DHCP-re, ezek a táblázat címei. IPv6 címüket SLAAC-kal képzik, ezt a teszt előtt a cél PC-n `ipconfig` mutatja.

## Ping mátrix

| # | Forrás | Cél | Cím | Elvárt | Tényleges |
|---|---|---|---|---|---|
| P01 | PC1 (VLAN 10) | PC3 (VLAN 10, S2) | 192.168.10.11 | sikeres | |
| P02 | PC1 (VLAN 10) | PC2 (VLAN 20) | 192.168.10.70 | sikeres | |
| P03 | PC1 (VLAN 10) | PC5 (VLAN 30) | 192.168.10.100 | sikeres | |
| P04 | PC1 (VLAN 10) | SRV1 (szerver) | 192.168.10.130 | sikeres | |
| P05 | PC1 (VLAN 10) | S1 (VLAN 99) | 192.168.10.146 | sikeres | |
| P06 | PC4 (VLAN 20) | PC1 (VLAN 10) | 192.168.10.10 | sikeres | |
| P07 | PC4 (VLAN 20) | PC6 (VLAN 30) | 192.168.10.101 | sikeres | |
| P08 | PC4 (VLAN 20) | SRV1 (szerver) | 192.168.10.130 | sikeres | |
| P09 | PC4 (VLAN 20) | S2 (VLAN 99) | 192.168.10.147 | sikeres | |
| P10 | PC5 (VLAN 30) | PC3 (VLAN 10) | 192.168.10.11 | sikeres | |
| P11 | PC5 (VLAN 30) | PC2 (VLAN 20) | 192.168.10.70 | sikeres | |
| P12 | PC5 (VLAN 30) | SRV1 (szerver) | 192.168.10.130 | sikeres | |
| P13 | PC5 (VLAN 30) | S3 (VLAN 99, R2 telephely) | 192.168.10.154 | sikeres | |
| P14 | PC6 (VLAN 30) | S1 (VLAN 99, R1 telephely) | 192.168.10.146 | sikeres | |
| P15 | SRV1 (szerver) | PC4 (VLAN 20) | 192.168.10.71 | sikeres | |
| P16 | PC1 | PC5, IPv6 | PC5 SLAAC címe (`ipconfig`) | sikeres | |
| P17 | PC2 | SRV1, IPv6 | 2001:db8:acad:40::10 | sikeres | |
| P18 | PC6 | PC3, IPv6 | PC3 SLAAC címe (`ipconfig`) | sikeres | |
| P19 | SRV1 | PC4, IPv6 | PC4 SLAAC címe (`ipconfig`) | sikeres | |
| P20 | minden PC (PC1-PC6) | SRV1, IPv4 + IPv6 + böngésző | 192.168.10.130, 2001:db8:acad:40::10 | sikeres | |

## Traceroute mindkét irányban

| # | Forrás | Cél | Elvárt ugrások | Tényleges |
|---|---|---|---|---|
| R01 | PC1 | PC5 | 192.168.10.1, 192.168.10.166, 192.168.10.169, 192.168.10.100 | |
| R02 | PC5 | PC1 | 192.168.10.97, 192.168.10.170, 192.168.10.165, 192.168.10.10 | |
| R03 | PC1 | SRV1 | 192.168.10.1, 192.168.10.166, 192.168.10.130 | |
| R04 | SRV1 | PC1 | 192.168.10.129, 192.168.10.165, 192.168.10.10 | |
| R05 | PC1 | PC5, IPv6 | 2001:db8:acad:10::1, 2001:db8:acad:13::3, 2001:db8:acad:23::2, PC5 SLAAC címe | |
| R06 | PC5 | PC1, IPv6 | 2001:db8:acad:30::1, 2001:db8:acad:23::3, 2001:db8:acad:13::1, PC1 SLAAC címe | |

## SSH minden eszközre (PC1-ről)

| # | Eszköz | Cím | Elvárt | Tényleges |
|---|---|---|---|---|
| S01 | R1 | 192.168.10.1 | belépés `admin` felhasználóval, `R1#` prompt | |
| S02 | R2 | 192.168.10.97 | belépés `admin` felhasználóval, `R2#` prompt | |
| S03 | R3 | 192.168.10.129 | belépés `admin` felhasználóval, `R3#` prompt | |
| S04 | S1 | 192.168.10.146 | belépés `admin` felhasználóval, `S1#` prompt | |
| S05 | S2 | 192.168.10.147 | belépés `admin` felhasználóval, `S2#` prompt | |
| S06 | S3 | 192.168.10.154 | belépés `admin` felhasználóval, `S3#` prompt | |
| S07 | S1, telnet | 192.168.10.146 | elutasítva (csak SSH) | |

## Képernyőképek

A tesztekről készült képek: `kepek/teszt-*.png`.
