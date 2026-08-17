# Création et application d'une première GPO

## Objectif

Créer une stratégie de groupe afin d'appliquer automatiquement un paramètre de sécurité à CLIENT01.

Cette étape permet de comprendre comment Active Directory centralise la configuration des ordinateurs d'un domaine.

## Qu'est-ce qu'une GPO ?

Une GPO (**Group Policy Object**) est un ensemble de paramètres administratifs pouvant être appliqués à des utilisateurs ou à des ordinateurs Active Directory.

Une GPO peut notamment servir à :

- configurer des paramètres de sécurité ;
- contrôler certaines fonctionnalités Windows ;
- configurer le pare-feu ;
- configurer Microsoft Defender ;
- appliquer des restrictions ;
- déployer certains paramètres aux postes du domaine.

## Organisation utilisée

CLIENT01 est placé dans :

    ad.meltech.local
    └── Postes
        └── Clients
            └── CLIENT01

La GPO est liée à l'OU `Clients`.

Ainsi, les ordinateurs placés dans cette OU peuvent recevoir la stratégie.

## Création de la GPO

Sur DC01 :

1. Ouvrir **Gestionnaire de serveur**.
2. Sélectionner **Outils**.
3. Ouvrir **Gestion de stratégie de groupe**.
4. Naviguer jusqu'à l'OU `Clients`.
5. Créer un nouvel objet GPO et le lier à cette OU.

Nom utilisé :

    GPO-CLIENTS-Securite-Base

## Configuration ordinateur et utilisateur

Une GPO contient deux catégories principales :

    Configuration ordinateur
    Configuration utilisateur

La configuration ordinateur s'applique à la machine indépendamment de l'utilisateur connecté.

La configuration utilisateur s'applique au compte utilisateur concerné.

La première stratégie du laboratoire utilise :

    Configuration ordinateur

## Paramètre configuré

La GPO configure une limite d'inactivité de l'ordinateur.

Chemin :

    Configuration ordinateur
    └── Stratégies
        └── Paramètres Windows
            └── Paramètres de sécurité
                └── Stratégies locales
                    └── Options de sécurité

Paramètre :

    Ouverture de session interactive :
    limite d'inactivité de l'ordinateur

Valeur :

    300 secondes

Après cinq minutes d'inactivité, la session doit être verrouillée.

## Application de la stratégie

Sur CLIENT01, ouvrir une invite de commandes avec les droits nécessaires puis exécuter :

```cmd
gpupdate /force
```

Cette commande force l'actualisation des stratégies de groupe.

## Vérification

Pour afficher les stratégies appliquées à l'ordinateur :

```cmd
gpresult /scope computer /r
```

Dans les objets de stratégie de groupe appliqués doit apparaître :

    GPO-CLIENTS-Securite-Base

## Fonctionnement observé

Le chemin logique de la stratégie est :

    DC01
      │
      ▼
    GPO-CLIENTS-Securite-Base
      │
      ▼
    OU Clients
      │
      ▼
    CLIENT01

La configuration est donc définie de manière centralisée puis appliquée au poste membre du domaine.

## Résultat

CLIENT01 reçoit correctement une stratégie de groupe créée depuis DC01.

Le laboratoire permet maintenant de mettre en pratique :

- l'organisation des ordinateurs dans des OU ;
- la création d'une GPO ;
- la liaison d'une GPO à une OU ;
- l'actualisation des stratégies ;
- la vérification des stratégies effectivement appliquées.
