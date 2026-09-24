# Site Évian

## Équipements

- Routeur : `LAB_ROUTEUR_EVIAN`, Cisco 890 Series.
- Switch : `LAB_SW_EVIAN`, Cisco Catalyst 2960X.
- PC : `PC-EVIAN`.

## Connexions

| Équipement       | Port                    | Équipement            | Port                         |
|------------------|-------------------------|-----------------------|------------------------------|
| PC-EVIAN         | `FastEthernet0`         | LAB_SW_EVIAN          | `FastEthernet0/1`            |
| LAB_SW_EVIAN     | `GigabitEthernet3/0/24` | LAB_ROUTEUR_EVIAN     | `GigabitEthernet0` (LAN)     |
| LAB_ROUTEUR_EVIAN| `GigabitEthernet8`      | LAB_ROUTEUR_AZAY      | `GigabitEthernet8` (WAN)     |
| LAB_ROUTEUR_EVIAN| `GigabitEthernet9`      | LAB_ROUTEUR_ANNECY    | `GigabitEthernet9` (WAN)     |

## Adressage

| Interface                 | Adresse                    |
|---------------------------|----------------------------|
| LAB_ROUTEUR_EVIAN G0      | `192.168.1.62/26`          |
| LAB_ROUTEUR_EVIAN G8      | `10.10.10.2/30`            |
| LAB_ROUTEUR_EVIAN G9      | `10.10.10.9/30`            |
| LAB_SW_EVIAN VLAN 10      | `192.168.1.61/26`          |
| PC-EVIAN                  | `192.168.1.10/26`          |
| Passerelle PC et switch   | `192.168.1.62`             |

## Configuration du routeur

```cisco
enable
configure terminal

hostname LAB_ROUTEUR_EVIAN
no ip domain-lookup
enable secret EVIAN
service password-encryption
banner motd # Bienvenue sur le site EVIAN #

line console 0
password EVIAN
login
logging synchronous
exit

line vty 0 4
password EVIAN
login
exit

! --- LAN : port L2 + SVI + TRUNK  ---

interface GigabitEthernet0
 description LAN_EVIAN_TRUNK_VERS_SW
 switchport mode trunk
 switchport trunk native vlan 10
 switchport trunk allowed vlan 1-2,10,1002-1005
 no shutdown
exit

interface vlan 10
 description LAN_EVIAN_192.168.1.0_26
 ip address 192.168.1.62 255.255.255.192
 no shutdown
exit

! --- WAN : liaison vers AZAY (10.10.10.0/30) ---
interface GigabitEthernet8
description LIAISON_VERS_AZAY_10.10.10.0_30
ip address 10.10.10.2 255.255.255.252
no shutdown
exit

! --- WAN : liaison vers ANNECY (10.10.10.8/30) ---
interface GigabitEthernet9
description LIAISON_VERS_ANNECY_10.10.10.8_30
ip address 10.10.10.9 255.255.255.252
no shutdown
exit

end

copy running-config startup-config
```

### Routes statiques

```cisco
enable
configure terminal

ip route 192.168.2.0 255.255.255.0 10.10.10.1
ip route 10.20.3.0 255.255.255.128 10.10.10.1
ip route 192.230.4.0 255.255.252.0 10.10.10.10

end
copy running-config startup-config
```

### Tests depuis le routeur

```cisco
ping 10.20.3.10
ping 192.230.5.20
traceroute 192.230.5.20
```

<!-- 
> Attention : G8 doit utiliser 10.10.10.2 pour Azay. G9 doit utiliser 10.10.10.9 pour Annecy. 
Ne place pas ces deux adresses sur la même liaison ni sur deux interfaces du même routeur appartenant au même /30. 
-->

## Configuration du switch

```cisco
enable
configure terminal
hostname LAB_SW_EVIAN
no ip domain-lookup
enable secret EVIAN
service password-encryption

line console 0
password EVIAN
login
logging synchronous
exit

line vty 0 4
password EVIAN
login
exit

vtp mode transparent

! --- VLANs ---
vlan 10
name LAN_EVIAN
exit

! --- Management du switch sur VLAN 10 (même sous-réseau que le LAN) ---
interface vlan 10
description MANAGEMENT_SW_EVIAN_VLAN10
ip address 192.168.1.61 255.255.255.192
no shutdown
exit

! --- Passerelle par défaut = SVI du routeur ---
ip default-gateway 192.168.1.62

! --- Port vers le routeur ---
interface GigabitEthernet3/0/24
 description ROUTE_VERS_ROUTEUR_LAN_EVIAN
 switchport mode trunk
 switchport trunk native vlan 10
 switchport trunk allowed vlan 1-2,10,1002-1005
 spanning-tree portfast edge
 storm-control broadcast level 10.00
 storm-control action trap
 no shutdown

! --- Tous les autres ports en VLAN 10 (LAN EVIAN) ---
interface range GigabitEthernet3/0/1 - 23
description PORTS_LAN_EVIAN
switchport mode access
switchport access vlan 10
spanning-tree portfast edge
no shutdown
exit

interface range GigabitEthernet3/0/25 - 28
description PORTS_LAN_EVIAN
switchport mode access
switchport access vlan 10
spanning-tree portfast edge
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

- IP : `192.168.1.10`
- Masque : `255.255.255.192`
- Passerelle : `192.168.1.62`

## Vérifications

Depuis le routeur :

```cisco
show ip interface brief
show ip route
ping 10.10.10.1
ping 10.10.10.10
ping 192.168.1.10
```

Depuis le PC :

```text
ping 192.168.2.10
```

---
