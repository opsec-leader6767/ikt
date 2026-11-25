# Forgalomirányítás: RIP kontra OSPF (issue #18)

A Cisco útmutató 5. fejezetének mérési jegyzőkönyve. A kimeneteket a mérés után kell bemásolni, az elvárt értékek az útmutató 5.5 pontjából valók.

## Elvárt eredmények (útmutató 5.5)

| Fázis | R1 útja az R2 LAN felé (192.168.10.96/27) | PC1 `tracert 192.168.10.100` | R1 IPv6 útja (2001:db8:acad:30::/64) |
|---|---|---|---|
| 1. RIPv2 | R [120/1] via 192.168.10.162, Serial0/0/0 | 192.168.10.1, 192.168.10.162, 192.168.10.100 | S [1/0] via FE80::2, Serial0/0/0 |
| 2. OSPF + RIP | O [110/3] via 192.168.10.166, GigabitEthernet0/1 | 192.168.10.1, 192.168.10.166, 192.168.10.169, 192.168.10.100 | S [1/0] via FE80::2, Serial0/0/0 |
| 3. csak OSPF + OSPFv3 | O [110/3] via 192.168.10.166, GigabitEthernet0/1 | 192.168.10.1, 192.168.10.166, 192.168.10.169, 192.168.10.100 | O [110/3] via FE80::3, GigabitEthernet0/1 |
| 3. R1 Gi0/1 letiltva | O [110/782] via 192.168.10.162, Serial0/0/0 | 192.168.10.1, 192.168.10.162, 192.168.10.100 | O [110/782] via FE80::2, Serial0/0/0 |

## 1. fázis: RIPv2

**R1 `show ip route`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert 192.168.10.100`**

```text
(ide másold a kimenetet)
```

Egyezik az 5.5 táblázattal? igen / nem. Ha nem, az eltérés oka:

## 2. fázis: OSPF a RIP mellett

**R1 `show ip route`**

```text
(ide másold a kimenetet)
```

**R1 `show ip protocols`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert 192.168.10.100`**

```text
(ide másold a kimenetet)
```

Egyezik az 5.5 táblázattal? igen / nem. Ha nem, az eltérés oka:

## 3. fázis: csak OSPF + OSPFv3

**R1 `show ip route`**

```text
(ide másold a kimenetet)
```

**R1 `show ipv6 route`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert 192.168.10.100`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert <PC5 IPv6 címe>` (PC5-ön `ipconfig`)**

```text
(ide másold a kimenetet)
```

Egyezik az 5.5 táblázattal? igen / nem. Ha nem, az eltérés oka:

Migrációs teszt, elveszett csomagok: IPv4 (RIP törlése): ____, IPv6 (statikus utak törlése): ____

## Kiesésteszt: R1 Gi0/1 letiltva

**R1 `show ip route ospf`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert 192.168.10.100`**

```text
(ide másold a kimenetet)
```

**PC1 `tracert 192.168.10.100` a `no shutdown` után**

```text
(ide másold a kimenetet)
```

Egyezik az 5.5 táblázattal? igen / nem. Ha nem, az eltérés oka:

Elveszett csomagok a kiesésnél: ____

## Magyarázat

### Miért választ más utat a RIP és az OSPF?

A RIP csak az ugrásokat számolja: R1-ről az R2 LAN a soros vonalon 1 ugrás, R3-on át 2, ezért a lassú, 128 kbit/s-os soros vonalat választja. Az OSPF a sávszélességből számolt költséget adja össze: a soros út 781 + 1 = 782, a Gigabit kerülőút 1 + 1 + 1 = 3, ezért R3-on át megy.

### Adminisztratív távolság és metrika

A metrika egy protokollon belül dönt a lehetséges utak között (RIP: ugrásszám, OSPF: költség). Az adminisztratív távolság (AD) azt mutatja, mennyire megbízható egy útvonal forrása, és akkor dönt, ha ugyanazt a hálózatot több forrás is ismeri: a kisebb nyer. Közvetlen: 0, statikus: 1, OSPF: 110, RIP: 120. Ezért került a 2. fázisban az OSPF útja a táblába, pedig a RIP is futott.

### Statikus és dinamikus útvonalak

A statikus utat a rendszergazda írja be kézzel: nincs protokollforgalom, de a hálózat változását nem követi, kiesésnél csak egy előre beállított lebegő út (nálunk AD 5) segít. A dinamikus protokoll (RIP, OSPF) a routerek között cserél információt, és kiesésnél magától új utat keres. A 4. fejezetben az IPv6 statikus volt, a 3. fázisban ezt OSPFv3 váltotta fel.
