# Maquette AG2R — Vue générale

## Objectif

Construire une maquette pour pratiquer le réseau :

- Azay ;
- Évian ;
- Villemandry ;
- Annecy.

Chaque site possède un routeur, un switch et un PC de test. Les routeurs sont interconnectés par des liaisons Ethernet point à point en `/30`. Le routage est réalisé dans un premier temps avec des routes statiques.

## Schéma logique

```text

 SITE AZAY                                      SITE EVIAN

 PC-AZAY                                        PC-EVIAN
 192.168.2.10/24                                192.168.1.10/26
     |                                               |
     | Fa0/1                                         | Fa0/1
     |                                               |
 +---+--------+                                +---+--------+
 |  SW-AZAY   |                                |  SW-EVIAN  |
 | VLAN 1     |                                | VLAN 1     |
 | .2/24      |                                | .61/26     |
 +---+--------+                                +---+--------+
     | G0/1                                        | G0/1
     |                                             |
     | G0/0                                        | G0/0
 +---+--------+       10.10.10.0/30              +---+--------+
 |  R-AZAY    | G0/1 .1 ================ .2 G0/1 |  R-EVIAN   |
 | G0/0 .1    |                                  | G0/0 .62   |
 | G0/2 .5    |                                  | G0/2 .9    |
 +---+--------+                                  +---+--------+
     | G0/2 .5                                       | G0/2 .9
     |                                               |
     | 10.10.10.4/30                                 | 10.10.10.8/30
     | .6 vers Azay                                  | .10 vers Annecy
     |                                               |
     |                                               |
 +---+----------------+       10.10.10.12/30    +---+-----------+
 | R-VILLEMANDRY      |=========================| R-ANNECY      |
 | G0/1 .6 vers Azay  | .13                  .14| G0/1 .10      |
 | G0/2 .13           |                         | G0/2 .14      |
 | G0/0 .126          |                         | G0/0 .1       |
 +---+----------------+                         +----+----------+
     | G0/0                                          | G0/0
     |                                               |
 +---+----------------+                         +---+----------+
 | SW-VILLEMANDRY    |                          | SW-ANNECY    |
 | VLAN 1            |                          | VLAN 1       |
 | .125/25           |                          | .2/22        |
 +---+----------------+                         +---+----------+
     | Fa0/1                                          | Fa0/1
     |                                                |
 PC-VILLEMANDRY                                  PC-ANNECY
 10.20.3.10/25                                    192.230.5.20/22

 SITE VILLEMANDRY                               SITE ANNECY
```

## Équipements

| Type | Modèle conseillé | Quantité |
|---|---|---:|
| Routeur | Cisco 890 Series | 2 |
| Routeur | Cisco x | x |
| Switch | Cisco Catalyst 2960x | 4 |
| PC | PC-PT | 4 |

sur le site d'Annecy il y a un stack de 2960x

## Plan d’adressage

### LAN des sites

| Site | Réseau | Masque | Routeur | Switch | PC |
|---|---|---|---|---|---|
| Azay | `192.168.2.0/24` | `255.255.255.0` | `192.168.2.1` | `192.168.2.2` | `192.168.2.10` |
| Évian | `192.168.1.0/26` | `255.255.255.192` | `192.168.1.62` | `192.168.1.61` | `192.168.1.10` |
| Villemandry | `10.20.3.0/25` | `255.255.255.128` | `10.20.3.126` | `10.20.3.125` | `10.20.3.10` |
| Annecy | `192.230.4.0/22` | `255.255.252.0` | `192.230.4.1` | `192.230.4.2` | `192.230.5.20` |

### Liaisons inter-routeurs

| Liaison | Réseau | Côté 1 | Côté 2 |
|---|---|---|---|
| Azay — Évian | `10.10.10.0/30` | Azay `10.10.10.1` | Évian `10.10.10.2` |
| Azay — Villemandry | `10.10.10.4/30` | Azay `10.10.10.5` | Villemandry `10.10.10.6` |
| Évian — Annecy | `10.10.10.8/30` | Évian `10.10.10.9` | Annecy `10.10.10.10` |
| Villemandry — Annecy | `10.10.10.12/30` | Villemandry `10.10.10.13` | Annecy `10.10.10.14` |

## Câblage

| Équipement A | Port | Équipement B | Port |
|---|---|---|---|
| PC-AZAY | `FastEthernet0` | LAB_ROUTEUR_SW-AZAY | `FastEthernet0/1` |
| LAB_SW_AZAY | `GigabitEthernet0/1` | LAB_ROUTEUR_AZAY | `GigabitEthernet0/0` |
| LAB_ROUTEUR_AZAY | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/1` |
| LAB_ROUTEUR_AZAY | `GigabitEthernet0/2` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/1` |
| PC-EVIAN | `FastEthernet0` | LAB_SW_EVIAN | `FastEthernet0/1` |
| LAB_SW-EVIAN | `GigabitEthernet0/1` | LAB_ROUTEUR_EVIAN | `GigabitEthernet0/0` |
| R-EVIAN | `GigabitEthernet0/2` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/1` |
| PC-VILLEMANDRY | `FastEthernet0` | LAB_SW_VILLEMANDRY | `FastEthernet0/1` |
| LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/1` | LAB_ROUTEUR_VILLEMANDRY | `GigabitEthernet0/0` |
| LAB_ROUTEUR-VILLEMANDRY | `GigabitEthernet0/2` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/2` |
| PC-ANNECY | `FastEthernet0` | LAB_SW_ANNECY | `FastEthernet0/1` |
| LAB_SW_ANNECY | `GigabitEthernet0/1` | LAB_ROUTEUR_ANNECY | `GigabitEthernet0/0` |
<!--
## Méthode de configuration

1. Placer et renommer les équipements.
2. Réaliser le câblage port par port.
3. Configurer les interfaces des routeurs en ligne de commande.
4. Configurer les routes statiques.
5. Configurer les switches et leurs adresses de gestion.
6. Configurer les adresses IP des PC dans `Desktop > IP Configuration`.
7. Vérifier les interfaces avec `show ip interface brief`.
8. Tester les voisins avec `ping`.
9. Vérifier les routes avec `show ip route`.
10. Tester les communications entre les quatre LAN avec `ping` et `traceroute`.
11. Sauvegarder avec `copy running-config startup-config`.
-->
<!--
## Règles de dépannage

- `administratively down/down` : exécuter `no shutdown`.
- `down/down` : vérifier le câble, le port et l’équipement distant.
- `up/down` : vérifier l’interface distante et le câblage.
- Adresse qui se chevauche : vérifier qu’un même sous-réseau n’est pas utilisé sur deux interfaces différentes du même routeur.
- Une liaison `/30` doit utiliser deux adresses du même réseau, une à chaque extrémité.
--> 
Les fichiers dédiés à chaque site contiennent une configuration détaillées. 
