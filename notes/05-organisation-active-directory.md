# Organisation d'Active Directory

## Objectif

Après le déploiement du domaine `ad.meltech.local`, une première structure organisationnelle est créée afin de simuler un environnement d'entreprise.

## Structure des unités d'organisation

La structure suivante est mise en place :

```text
ad.meltech.local
│
├── Utilisateurs
│   ├── IT
│   ├── RH
│   └── Direction
│
├── Postes
│   ├── Clients
│   └── Serveurs
│
└── Groupes
```

Les unités d'organisation permettent d'organiser les objets Active Directory et faciliteront notamment l'application de stratégies de groupe.

## Création des utilisateurs

Trois comptes utilisateurs fictifs sont créés.

### Service IT

- Nom : Mélanie Martin
- Identifiant : `melanie.martin`
- OU : `Utilisateurs/IT`

### Ressources humaines

- Nom : Alice Dupont
- Identifiant : `alice.dupont`
- OU : `Utilisateurs/RH`

### Direction

- Nom : Guillaume Martin
- Identifiant : `guillaume.martin`
- OU : `Utilisateurs/Direction`

Les utilisateurs doivent modifier leur mot de passe lors de leur première connexion.

> Les mots de passe utilisés dans le laboratoire ne sont pas documentés dans ce dépôt.

## Création des groupes

Trois groupes globaux de sécurité sont créés :

- `GG_IT`
- `GG_RH`
- `GG_DIRECTION`

Chaque utilisateur est ajouté au groupe correspondant à son service :

```text
melanie.martin
└── GG_IT

alice.dupont
└── GG_RH

guillaume.martin
└── GG_DIRECTION
```

## Différence entre une OU et un groupe

Une unité d'organisation permet principalement d'organiser et d'administrer les objets Active Directory.

Un groupe de sécurité permet de regrouper plusieurs comptes afin de leur attribuer des autorisations.

Ces deux types d'objets remplissent donc des fonctions différentes.

## Gestion future des permissions

Une architecture de type AGDLP pourra être utilisée :

```text
Accounts
   ↓
Global Groups
   ↓
Domain Local Groups
   ↓
Permissions
```

Cette méthode permettra de séparer l'appartenance des utilisateurs à un service des permissions accordées sur les différentes ressources.
