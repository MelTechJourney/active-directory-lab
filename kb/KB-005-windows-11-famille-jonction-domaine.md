# KB-005 - Impossible de joindre Windows 11 à un domaine Active Directory

## Problème

Lors de la tentative d'intégration de CLIENT01 au domaine `ad.meltech.local`, l'option permettant de rejoindre un domaine Active Directory était indisponible.

Windows indiquait :

> Vous ne pouvez pas joindre à un domaine un ordinateur exécutant cette édition de Windows.

## Vérifications

Avant d'identifier la cause, plusieurs éléments ont été contrôlés :

- communication entre CLIENT01 et DC01 ;
- configuration IPv4 ;
- serveur DNS configuré sur `172.16.0.1` ;
- résolution DNS de `dc01.ad.meltech.local`.

La connectivité réseau et la résolution DNS fonctionnaient correctement.

## Diagnostic

L'édition de Windows installée sur CLIENT01 a été vérifiée avec :

```cmd
winver
```

L'édition installée était :

`Windows 11 Famille`

## Cause

Windows 11 Famille ne permet pas de joindre le poste à un domaine Active Directory Domain Services.

Le problème n'était donc pas lié au réseau, au DNS ou au contrôleur de domaine, mais à l'édition du système d'exploitation client.

## Solution

Une nouvelle machine virtuelle CLIENT01 est créée avec une édition de Windows compatible avec la jonction à un domaine.

Lors de la création de la nouvelle machine virtuelle, l'installation automatique de VirtualBox est désactivée :

`Proceed with Unattended Installation`

Cette modification permet d'utiliser l'assistant d'installation Windows manuellement et de sélectionner une édition compatible.

## Configuration prévue de CLIENT01

- Nom : `CLIENT01`
- IPv4 : `172.16.0.20`
- Masque : `255.255.255.0`
- DNS : `172.16.0.1`
- Réseau VirtualBox : `AD-LAB`
- Domaine : `ad.meltech.local`

## À retenir

Lorsqu'une option de jonction à un domaine est indisponible sous Windows, l'édition du système d'exploitation fait partie des premières vérifications à effectuer.

Une connectivité réseau et une résolution DNS fonctionnelles ne signifient pas nécessairement que l'édition de Windows dispose des fonctionnalités nécessaires.
