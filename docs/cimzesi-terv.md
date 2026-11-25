# Címzési terv (05. Projekt, Cisco rész)

Ez a dokumentum az issue #14 (Címzési terv ellenőrzése és dokumentálása) kimenete. A számítás minden lépése le van írva, így a VLSM-diához közvetlenül felhasználható.

## 1. Kiinduló adatok

- Kapott IPv4 hálózat: **192.168.10.0/24** (256 cím)
- IPv6 előtag: **2001:db8:acad::/48** (dokumentációs célú tartomány, a CCNA tananyag is ezt használja)
- Forgalomirányítók: R1, R2, R3 (háromszög topológia), kapcsolók: S1, S2 (R1 telephely), S3 (R2 telephely)

## 2. Címigények

Az igénybe a hosztok mellett az alapértelmezett átjáró (router alinterfész) címe is beleszámít.

| Alhálózat | Telephely | VLAN | Igény (cím) | Indoklás |
|---|---|---|---|---|
| VLAN 10 IRODA | R1 | 10 | 51 | 50 munkaállomás + 1 átjáró |
| VLAN 20 FEJLESZTES | R1 | 20 | 26 | 25 munkaállomás + 1 átjáró |
| VLAN 30 RAKTAR | R2 | 30 | 21 | 20 munkaállomás + 1 átjáró |
| SZERVER LAN | R3 | - | 11 | 10 szerver + 1 átjáró |
| VLAN 99 MENEDZSMENT (R1 telephely) | R1 | 99 | 4 | S1 + S2 + 1 átjáró + 1 tartalék kapcsolónak |
| VLAN 99 MENEDZSMENT (R2 telephely) | R2 | 99 | 3 | S3 + 1 átjáró + 1 tartalék kapcsolónak |
| R1-R2 soros WAN | R1-R2 | - | 2 | 2 forgalomirányító-interfész |
| R1-R3 Ethernet | R1-R3 | - | 2 | 2 forgalomirányító-interfész |
| R2-R3 Ethernet | R2-R3 | - | 2 | 2 forgalomirányító-interfész |

## 3. VLSM számítás lépésről lépésre

**Miért a legnagyobb alhálózattal kezdünk?** A blokkokat méret szerint csökkenő sorrendben osztjuk ki. Így minden blokk a saját méretének többszörösén kezdődik (blokkhatáron), nem keletkezik átfedés és kihasználatlan lyuk a blokkok között, a megmaradó szabad tartomány pedig egyben marad a későbbi bővítéshez. Ha kicsivel kezdenénk, egy nagyobb blokk csak a következő saját blokkhatárán kezdődhetne, és a kettő között címek vesznének el.

**Képlet:** a legkisebb n, amelyre 2^n - 2 ≥ igény (a -2 a hálózatcím és a szórási cím). Az előtag hossza 32 - n, a blokkméret 2^n.

### 1. VLAN 10 IRODA

- Igény: **51** cím (50 munkaállomás + 1 átjáró)
- Legkisebb blokk: n = 6, mert 2^6 - 2 = 62 ≥ 51, de 2^5 - 2 = 30 < 51
- Előtag és maszk: **/26**, 255.255.255.192 (wildcard: 0.0.0.63), blokkméret: 64
- Hálózatcím: **192.168.10.0** (az előző blokk utáni első cím: 192.168.10.0)
- Kiosztható tartomány: 192.168.10.1 - 192.168.10.62 (62 cím, ebből 51 szükséges)
- Szórási cím: 192.168.10.63
- Alapértelmezett átjáró: 192.168.10.1 (R1 Gi0/0.10)
- Következő szabad cím: 192.168.10.64

### 2. VLAN 20 FEJLESZTES

- Igény: **26** cím (25 munkaállomás + 1 átjáró)
- Legkisebb blokk: n = 5, mert 2^5 - 2 = 30 ≥ 26, de 2^4 - 2 = 14 < 26
- Előtag és maszk: **/27**, 255.255.255.224 (wildcard: 0.0.0.31), blokkméret: 32
- Hálózatcím: **192.168.10.64** (az előző blokk utáni első cím: 192.168.10.64)
- Kiosztható tartomány: 192.168.10.65 - 192.168.10.94 (30 cím, ebből 26 szükséges)
- Szórási cím: 192.168.10.95
- Alapértelmezett átjáró: 192.168.10.65 (R1 Gi0/0.20)
- Következő szabad cím: 192.168.10.96

### 3. VLAN 30 RAKTAR

