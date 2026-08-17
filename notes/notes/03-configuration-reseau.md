# Configuration réseau du laboratoire Active Directory

## Objectif

Avant de déployer Active Directory, les différentes machines du laboratoire doivent pouvoir communiquer entre elles.

Le laboratoire est constitué de trois machines virtuelles :

| Machine | Rôle | Adresse IPv4 |
| --- | --- | --- |
| DC01 | Contrôleur de domaine principal | 172.16.0.1 |
| DC02 | Second serveur Windows Server | 172.16.0.2 |
| CLIENT01 | Poste client Windows 11 | 172.16.0.20 |

Le masque utilisé est :

`255.255.255.0 (/24)`

## Configuration du réseau VirtualBox

Les trois machines virtuelles sont connectées au même réseau interne VirtualBox :

`AD-LAB`

Configuration utilisée :

- Mode d'accès réseau : Réseau interne
- Nom du réseau : `AD-LAB`
- Câble virtuel connecté : Oui

Ce réseau permet aux machines du laboratoire de communiquer entre elles.

## Configuration IPv4

### DC01

- Adresse IPv4 : `172.16.0.1`
- Masque : `255.255.255.0`
- DHCP : désactivé
- DNS : `172.16.0.1`

DC01 possède une adresse IP statique car il héberge les services Active Directory et DNS.

### DC02

- Adresse IPv4 : `172.16.0.2`
- Masque : `255.255.255.0`
- DHCP : désactivé

### CLIENT01

- Adresse IPv4 : `172.16.0.20`
- Masque : `255.255.255.0`
- DNS : `172.16.0.1`

## Validation de la connectivité

La connectivité entre les machines est testée avec la commande :

```cmd
ping <adresse_IP>
```

Exemple depuis DC01 vers CLIENT01 :

```cmd
ping 172.16.0.20
```

Les tests sont effectués dans les deux sens afin de vérifier que les machines peuvent communiquer correctement.

## Autorisation des requêtes ICMP

Lors des tests, les requêtes ICMP ont été autorisées dans le pare-feu Windows :

```cmd
netsh advfirewall firewall add rule name="Autoriser ping IPv4" protocol=icmpv4:8,any dir=in action=allow
```

## Résultat

Après configuration :

- DC01 communique avec CLIENT01 ;
- DC01 communique avec DC02 ;
- les machines appartiennent au réseau interne `AD-LAB` ;
- les serveurs utilisent des adresses IP statiques ;
- DC01 utilise sa propre adresse comme serveur DNS ;
- l'infrastructure réseau est prête pour le déploiement d'Active Directory.
