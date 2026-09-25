# Site Évian

## Équipements

- Routeur : `LAB_ROUTEUR_EVIAN`, Cisco 2911.
- Switch : `LAB_SWITCH_EVIAN`, Cisco 2960.
- PC : `PC-EVIAN`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
| PC-EVIAN | `FastEthernet0` | LAB_SWITCH_EVIAN | `FastEthernet0/1` |
| LAB_SWITCH_EVIAN | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/0` |
| LAB_ROUTEUR_EVIAN | `GigabitEthernet0/1` | LAB_ROUTEUR_AZAY | `GigabitEthernet0/1` |
| LAB_ROUTEUR_EVIAN | `GigabitEthernet0/2` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/1` |

## Adressage

| Interface | Adresse |
|---|---|
| LAB_ROUTEUR_EVIAN G0/0 | `192.168.1.62/26` |
| LAB_ROUTEUR_EVIAN G0/1 | `10.10.10.2/30` |
| LAB_ROUTEUR_EVIAN G0/2 | `10.10.10.9/30` |
| LAB_SWITCH_EVIAN VLAN 1 | `192.168.1.61/26` |
| PC-EVIAN | `192.168.1.10/26` |
| Passerelle PC et switch | `192.168.1.62` |

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

interface gigabitEthernet0/0
description LAN_EVIAN_192.168.1.0_26
ip address 192.168.1.62 255.255.255.192
no shutdown
exit

interface gigabitEthernet0/1
description LIAISON_VERS_AZAY_10.10.10.0_30
ip address 10.10.10.2 255.255.255.252
no shutdown
exit

interface gigabitEthernet0/2
description LIAISON_VERS_ANNECY_10.10.10.8_30
ip address 10.10.10.9 255.255.255.252
no shutdown
exit

ip route 192.168.2.0 255.255.255.0 10.10.10.1
ip route 10.20.3.0 255.255.255.128 10.10.10.1
ip route 192.230.4.0 255.255.252.0 10.10.10.10

end
copy running-config startup-config
```

> Attention : `G0/1` doit utiliser `10.10.10.2` pour Azay. `G0/2` doit utiliser `10.10.10.9` pour Annecy. Ne place pas ces deux adresses sur la même liaison ni sur deux interfaces du même routeur appartenant au même `/30`.

## Configuration du switch

```cisco
enable
configure terminal
hostname LAB_SWITCH_EVIAN
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

interface vlan 1
description MANAGEMENT_LAB_SWITCH_EVIAN
ip address 192.168.1.61 255.255.255.192
no shutdown
exit

ip default-gateway 192.168.1.62

interface fastEthernet0/1
description PC_EVIAN
switchport mode access
spanning-tree portfast
no shutdown
exit

interface gigabitEthernet0/1
description VERS_LAB_ROUTEUR_EVIAN_G0_0
switchport mode access
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

Dans `Desktop > IP Configuration > Static` :

- IP : `192.168.1.10`.
- Masque : `255.255.255.192`.
- Passerelle : `192.168.1.62`.

## Vérifications

Sur le routeur :

```cisco
show ip interface brief
show ip route
ping 10.10.10.1
ping 10.10.10.10
ping 192.168.1.10
```

Sur le PC :

```text
ping 192.168.1.62
ping 192.168.2.10
ping 10.20.3.10
ping 192.230.5.20
tracert 192.230.5.20
```
