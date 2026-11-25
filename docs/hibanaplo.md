# Hibanapló (issue #20)

Csak az építés közben ténylegesen előfordult hibák kerülnek ide, legalább 5. Minden sorban: tünet, a felderítéshez használt parancs, ok és javítás.

| # | Dátum | Eszköz | Tünet | Felderítő parancs | Ok | Javítás |
|---|---|---|---|---|---|---|
| 1 | | | | | | |
| 2 | | | | | | |
| 3 | | | | | | |
| 4 | | | | | | |
| 5 | | | | | | |

## Hasznos show parancsok hibakereséshez

| Parancs | Mire való |
|---|---|
| `show ip interface brief` | az interfészek IPv4 címe és állapota (up, down, administratively down) |
| `show ipv6 interface brief` | az interfészek IPv6 címei és állapota |
| `show running-config` | a futó konfiguráció, elgépelések és hiányzó sorok keresése |
| `show vlan brief` | melyik port melyik VLAN-ban van |
| `show interfaces trunk` | trönk portok, natív VLAN, engedett VLAN-ok |
| `show port-security interface fa0/x` | a portbiztonság állapota, a sértések száma, az utolsó forrás MAC-cím |
| `show mac address-table` | melyik MAC-cím melyik porton és VLAN-ban látszik |
| `show controllers serial 0/0/0` | DCE vagy DTE oldal, órajel |
| `show ip route`, `show ipv6 route` | az irányítótábla: honnan és merre ismeri a hálózatokat |
| `show ip dhcp binding`, `show ip dhcp pool` | melyik PC melyik címet kapta a DHCP-től, mennyi szabad cím maradt |
| `show ip protocols` | a futó irányító protokollok, hálózatok, passzív interfészek |
| `show ip ospf neighbor`, `show ipv6 ospf neighbor` | az OSPF szomszédok és állapotuk |
| `show cdp neighbors` | melyik eszköz melyik porton csatlakozik (kábelezés ellenőrzése) |
| `show ip ssh` | az SSH verziója és állapota |
