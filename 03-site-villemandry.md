# Site Villemandry

## Équipements

- Routeur : `LAB_ROUTEUR_VILLEMANDRY`, Cisco 2911.
- Switch : `LAB_SWITCH_VILLEMANDRY`, Cisco 2960.
- PC : `PC-VILLEMANDRY`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
| PC-VILLEMANDRY | `FastEthernet0` | LAB_SWITCH_VILLEMANDRY | `FastEthernet0/1` |
| LAB_SWITCH_VILLEMANDRY | `GigabitEthernet0/1` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/0` |
| LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/1` | LAB_ROUTEUR_AZAY | `GigabitEthernet0/2` |
| LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/2` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/2` |

## Adressage

| Interface | Adresse |
|---|---|
| LAB_ROUTEUR_VILLEMANDRY G0/0 | `10.20.3.126/25` |
| LAB_ROUTEUR_VILLEMANDRY G0/1 | `10.10.10.6/30` |
| LAB_ROUTEUR_VILLEMANDRY G0/2 | `10.10.10.13/30` |
| LAB_SWITCH_VILLEMANDRY VLAN 1 | `10.20.3.125/25` |
| PC-VILLEMANDRY | `10.20.3.10/25` |
| Passerelle PC et switch | `10.20.3.126` |

## Configuration du routeur

```cisco
enable
configure terminal
hostname LAB_ROUTEUR_VILLEMANDRY
no ip domain-lookup
enable secret VILLEMANDRY
service password-encryption
banner motd # Bienvenue sur le site VILLEMANDRY #

line console 0
password VILLEMANDRY
login
logging synchronous
exit

line vty 0 4
password VILLEMANDRY
login
exit

interface gigabitEthernet0/0
description LAN_VILLEMANDRY_10.20.3.0_25
ip address 10.20.3.126 255.255.255.128
no shutdown
exit

interface gigabitEthernet0/1
description LIAISON_VERS_AZAY_10.10.10.4_30
ip address 10.10.10.6 255.255.255.252
no shutdown
exit

interface gigabitEthernet0/2
description LIAISON_VERS_ANNECY_10.10.10.12_30
ip address 10.10.10.13 255.255.255.252
no shutdown
exit

ip route 192.168.2.0 255.255.255.0 10.10.10.5
ip route 192.168.1.0 255.255.255.192 10.10.10.5
ip route 192.230.4.0 255.255.252.0 10.10.10.14

end
copy running-config startup-config
```

## Configuration du switch

```cisco
enable
configure terminal
hostname LAB_SWITCH_VILLEMANDRY
no ip domain-lookup
enable secret VILLEMANDRY
service password-encryption

line console 0
password VILLEMANDRY
login
logging synchronous
exit

line vty 0 4
password VILLEMANDRY
login
exit

vtp mode transparent

interface vlan 1
description MANAGEMENT_LAB_SWITCH_VILLEMANDRY
ip address 10.20.3.125 255.255.255.128
no shutdown
exit

ip default-gateway 10.20.3.126

interface fastEthernet0/1
description PC_VILLEMANDRY
switchport mode access
spanning-tree portfast
no shutdown
exit

interface gigabitEthernet0/1
description VERS_LAB_ROUTEUR_VILLEMANDRY_G0_0
switchport mode access
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

Dans `Desktop > IP Configuration > Static` :

- IP : `10.20.3.10`.
- Masque : `255.255.255.128`.
- Passerelle : `10.20.3.126`.

## Vérifications

Sur le routeur :

```cisco
show ip interface brief
show ip route
ping 10.10.10.5
ping 10.10.10.14
ping 10.20.3.10
```

Sur le PC :

```text
ping 10.20.3.126
ping 192.168.2.10
ping 192.168.1.10
ping 192.230.5.20
tracert 192.230.5.20
```