- Igény: **21** cím (20 munkaállomás + 1 átjáró)
- Legkisebb blokk: n = 5, mert 2^5 - 2 = 30 ≥ 21, de 2^4 - 2 = 14 < 21
- Előtag és maszk: **/27**, 255.255.255.224 (wildcard: 0.0.0.31), blokkméret: 32
- Hálózatcím: **192.168.10.96** (az előző blokk utáni első cím: 192.168.10.96)
- Kiosztható tartomány: 192.168.10.97 - 192.168.10.126 (30 cím, ebből 21 szükséges)
- Szórási cím: 192.168.10.127
- Alapértelmezett átjáró: 192.168.10.97 (R2 Gi0/0.30)
- Következő szabad cím: 192.168.10.128

### 4. SZERVER LAN

- Igény: **11** cím (10 szerver + 1 átjáró)
- Legkisebb blokk: n = 4, mert 2^4 - 2 = 14 ≥ 11, de 2^3 - 2 = 6 < 11
- Előtag és maszk: **/28**, 255.255.255.240 (wildcard: 0.0.0.15), blokkméret: 16
- Hálózatcím: **192.168.10.128** (az előző blokk utáni első cím: 192.168.10.128)
- Kiosztható tartomány: 192.168.10.129 - 192.168.10.142 (14 cím, ebből 11 szükséges)
- Szórási cím: 192.168.10.143
- Alapértelmezett átjáró: 192.168.10.129 (R3 Gi0/0)
- Következő szabad cím: 192.168.10.144

### 5. VLAN 99 MENEDZSMENT (R1 telephely)

- Igény: **4** cím (S1 + S2 + 1 átjáró + 1 tartalék kapcsolónak)
- Legkisebb blokk: n = 3, mert 2^3 - 2 = 6 ≥ 4, de 2^2 - 2 = 2 < 4
- Előtag és maszk: **/29**, 255.255.255.248 (wildcard: 0.0.0.7), blokkméret: 8
- Hálózatcím: **192.168.10.144** (az előző blokk utáni első cím: 192.168.10.144)
- Kiosztható tartomány: 192.168.10.145 - 192.168.10.150 (6 cím, ebből 4 szükséges)
- Szórási cím: 192.168.10.151
- Alapértelmezett átjáró: 192.168.10.145 (R1 Gi0/0.99)
- Következő szabad cím: 192.168.10.152

### 6. VLAN 99 MENEDZSMENT (R2 telephely)

- Igény: **3** cím (S3 + 1 átjáró + 1 tartalék kapcsolónak)
- Legkisebb blokk: n = 3, mert 2^3 - 2 = 6 ≥ 3, de 2^2 - 2 = 2 < 3
- Előtag és maszk: **/29**, 255.255.255.248 (wildcard: 0.0.0.7), blokkméret: 8
- Hálózatcím: **192.168.10.152** (az előző blokk utáni első cím: 192.168.10.152)
- Kiosztható tartomány: 192.168.10.153 - 192.168.10.158 (6 cím, ebből 3 szükséges)
- Szórási cím: 192.168.10.159
- Alapértelmezett átjáró: 192.168.10.153 (R2 Gi0/0.99)
- Következő szabad cím: 192.168.10.160

### 7. R1-R2 soros WAN

- Igény: **2** cím (2 forgalomirányító-interfész)
- Legkisebb blokk: n = 2, mert 2^2 - 2 = 2 ≥ 2, de 2^1 - 2 = 0 < 2
- Előtag és maszk: **/30**, 255.255.255.252 (wildcard: 0.0.0.3), blokkméret: 4
- Hálózatcím: **192.168.10.160** (az előző blokk utáni első cím: 192.168.10.160)
- Kiosztható tartomány: 192.168.10.161 - 192.168.10.162 (2 cím, ebből 2 szükséges)
- Szórási cím: 192.168.10.163
- Kiosztás: R1 = 192.168.10.161, R2 = 192.168.10.162
- Következő szabad cím: 192.168.10.164

### 8. R1-R3 Ethernet

- Igény: **2** cím (2 forgalomirányító-interfész)
- Legkisebb blokk: n = 2, mert 2^2 - 2 = 2 ≥ 2, de 2^1 - 2 = 0 < 2
- Előtag és maszk: **/30**, 255.255.255.252 (wildcard: 0.0.0.3), blokkméret: 4
- Hálózatcím: **192.168.10.164** (az előző blokk utáni első cím: 192.168.10.164)
- Kiosztható tartomány: 192.168.10.165 - 192.168.10.166 (2 cím, ebből 2 szükséges)
- Szórási cím: 192.168.10.167
- Kiosztás: R1 = 192.168.10.165, R3 = 192.168.10.166
- Következő szabad cím: 192.168.10.168

