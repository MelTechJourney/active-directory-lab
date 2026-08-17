# Jonction de CLIENT01 au domaine Active Directory

## Objectif

Intégrer un poste Windows 11 Professionnel au domaine Active Directory :

    ad.meltech.local

Le poste client pourra ensuite utiliser les comptes du domaine et recevoir les stratégies définies par l'administrateur.

## Machine utilisée

    Nom : CLIENT01
    Système : Windows 11 Professionnel
    IPv4 : 172.16.0.20
    DNS : 172.16.0.1

Le contrôleur de domaine utilisé est :

    Nom : DC01
    IPv4 : 172.16.0.1
    Domaine : ad.meltech.local

## Prérequis

Avant la jonction au domaine, plusieurs éléments doivent être vérifiés :

- CLIENT01 et DC01 doivent être sur le même réseau ;
- CLIENT01 doit pouvoir communiquer avec DC01 ;
- le DNS de CLIENT01 doit pointer vers DC01 ;
- le domaine doit pouvoir être résolu par DNS ;
- CLIENT01 doit utiliser une édition de Windows compatible avec la jonction à un domaine.

## Vérification de la connectivité

Depuis CLIENT01 :

```cmd
ping 172.16.0.1
```

Depuis DC01 :

```cmd
ping 172.16.0.20
```

La communication doit fonctionner dans les deux sens.

## Vérification de la configuration DNS

Depuis CLIENT01 :

```cmd
ipconfig /all
```

Le serveur DNS doit être :

    172.16.0.1

Il est ensuite possible de tester la résolution du domaine :

```cmd
nslookup ad.meltech.local
```

Et la présence des enregistrements permettant de localiser le contrôleur de domaine :

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.ad.meltech.local
```

## Jonction au domaine

Depuis les propriétés système de CLIENT01 :

1. Ouvrir les paramètres avancés concernant le nom de l'ordinateur.
2. Sélectionner **Modifier**.
3. Choisir **Domaine**.
4. Saisir :

       ad.meltech.local

5. Fournir les identifiants d'un compte autorisé à joindre un ordinateur au domaine.

Une fois l'opération validée, Windows confirme l'entrée de CLIENT01 dans le domaine.

Le poste doit ensuite être redémarré.

## Vérification dans Active Directory

Sur DC01, ouvrir :

**Utilisateurs et ordinateurs Active Directory**

L'objet :

    CLIENT01

doit être présent.

Le poste est ensuite déplacé dans l'unité d'organisation prévue :

    ad.meltech.local
    └── Postes
        └── Clients
            └── CLIENT01

## Test d'une connexion utilisateur

Sur CLIENT01, ouvrir une session avec un compte Active Directory.

Exemple :

    melanie.martin@ad.meltech.local

Une fois connecté :

```cmd
whoami
```

Le résultat doit identifier le compte du domaine.

Exemple :

    ad\melanie.martin

## Vérification du contrôleur de domaine utilisé

```cmd
echo %logonserver%
```

Résultat attendu :

    \\DC01

Cette commande permet d'identifier le contrôleur de domaine ayant traité l'ouverture de session.

## Vérification de l'appartenance au domaine

```cmd
systeminfo | findstr /B /C:"Domaine"
```

Le domaine retourné doit être :

    ad.meltech.local

## Vérification des groupes

```cmd
whoami /groups
```

Cette commande permet de vérifier les groupes de sécurité associés à la session de l'utilisateur.

## Résultat

CLIENT01 est membre du domaine `ad.meltech.local`.

Un utilisateur Active Directory peut ouvrir une session sur le poste et être authentifié par DC01.
