# KB-004 - Perte de connectivité liée à une adresse MAC dupliquée

## Problème

Lors de la préparation du réseau du laboratoire Active Directory, DC01 et DC02 ne parvenaient pas à communiquer correctement.

Depuis DC01 :

```cmd
ping 172.16.0.2
```

Le résultat indiquait :

```text
Impossible de joindre l'hôte de destination.
```

La configuration IP semblait pourtant correcte :

- DC01 : `172.16.0.1/24`
- DC02 : `172.16.0.2/24`

Les deux machines étaient également connectées au même réseau interne VirtualBox :

`AD-LAB`

## Diagnostic

Plusieurs éléments ont été vérifiés :

1. les adresses IPv4 avec `ipconfig /all` ;
2. le masque de sous-réseau ;
3. le réseau interne VirtualBox ;
4. le pare-feu Windows ;
5. la connectivité avec `ping` ;
6. les interfaces réseau virtuelles.

La comparaison des interfaces réseau a finalement permis d'identifier que DC01 et DC02 possédaient la même adresse MAC.

## Cause

Les deux machines virtuelles utilisaient une adresse MAC identique.

Cette situation peut notamment apparaître après la duplication ou le clonage d'une machine virtuelle lorsque l'adresse MAC de l'interface réseau est conservée.

Deux interfaces présentes sur le même réseau doivent disposer d'adresses MAC différentes.

## Solution

DC02 a été arrêté.

Dans VirtualBox, une nouvelle adresse MAC a été générée depuis :

`DC02 > Configuration > Réseau > Adaptateur 1`

Après le redémarrage de DC02, les anciennes entrées ARP ont été supprimées :

```cmd
arp -d *
```

La connectivité a ensuite été testée depuis DC02 :

```cmd
ping 172.16.0.1
```

Puis depuis DC01 :

```cmd
ping 172.16.0.2
```

## Résultat

Les deux machines communiquent désormais correctement.

Le problème ne provenait pas de l'adressage IPv4 ou du réseau interne VirtualBox, mais d'une duplication d'adresse MAC.

## À retenir

Lors du clonage d'une machine virtuelle destinée à fonctionner sur le même réseau que la machine d'origine, il est nécessaire de vérifier que chaque interface réseau possède une adresse MAC unique.

En cas de problème de communication entre deux machines virtuelles malgré une configuration IPv4 correcte, la vérification des adresses MAC fait partie des contrôles à effectuer.