### 9. R2-R3 Ethernet

- Igény: **2** cím (2 forgalomirányító-interfész)
- Legkisebb blokk: n = 2, mert 2^2 - 2 = 2 ≥ 2, de 2^1 - 2 = 0 < 2
- Előtag és maszk: **/30**, 255.255.255.252 (wildcard: 0.0.0.3), blokkméret: 4
- Hálózatcím: **192.168.10.168** (az előző blokk utáni első cím: 192.168.10.168)
- Kiosztható tartomány: 192.168.10.169 - 192.168.10.170 (2 cím, ebből 2 szükséges)
- Szórási cím: 192.168.10.171
- Kiosztás: R2 = 192.168.10.169, R3 = 192.168.10.170
- Következő szabad cím: 192.168.10.172

### Összesítő táblázat

| # | Alhálózat | Hálózat/előtag | Maszk | Első kiosztható | Utolsó kiosztható | Szórási | Átjáró | Igény / kiosztható |
|---|---|---|---|---|---|---|---|---|
| 1 | VLAN 10 IRODA | 192.168.10.0/26 | 255.255.255.192 | 192.168.10.1 | 192.168.10.62 | 192.168.10.63 | 192.168.10.1 | 51 / 62 |
| 2 | VLAN 20 FEJLESZTES | 192.168.10.64/27 | 255.255.255.224 | 192.168.10.65 | 192.168.10.94 | 192.168.10.95 | 192.168.10.65 | 26 / 30 |
| 3 | VLAN 30 RAKTAR | 192.168.10.96/27 | 255.255.255.224 | 192.168.10.97 | 192.168.10.126 | 192.168.10.127 | 192.168.10.97 | 21 / 30 |
| 4 | SZERVER LAN | 192.168.10.128/28 | 255.255.255.240 | 192.168.10.129 | 192.168.10.142 | 192.168.10.143 | 192.168.10.129 | 11 / 14 |
| 5 | VLAN 99 MENEDZSMENT (R1 telephely) | 192.168.10.144/29 | 255.255.255.248 | 192.168.10.145 | 192.168.10.150 | 192.168.10.151 | 192.168.10.145 | 4 / 6 |
| 6 | VLAN 99 MENEDZSMENT (R2 telephely) | 192.168.10.152/29 | 255.255.255.248 | 192.168.10.153 | 192.168.10.158 | 192.168.10.159 | 192.168.10.153 | 3 / 6 |
| 7 | R1-R2 soros WAN | 192.168.10.160/30 | 255.255.255.252 | 192.168.10.161 | 192.168.10.162 | 192.168.10.163 | - | 2 / 2 |
| 8 | R1-R3 Ethernet | 192.168.10.164/30 | 255.255.255.252 | 192.168.10.165 | 192.168.10.166 | 192.168.10.167 | - | 2 / 2 |
| 9 | R2-R3 Ethernet | 192.168.10.168/30 | 255.255.255.252 | 192.168.10.169 | 192.168.10.170 | 192.168.10.171 | - | 2 / 2 |

## 4. Tartalék címek

Kiosztva: 192.168.10.0 - 192.168.10.171 (172 cím). **Szabad: 192.168.10.172 - 192.168.10.255 (84 cím).**

Javasolt felhasználás bővítéskor (mind blokkhatáron kezdődik, így VLSM-mel kiosztható):

| Tartomány | Méret | Lehetséges felhasználás |
|---|---|---|
| 192.168.10.172/30 | 4 cím | kapcsolat egy SOHO routerhez / internet-kijárathoz |
| 192.168.10.176/28 | 16 cím | új kis VLAN, pl. nyomtatók vagy IP-kamerák |
| 192.168.10.192/26 | 64 cím | vezeték nélküli (WLAN) vagy vendég VLAN, pl. a 06. projekt Wi-Fi része |

## 5. IPv6 címzési séma

- Minden alhálózat /64: **2001:db8:acad:ID::/64**
- LAN esetén az **ID = a VLAN száma** (VLAN 10 → :10::, VLAN 20 → :20::, VLAN 30 → :30::), a VLAN nélküli szerver LAN a 40-es azonosítót kapta.
- Router-router kapcsolatnál az **ID = a routerpár** (R1-R2 → :12::, R1-R3 → :13::, R2-R3 → :23::).
- Interfész-azonosító: LAN átjáró = ::1, kapcsolaton a router sorszáma (R1 = ::1, R2 = ::2, R3 = ::3).
- Link-local cím (kézzel beállítva, hogy olvasható legyen): R1 = FE80::1, R2 = FE80::2, R3 = FE80::3. A PC-k IPv6 átjárója a saját routerük link-local címe.
- A menedzsment VLAN-ok (VLAN 99) csak IPv4-et használnak.

