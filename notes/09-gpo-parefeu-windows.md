# Configuration du pare-feu Windows via GPO

## Objectif

Créer une stratégie de groupe permettant de gérer de manière centralisée la configuration du pare-feu Windows des postes clients du domaine.

La GPO est appliquée à l'OU :

```text
Postes
└── Clients
```

CLIENT01 étant placé dans cette OU, il reçoit automatiquement la stratégie.

---

## Création de la GPO

Depuis DC01, dans la console **Gestion de stratégie de groupe**, une nouvelle GPO est créée :

```text
GPO-CLIENTS-PareFeu
```

Elle est liée directement à l'OU `Clients`.

![GPO Pare-feu liée à l'OU Clients](../screenshots/gpo/06-gpo-parefeu-liee-ou-clients.png)

---

## Accès à la configuration du pare-feu

La configuration du pare-feu est accessible depuis :

```text
Configuration ordinateur
└── Stratégies
    └── Paramètres Windows
        └── Paramètres de sécurité
            └── Pare-feu Windows Defender avec fonctions avancées de sécurité
```

![Configuration du pare-feu dans la GPO](../screenshots/gpo/07-configuration-parefeu-gpo.png)

---

## Configuration du profil de domaine

Le profil de domaine est configuré avec les paramètres suivants :

```text
État du pare-feu : Activé
Connexions entrantes : Bloquer
Connexions sortantes : Autoriser
```

Cette configuration permet de bloquer par défaut les connexions entrantes qui ne correspondent pas à une règle autorisée, tout en permettant au poste d'initier des connexions sortantes.

![Profil de domaine configuré](../screenshots/gpo/08-parefeu-profil-domaine-configure.png)

---

## Vérification initiale sur CLIENT01

Avant de forcer l'actualisation des stratégies, l'état du pare-feu est contrôlé avec PowerShell :

```powershell
Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

![État du pare-feu avant actualisation de la GPO](../screenshots/gpo/09-parefeu-client-avant-gpo.png)

---

## Actualisation des stratégies

La récupération des stratégies de groupe est forcée avec :

```cmd
gpupdate /force
```

La mise à jour de la stratégie ordinateur et de la stratégie utilisateur se termine sans erreur.

![Actualisation des stratégies réussie](../screenshots/gpo/10-gpupdate-parefeu-reussi.png)

---

## Vérification de la GPO appliquée

La commande suivante permet de vérifier les stratégies appliquées à CLIENT01 :

```cmd
gpresult /scope computer /r
```

La GPO `GPO-CLIENTS-PareFeu` apparaît parmi les stratégies appliquées à l'ordinateur.

![GPO Pare-feu appliquée sur CLIENT01](../screenshots/gpo/11-gpresult-parefeu-applique.png)

---

## Vérification de la configuration effective

La première commande PowerShell affichait certaines valeurs comme `NotConfigured`.

Pour consulter la configuration réellement effective du pare-feu, le magasin de stratégie actif est interrogé :

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

Le résultat confirme notamment :

```text
Domain    True    Block    Allow
Private   True    Block    Allow
Public    True    Block    Allow
```

![Configuration effective du pare-feu](../screenshots/gpo/12-parefeu-active-store-client01.png)

---

## Résultat

La GPO `GPO-CLIENTS-PareFeu` est correctement appliquée à CLIENT01.

Le fonctionnement mis en place est le suivant :

```text
DC01
 │
 ▼
GPO-CLIENTS-PareFeu
 │
 ▼
OU Clients
 │
 ▼
CLIENT01
```

La configuration du pare-feu peut désormais être administrée de manière centralisée depuis Active Directory.

Cette étape a également permis de découvrir la différence entre la configuration affichée par défaut par `Get-NetFirewallProfile` et la stratégie réellement effective disponible dans `ActiveStore`.
