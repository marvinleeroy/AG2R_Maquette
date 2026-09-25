# Site Azay

## Équipements

- Routeur : `LAB_ROUTEUR_AZAY`, Cisco 2911.
- Switch : `LAB_SWITCH_AZAY`, Cisco 2960.
- PC : `PC-AZAY`.

## Connexions

| Équipement | Port | Équipement | Port |
|---|---|---|---|
| PC-AZAY | `FastEthernet0` | LAB_SWITCH_AZAY | `FastEthernet0/1` |
| LAB_SWITCH_AZAY | `GigabitEthernet0/1` | LAB_ROUTEUR_AZAY | `GigabitEthernet0/0` |
| LAB_ROUTEUR_AZAY | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/1` |
| LAB_ROUTEUR_AZAY | `GigabitEthernet0/2` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/1` |

## Adressage

| Interface | Adresse |
|---|---|
| LAB_ROUTEUR_AZAY G0/0 | `192.168.2.1/24` |
| LAB_ROUTEUR_AZAY G0/1 | `10.10.10.1/30` |
| LAB_ROUTEUR_AZAY G0/2 | `10.10.10.5/30` |
| LAB_SWITCH_AZAY VLAN 1 | `192.168.2.2/24` |
| PC-AZAY | `192.168.2.10/24` |
| Passerelle PC et switch | `192.168.2.1` |

## Configuration du routeur

```cisco
enable
configure terminal
hostname LAB_ROUTEUR_AZAY
no ip domain-lookup
enable secret AZAY
service password-encryption
banner motd # Bienvenue sur le site AZAY #

line console 0
password AZAY
login
logging synchronous
exit

line vty 0 4
password AZAY
login
exit

interface gigabitEthernet0/0
description LAN_AZAY_192.168.2.0_24
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet0/1
description LIAISON_VERS_EVIAN_10.10.10.0_30
ip address 10.10.10.1 255.255.255.252
no shutdown
exit

interface gigabitEthernet0/2
description LIAISON_VERS_VILLEMANDRY_10.10.10.4_30
ip address 10.10.10.5 255.255.255.252
no shutdown
exit

ip route 192.168.1.0 255.255.255.192 10.10.10.2
ip route 10.20.3.0 255.255.255.128 10.10.10.6
ip route 192.230.4.0 255.255.252.0 10.10.10.2

end
copy running-config startup-config
```

## Configuration du switch

```cisco
enable
configure terminal
hostname LAB_SWITCH_AZAY
no ip domain-lookup
enable secret AZAY
service password-encryption

line console 0
password AZAY
login
logging synchronous
exit

line vty 0 4
password AZAY
login
exit

vtp mode transparent

interface vlan 1
description MANAGEMENT_LAB_SWITCH_AZAY
ip address 192.168.2.2 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.2.1

interface fastEthernet0/1
description PC_AZAY
switchport mode access
spanning-tree portfast
no shutdown
exit

interface gigabitEthernet0/1
description VERS_LAB_ROUTEUR_AZAY_G0_0
switchport mode access
no shutdown
exit

end
copy running-config startup-config
```

## Configuration du PC

Dans `Desktop > IP Configuration > Static` :

- IP : `192.168.2.10`.
- Masque : `255.255.255.0`.
- Passerelle : `192.168.2.1`.

## Vérifications

Sur le routeur :

```cisco
show ip interface brief
show ip route
ping 10.10.10.2
ping 10.10.10.6
ping 192.168.2.10
```

Sur le PC :

```text
ping 192.168.2.1
ping 192.168.1.10
ping 10.20.3.10
ping 192.230.5.20
tracert 192.230.5.20
```
