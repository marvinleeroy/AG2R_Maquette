# Site Annecy

## Équipements

- Routeur : `LAB_ROUTEUR_ANNECY`, Cisco 2911.
- Switch : `LAB_SWITCH_ANNECY`, Cisco 2960.
- PC : `PC-ANNECY`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
| PC-ANNECY | `FastEthernet0` | LAB_SWITCH_ANNECY | `FastEthernet0/1` |
| LAB_SWITCH_ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/0` |
| LAB_ROUTEUR_ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/2` |
| LAB_ROUTEUR_ANNECY | `GigabitEthernet0/2` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/2` |

## Adressage

| Interface | Adresse |
|---|---|
| LAB_ROUTEUR_ANNECY G0/0 | `192.230.4.1/22` |
| LAB_ROUTEUR_ANNECY G0/1 | `10.10.10.10/30` |
| LAB_ROUTEUR_ANNECY G0/2 | `10.10.10.14/30` |
| LAB_SWITCH_ANNECY VLAN 1 | `192.230.4.2/22` |
| PC-ANNECY | `192.230.5.20/22` |
| Passerelle PC et switch | `192.230.4.1` |

## Configuration du routeur

```cisco
enable
configure terminal
hostname LAB_ROUTEUR_ANNECY
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
hostname LAB_SWITCH_ANNECY
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
description MANAGEMENT_LAB_SWITCH_ANNECY
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

## Correction importante

Avec un masque `/22`, `192.230.5.20` appartient au réseau `192.230.4.0/22`. Il est donc normal que le routeur et le switch utilisent des adresses en `192.230.4.x` tandis que le PC utilise `192.230.5.20`.
