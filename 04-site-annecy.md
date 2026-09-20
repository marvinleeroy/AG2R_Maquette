# Site Annecy

## Équipements

- Routeur : `R-ANNECY`, Cisco 2911.
- Switch : `SW-ANNECY`, Cisco 2960.
- PC : `PC-ANNECY`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
| PC-ANNECY | `FastEthernet0` | SW-ANNECY | `FastEthernet0/1` |
| SW-ANNECY | `GigabitEthernet0/1` | R-ANNECY | `GigabitEthernet0/0` |
| R-ANNECY | `GigabitEthernet0/1` | R-EVIAN | `GigabitEthernet0/2` |
| R-ANNECY | `GigabitEthernet0/2` | R-VILLEMANDRY | `GigabitEthernet0/2` |

## Adressage

| Interface | Adresse |
|---|---|
| R-ANNECY G0/0 | `192.230.4.1/22` |
| R-ANNECY G0/1 | `10.10.10.10/30` |
| R-ANNECY G0/2 | `10.10.10.14/30` |
| SW-ANNECY VLAN 1 | `192.230.4.2/22` |
| PC-ANNECY | `192.230.5.20/22` |
| Passerelle PC et switch | `192.230.4.1` |

## Configuration du routeur

```cisco
enable
configure terminal
hostname R-ANNECY
no ip domain-lookup
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

interface gigabitEthernet0/0
description LAN_ANNECY_192.230.4.0_22
ip address 192.230.4.1 255.255.252.0
no shutdown
exit

interface gigabitEthernet0/1
description LIAISON_VERS_EVIAN_10.10.10.8_30
ip address 10.10.10.10 255.255.255.252
no shutdown
exit

interface gigabitEthernet0/2
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
hostname SW-ANNECY
no ip domain-lookup
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

interface vlan 1
description MANAGEMENT_SW_ANNECY
ip address 192.230.4.2 255.255.252.0
no shutdown
exit

ip default-gateway 192.230.4.1

interface fastEthernet0/1
description PC_ANNECY
switchport mode access
spanning-tree portfast
no shutdown
exit

interface gigabitEthernet0/1
description VERS_R_ANNECY_G0_0
switchport mode access
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

- IP : `192.230.5.20`.
- Masque : `255.255.252.0`.
- Passerelle : `192.230.4.1`.

## Vérifications

```cisco
show ip interface brief
show ip route
ping 10.10.10.9
ping 10.10.10.13
ping 192.230.5.20
```

Depuis le PC :

```text
ping 192.168.2.10
ping 192.168.1.10
ping 10.20.3.10
tracert 192.168.2.10
```

## Correction importante

Avec un masque `/22`, `192.230.5.20` appartient au réseau `192.230.4.0/22`. Il est donc normal que le routeur et le switch utilisent des adresses en `192.230.4.x` tandis que le PC utilise `192.230.5.20`.
