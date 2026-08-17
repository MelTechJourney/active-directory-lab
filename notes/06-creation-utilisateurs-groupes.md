# Création des utilisateurs et groupes Active Directory

## Objectif

Créer et organiser des comptes utilisateurs et des groupes de sécurité dans Active Directory afin de reproduire une structure simple d'entreprise.

## Prérequis

- Le domaine `ad.meltech.local` est opérationnel.
- DC01 est contrôleur de domaine.
- Les unités d'organisation ont déjà été créées.

## Structure utilisée

Les utilisateurs sont organisés dans différentes unités d'organisation selon leur fonction :

    Utilisateurs
    ├── IT
    ├── RH
    └── Direction

Cette séparation permet de structurer l'annuaire et facilitera par la suite l'application de stratégies ou la délégation de droits.

## Création d'un utilisateur

Depuis DC01 :

1. Ouvrir **Gestionnaire de serveur**.
2. Aller dans **Outils**.
3. Ouvrir **Utilisateurs et ordinateurs Active Directory**.
4. Sélectionner l'OU correspondant au service.
5. Clic droit → **Nouveau → Utilisateur**.
6. Renseigner le prénom, le nom et l'identifiant de connexion.
7. Définir un mot de passe temporaire.

Exemple de compte créé :

    Mélanie Martin
    melanie.martin@ad.meltech.local

Le compte est placé dans :

    ad.meltech.local
    └── Utilisateurs
        └── IT
            └── Mélanie Martin

## Gestion du mot de passe

Pour un compte utilisateur standard, l'option suivante peut être activée :

> L'utilisateur doit changer le mot de passe à la prochaine ouverture de session.

Cela permet à l'administrateur de fournir un mot de passe temporaire sans connaître le futur mot de passe personnel de l'utilisateur.

## Groupes de sécurité

Des groupes de sécurité sont également créés pour regrouper les utilisateurs selon leur rôle.

Exemple :

    GG_IT

Le compte de Mélanie Martin est ajouté au groupe correspondant au service informatique.

## Pourquoi utiliser des groupes ?

Il est préférable d'attribuer des permissions à des groupes plutôt que directement aux utilisateurs.

Au lieu de :

    Mélanie → Permission
    Alice → Permission
    Guillaume → Permission

on utilise :

    Utilisateurs
        │
        ▼
      Groupe
        │
        ▼
    Permission

Cette méthode simplifie l'administration lorsque le nombre d'utilisateurs augmente.

## Vérification

Les propriétés d'un utilisateur permettent de vérifier son appartenance aux différents groupes.

Depuis un poste membre du domaine, la commande suivante peut également être utilisée :

    whoami /groups

## Résultat

Les utilisateurs sont organisés dans les OU correspondant à leur fonction et peuvent être associés à des groupes de sécurité.

Cette structure servira ensuite à gérer les permissions et les stratégies de manière centralisée.
