# Portbiztonság bemutatása (issue #19)

Tesztport: S1 Fa0/2 (VLAN 10), rajta egy hub és három laptop (LAPTOP1-3, 192.168.10.51-53). Portonként 1 MAC-cím engedélyezett: a LAPTOP1 lett az engedélyezett (sticky) eszköz, a LAPTOP2 és a LAPTOP3 sértést okoz.

## A három sértési mód összevetése

| Sértési mód | Eldobja a keretet | Naplóüzenetet küld | Sértésszámláló nő | Letiltja a portot (err-disabled) |
|---|---|---|---|---|
| protect | igen | nem | nem | nem |
| restrict | igen | igen | igen | nem |
| shutdown (alapértelmezett) | igen | igen | igen | igen |

## Mérések

### Protect

- `show port-security interface fa0/2`: ![protect](kepek/portbiztonsag-protect.png)
- Ping az átjáróra (192.168.10.1): LAPTOP1: ____, LAPTOP2: ____, LAPTOP3: ____
- Port Status: ____, Security Violation Count: ____

### Restrict

- `show port-security interface fa0/2`: ![restrict](kepek/portbiztonsag-restrict.png)
- Ping az átjáróra (192.168.10.1): LAPTOP1: ____, LAPTOP2: ____, LAPTOP3: ____
- Port Status: ____, Security Violation Count: ____

### Shutdown

- `show port-security interface fa0/2`: ![shutdown](kepek/portbiztonsag-shutdown.png)
- Ping az átjáróra (192.168.10.1): LAPTOP1: ____, LAPTOP2: ____, LAPTOP3: ____
- Port Status: ____, Security Violation Count: ____

## A MAC-tábla oszlopai (`show mac address-table`)

| Oszlop | Jelentés |
|---|---|
| Vlan | a VLAN, amelyben a kapcsoló a címet tanulta |
| Mac Address | az eszköz hálózati kártyájának fizikai (MAC) címe |
| Type | DYNAMIC: forgalomból tanult, kb. 5 perc után elöregszik. STATIC: kézzel vagy sticky módon rögzített, nem öregszik el |
| Ports | a port, amelyen az eszköz elérhető (távoli eszköznél a trönk port) |

## Err-disabled állapot felismerése és helyreállítása

Felismerés: a link piros, a `show interfaces fa0/2` első sora *down, line protocol is down (err-disabled)*, a `show port-security interface fa0/2` szerint a Port Status *Secure-shutdown*.

1. A sértés okának megszüntetése (a LAPTOP2 és a LAPTOP3 lecsatlakoztatása a hubról).
2. Az interfész újraindítása: `shutdown`, majd `no shutdown` a Fa0/2-n.
3. Ellenőrzés: a LAPTOP1 pingje újra megy, a port *Secure-up*.

Valós kapcsolón automatikus helyreállítás is beállítható (`errdisable recovery cause psecure-violation`).

## A sértő laptop MAC-címe (11. feladathoz)

Last Source Address: ____ (átadva opsec-leader6767-nek, dátum: ____)

## Lezárás

A tesztport restrict módban maradt, a konfiguráció mentve: igen / nem