**Példa:** VLAN 30 → 2001:db8:acad:30::/64, átjáró 2001:db8:acad:30::1 (R2), a PC5 és a PC6 ebből az előtagból SLAAC-kal képzi a címét, átjárójuk FE80::2. R1-R3 kapcsolat → 2001:db8:acad:13::/64, R1 = 2001:db8:acad:13::1, R3 = 2001:db8:acad:13::3.

| Alhálózat | IPv6 előtag | Átjáró / címek |
|---|---|---|
| VLAN 10 IRODA | 2001:db8:acad:10::/64 | 2001:db8:acad:10::1 (R1), link-local FE80::1 |
| VLAN 20 FEJLESZTES | 2001:db8:acad:20::/64 | 2001:db8:acad:20::1 (R1), link-local FE80::1 |
| VLAN 30 RAKTAR | 2001:db8:acad:30::/64 | 2001:db8:acad:30::1 (R2), link-local FE80::2 |
| SZERVER LAN | 2001:db8:acad:40::/64 | 2001:db8:acad:40::1 (R3), link-local FE80::3 |
| R1-R2 soros WAN | 2001:db8:acad:12::/64 | R1: 2001:db8:acad:12::1, R2: 2001:db8:acad:12::2 |
| R1-R3 Ethernet | 2001:db8:acad:13::/64 | R1: 2001:db8:acad:13::1, R3: 2001:db8:acad:13::3 |
| R2-R3 Ethernet | 2001:db8:acad:23::/64 | R2: 2001:db8:acad:23::2, R3: 2001:db8:acad:23::3 |

## 6. Végpontok címzése: DHCP és SLAAC

A PC-k IPv4 címét DHCP osztja ki (R1: VLAN 10 és 20, R2: VLAN 30). IPv6 címüket SLAAC-kal képzik: az előtagot a router RA üzenetéből veszik, az interfész-azonosítót a saját MAC-címükből (EUI-64), az átjárójuk a router link-local címe. SRV1 és a portbiztonsági bemutató laptopjai statikusak.

| DHCP készlet | Router | Hálózat | default-router | Kizárva | Kiosztható | Igény (munkaállomás) |
|---|---|---|---|---|---|---|
| VLAN10 | R1 | 192.168.10.0/26 | 192.168.10.1 | 192.168.10.1 - 192.168.10.9, 192.168.10.51 - 192.168.10.53 | 50 | 50 |
| VLAN20 | R1 | 192.168.10.64/27 | 192.168.10.65 | 192.168.10.65 - 192.168.10.69 | 25 | 25 |
| VLAN30 | R2 | 192.168.10.96/27 | 192.168.10.97 | 192.168.10.97 - 192.168.10.99 | 27 | 20 |

A kizárt tartományok miatt minden készlet első szabad címe a terv szerinti első PC-cím. Ha a PC-k ebben a sorrendben kapcsolnak DHCP-re: PC1 (VLAN 10), PC3 (VLAN 10), PC2 (VLAN 20), PC4 (VLAN 20), PC5 (VLAN 30), PC6 (VLAN 30), akkor pontosan a lenti táblázat címeit kapják. A kizárt tartomány alja (átjáró + tartalék) később statikus eszközöknek (pl. nyomtató) is jut.

## 7. Címtábla eszközönként

