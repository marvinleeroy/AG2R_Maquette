# Site Annecy

## Équipements

<<<<<<< HEAD
- Routeur : `LAB_ROUTEUR_ANNECY`, Cisco 2911.
- Switch : `LAB_SWITCH_ANNECY`, Cisco 2960.
=======
- Routeur : `LAB_ROUTEUR_ANNECY`, Cisco 800 Series.
- Switch : `LAB_SW_ANNECY`, Cisco Catalyst 2960X.
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde
- PC : `PC-ANNECY`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
<<<<<<< HEAD
| PC-ANNECY | `FastEthernet0` | LAB_SWITCH_ANNECY | `FastEthernet0/1` |
| LAB_SWITCH_ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/0` |
| LAB_ROUTEUR_ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/2` |
| LAB_ROUTEUR_ANNECY | `GigabitEthernet0/2` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/2` |
=======
| PC-ANNECY | `FastEthernet0` | LAB_SW_ANNECY | `FastEthernet0/1` |
| SW-ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/0` |
| R-ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/2` |
| R-ANNECY | `GigabitEthernet0/2` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/2` |
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde

## Adressage

| Interface | Adresse |
|---|---|
| LAB_ROUTEUR_ANNECY G0/0 | `192.230.4.1/22` |
| LAB_ROUTEUR_ANNECY G0/1 | `10.10.10.10/30` |
| LAB_ROUTEUR_ANNECY G0/2 | `10.10.10.14/30` |
<<<<<<< HEAD
| LAB_SWITCH_ANNECY VLAN 1 | `192.230.4.2/22` |
=======
| SW-ANNECY VLAN 1 | `192.230.4.2/22` |
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde
| PC-ANNECY | `192.230.5.20/22` |
| Passerelle PC et switch | `192.230.4.1` |

## Configuration du routeur

```cisco
enable
configure terminal
hostname LAB_ROUTEUR_ANNECY
<<<<<<< HEAD
no ip domain-lookup
=======
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde
enable secret ANNECY
service password-encryption
banner motd # Bienvenue sur le site ANNECY #

line console 0
password ANNECY
login
logging synchronous
exit

line vty 0 4
password ANNECY
login
exit

interface vlan 74
description LAN_ANNECY
ip address 192.230.4.1 255.255.252.0
no shutdown
exit

interface vlan 42
description Lien-vers-Evian
ip address 10.10.10.10 255.255.255.252
no shutdown
exit


interface FastEthernet0
description LAN_ANNECY_TRUNK_VERS_SW
switchport mode trunk
switchport trunk native vlan 74
switchport trunk allowed vlan 1-2,42,74,1002-1005
no shutdown
exit


interface FastEthernet1
description LIAISON_VERS_EVIAN
ip address 10.10.10.10 255.255.255.252
no shutdown
exit


interface FastEthernet3
description LIAISON_VERS_VILLEMANDRY_10.10.10.12_30
ip address 10.10.10.14 255.255.255.252
no shutdown
exit


ip route 192.168.2.0 255.255.255.0 10.10.10.9
ip route 192.168.1.0 255.255.255.192 10.10.10.9
ip route 10.20.3.0 255.255.255.128 10.10.10.13


end
copy running-config startup-config
```

## Configuration du switch

```cisco
enable
configure terminal
<<<<<<< HEAD
hostname LAB_SWITCH_ANNECY
no ip domain-lookup
=======
hostname LAB_SW_ANNECY
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde
enable secret ANNECY
service password-encryption

line console 0
password ANNECY
login
logging synchronous
exit

line vty 0 4
password ANNECY
login
exit

vtp mode transparent

<<<<<<< HEAD
interface vlan 1
description MANAGEMENT_LAB_SWITCH_ANNECY
=======
vlan 74
name LAN_ANNECY
exit

interface vlan 74
description MANAGEMENT_SW_ANNECY_VLAN74
>>>>>>> c1c8480505a02dc280cac1333b2103262ecbbdde
ip address 192.230.4.2 255.255.252.0
no shutdown
exit


ip default-gateway 192.230.4.1

interface vlan 42
description Lien-vers-Evian
ip address 192.230.4.2 255.255.252.0
no shutdown
exit


interface GigabitEthernet0/1
description LIAISON_VERS_ANNECY
switchport mode trunk
switchport trunk native vlan 74
switchport trunk allowed vlan 1-2,74,1002-1005
spanning-tree portfast edge
storm-control broadcast level 10.00
storm-control action trap
no shutdown
exit


interface fastEthernet0/1
description PC_ANNECY
switchport mode access
spanning-tree portfast
no shutdown
exit

interface gigabitEthernet0/1
description VERS_LAB_ROUTEUR_ANNECY_G0_0
switchport mode access
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

Dans `Desktop > IP Configuration > Static` :

- IP : `192.230.5.20`.
- Masque : `255.255.252.0`.
- Passerelle : `192.230.4.1`.

## Vérifications

Sur le routeur :

```cisco
show ip interface brief
show ip route
ping 10.10.10.9
ping 10.10.10.13
ping 192.230.5.20
```

Sur le PC :

```text
ping 192.230.4.1
ping 192.168.2.10
ping 192.168.1.10
ping 10.20.3.10
tracert 192.168.2.10
```

