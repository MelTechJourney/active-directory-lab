# Installation d'Active Directory Domain Services

## Objectif

L'objectif est de transformer DC01 en premier contrôleur de domaine du laboratoire.

Configuration de DC01 avant installation :

- Nom : `DC01`
- IPv4 : `172.16.0.1`
- Masque : `255.255.255.0`
- DHCP : désactivé
- DNS : `172.16.0.1`

## Installation du rôle AD DS

Depuis le Gestionnaire de serveur :

`Gérer > Ajouter des rôles et fonctionnalités`

Le rôle suivant est installé :

`Services AD DS (Active Directory Domain Services)`

Les fonctionnalités nécessaires sont également ajoutées.

## Promotion en contrôleur de domaine

Après l'installation du rôle AD DS, DC01 est promu en contrôleur de domaine.

L'option sélectionnée est :

`Ajouter une nouvelle forêt`

Le domaine créé est :

`ad.meltech.local`

DC01 devient ainsi le premier contrôleur de domaine de cette nouvelle forêt.

## Services configurés

Lors de la promotion, DC01 assure notamment les fonctions suivantes :

- Active Directory Domain Services ;
- serveur DNS ;
- catalogue global.

Un mot de passe DSRM est également défini pour les opérations de récupération d'Active Directory.

## Vérification de la résolution DNS

La résolution du domaine est testée avec :

```cmd
nslookup ad.meltech.local
```

Le domaine est correctement résolu vers :

```text
172.16.0.1
```

## Vérification des enregistrements SRV

La présence de l'enregistrement permettant de localiser le service LDAP du contrôleur de domaine est vérifiée avec :

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.ad.meltech.local 172.16.0.1
```

Le résultat indique notamment :

```text
port = 389
svr hostname = dc01.ad.meltech.local
```

DC01 est donc correctement publié dans DNS comme contrôleur de domaine proposant le service LDAP.

## Diagnostic DNS

La configuration DNS d'Active Directory est également contrôlée avec :

```cmd
dcdiag /test:dns /s:DC01 /DnsBasic
```

Le test DNS est validé avec succès.

## Résultat

DC01 est maintenant opérationnel en tant que :

- contrôleur de domaine ;
- serveur DNS ;
- catalogue global.

Domaine :

`ad.meltech.local`

Contrôleur de domaine :

`dc01.ad.meltech.local`

Adresse IPv4 :

`172.16.0.1`