| Eszköz | Interfész | IPv4 cím | Maszk | IPv6 cím / előtag | Link-local | Alapért. átjáró |
|---|---|---|---|---|---|---|
| R1 | Gi0/0.10 | 192.168.10.1 | 255.255.255.192 | 2001:db8:acad:10::1/64 | FE80::1 | - |
| R1 | Gi0/0.20 | 192.168.10.65 | 255.255.255.224 | 2001:db8:acad:20::1/64 | FE80::1 | - |
| R1 | Gi0/0.99 | 192.168.10.145 | 255.255.255.248 | - | - | - |
| R1 | Se0/0/0 | 192.168.10.161 | 255.255.255.252 | 2001:db8:acad:12::1/64 | FE80::1 | - |
| R1 | Gi0/1 | 192.168.10.165 | 255.255.255.252 | 2001:db8:acad:13::1/64 | FE80::1 | - |
| R2 | Gi0/0.30 | 192.168.10.97 | 255.255.255.224 | 2001:db8:acad:30::1/64 | FE80::2 | - |
| R2 | Gi0/0.99 | 192.168.10.153 | 255.255.255.248 | - | - | - |
| R2 | Se0/0/0 | 192.168.10.162 | 255.255.255.252 | 2001:db8:acad:12::2/64 | FE80::2 | - |
| R2 | Gi0/1 | 192.168.10.169 | 255.255.255.252 | 2001:db8:acad:23::2/64 | FE80::2 | - |
| R3 | Gi0/0 | 192.168.10.129 | 255.255.255.240 | 2001:db8:acad:40::1/64 | FE80::3 | - |
| R3 | Gi0/1 | 192.168.10.166 | 255.255.255.252 | 2001:db8:acad:13::3/64 | FE80::3 | - |
| R3 | Gi0/2 | 192.168.10.170 | 255.255.255.252 | 2001:db8:acad:23::3/64 | FE80::3 | - |
| S1 | VLAN 99 (SVI) | 192.168.10.146 | 255.255.255.248 | - | - | 192.168.10.145 |
| S2 | VLAN 99 (SVI) | 192.168.10.147 | 255.255.255.248 | - | - | 192.168.10.145 |
| S3 | VLAN 99 (SVI) | 192.168.10.154 | 255.255.255.248 | - | - | 192.168.10.153 |
| PC1 | Fa0 | 192.168.10.10 (DHCP) | 255.255.255.192 | SLAAC (2001:db8:acad:10::/64, EUI-64) | (automatikus) | 192.168.10.1 / FE80::1 |
| PC2 | Fa0 | 192.168.10.70 (DHCP) | 255.255.255.224 | SLAAC (2001:db8:acad:20::/64, EUI-64) | (automatikus) | 192.168.10.65 / FE80::1 |
| PC3 | Fa0 | 192.168.10.11 (DHCP) | 255.255.255.192 | SLAAC (2001:db8:acad:10::/64, EUI-64) | (automatikus) | 192.168.10.1 / FE80::1 |
| PC4 | Fa0 | 192.168.10.71 (DHCP) | 255.255.255.224 | SLAAC (2001:db8:acad:20::/64, EUI-64) | (automatikus) | 192.168.10.65 / FE80::1 |
| PC5 | Fa0 | 192.168.10.100 (DHCP) | 255.255.255.224 | SLAAC (2001:db8:acad:30::/64, EUI-64) | (automatikus) | 192.168.10.97 / FE80::2 |
| PC6 | Fa0 | 192.168.10.101 (DHCP) | 255.255.255.224 | SLAAC (2001:db8:acad:30::/64, EUI-64) | (automatikus) | 192.168.10.97 / FE80::2 |
| SRV1 | Fa0 | 192.168.10.130 (statikus) | 255.255.255.240 | 2001:db8:acad:40::10/64 (statikus) | (automatikus) | 192.168.10.129 / FE80::3 |
| LAPTOP1 (6. fejezet, hub az S1 Fa0/2-n) | Fa0 | 192.168.10.51 (statikus) | 255.255.255.192 | - | - | 192.168.10.1 |
| LAPTOP2 (6. fejezet, hub az S1 Fa0/2-n) | Fa0 | 192.168.10.52 (statikus) | 255.255.255.192 | - | - | 192.168.10.1 |
| LAPTOP3 (6. fejezet, hub az S1 Fa0/2-n) | Fa0 | 192.168.10.53 (statikus) | 255.255.255.192 | - | - | 192.168.10.1 |

## 8. Ellenőrzés

A tervet egy ellenőrző szkript is átnézte (`build.py`):

- nincs átfedés az alhálózatok között, mind a 192.168.10.0/24-en belül van;
- minden alhálózat a legkisebb elégséges blokkot kapta (eggyel kisebb blokk már nem lenne elég);
- minden blokk a saját blokkhatárán kezdődik, a kiosztás folytonos, a szabad rész 192.168.10.172-től kezdődik;
- minden kiosztott cím egyedi, egyik sem hálózatcím vagy szórási cím;
- minden LAN átjárója az első kiosztható cím, a PC-k átjárója a saját alhálózatuk routerére mutat;
- minden DHCP-készlet a saját alhálózatára mutat, a kizárt tartományban van az átjáró és minden statikus cím, a készlet elég a munkaállomásoknak, és a megadott sorrendben a PC-k pontosan a tervezett címet kapják;
- az OSPF `network` utasítások (wildcard maszkkal) pontosan a router interfészeit fedik le.

> Megjegyzés: ezt a tervet mi számoltuk ki. Ha a csapat kapott egy előre megadott címtáblát, azt a fenti lépésekkel kell összevetni, és az eltéréseket egyeztetni (issue #14).

