# Sécurisation des postes et comptes via GPO

## Objectif

Poursuivre la sécurisation de l'environnement Active Directory à l'aide de stratégies de groupe.

Cette étape couvre :

- Microsoft Defender Antivirus ;
- la protection en temps réel ;
- la vérification de l'application des GPO ;
- la politique de verrouillage des comptes ;
- la simulation d'un verrouillage utilisateur ;
- le diagnostic depuis Active Directory ;
- l'analyse de l'événement de sécurité 4740 ;
- le déverrouillage du compte.

---

# 1. Microsoft Defender Antivirus

Une GPO dédiée à Microsoft Defender a été créée puis liée à l'OU contenant les postes clients :

```text
GPO-CLIENTS-Defender
```

![GPO Defender liée à l'OU Clients](../screenshots/13-gpo-defender-liee-ou-clients.png)

---

## Configuration de la protection en temps réel

Les paramètres de Microsoft Defender sont accessibles depuis :

```text
Configuration ordinateur
└── Stratégies
    └── Modèles d'administration
        └── Composants Windows
            └── Antivirus Microsoft Defender
                └── Protection en temps réel
```

La protection en temps réel et la surveillance comportementale sont configurées par stratégie.

![Configuration de la protection en temps réel](../screenshots/14-defender-protection-temps-reel-configuree.png)

---

## Vérification de l'application de la GPO

Après actualisation des stratégies :

```cmd
gpupdate /force
```

la GPO appliquée à CLIENT01 est vérifiée avec :

```cmd
gpresult /scope computer /r
```

La stratégie `GPO-CLIENTS-Defender` apparaît dans les stratégies appliquées.

![GPO Defender appliquée](../screenshots/15-gpresult-defender-applique.png)

---

## Vérification de Microsoft Defender sur CLIENT01

L'état de Microsoft Defender est contrôlé avec PowerShell :

```powershell
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled, BehaviorMonitorEnabled
```

Les protections attendues doivent être actives :

```text
AntivirusEnabled            : True
RealTimeProtectionEnabled   : True
BehaviorMonitorEnabled      : True
```

![Microsoft Defender après application de la GPO](../screenshots/16-defender-client-apres-gpupdate.png)

---

# 2. Politique de verrouillage des comptes

Une nouvelle GPO est créée afin de définir une politique de verrouillage pour les comptes Active Directory.

Nom de la stratégie :

```text
GPO-DOMAINE-Politique-Comptes
```

Contrairement aux GPO destinées aux postes clients, cette stratégie est liée directement au domaine :

```text
ad.meltech.local
```

---

## Configuration

Les paramètres suivants sont définis :

| Paramètre | Valeur |
| --- | ---: |
| Seuil de verrouillage du compte | 5 tentatives |
| Durée de verrouillage | 15 minutes |
| Réinitialisation du compteur | 15 minutes |
| Verrouillage du compte Administrateur | Activé |

![Configuration de la politique de verrouillage](../screenshots/17-gpo-politique-verrouillage-configuree.png)

---

## Vérification de la politique effective

La politique réellement active dans le domaine est vérifiée depuis DC01 avec PowerShell :

```powershell
Get-ADDefaultDomainPasswordPolicy |
Select-Object LockoutThreshold, LockoutDuration, LockoutObservationWindow
```

Le résultat attendu est :

```text
LockoutThreshold         : 5
LockoutDuration          : 00:15:00
LockoutObservationWindow : 00:15:00
```

![Politique de verrouillage effective](../screenshots/18-politique-verrouillage-effective.png)

Cette vérification permet de confirmer que les paramètres configurés dans la GPO sont réellement appliqués au domaine.

---

# 3. Simulation d'un verrouillage de compte

Un compte utilisateur dédié au test est créé :

```text
test.lockout
```

Depuis l'écran **Autre utilisateur** de CLIENT01, plusieurs mots de passe incorrects sont volontairement saisis.

![Tentative de connexion incorrecte](../screenshots/19-compte-verrouille-client01.png)

Après plusieurs mauvaises tentatives, le compteur peut être contrôlé depuis DC01 avec PowerShell.

---

## Vérification du compteur de mauvais mots de passe

```powershell
Get-ADUser test.lockout -Properties LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime |
Select-Object SamAccountName,LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime
```

Cette commande permet notamment d'observer :

- le nombre de mauvais mots de passe ;
- la dernière tentative incorrecte ;
- l'état de verrouillage ;
- la date de verrouillage.

![Vérification du compte avec PowerShell](../screenshots/20-verification-compte-verrouille-powershell.png)

---

## Verrouillage du compte

Après cinq tentatives incorrectes, la politique Active Directory est déclenchée.

Le compte `test.lockout` apparaît alors comme verrouillé.

![Compte verrouillé dans Active Directory](../screenshots/21-compte-test-verrouille-active-directory.png)

Le verrouillage peut également être recherché avec :

```powershell
Search-ADAccount -LockedOut
```

---

# 4. Analyse du verrouillage

Le verrouillage est ensuite analysé depuis le contrôleur de domaine.

Dans l'Observateur d'événements :

```text
Journaux Windows
└── Sécurité
```

l'événement suivant est recherché :

```text
ID 4740
```

Cet événement correspond au verrouillage d'un compte utilisateur.

![Événement 4740 correspondant au verrouillage](../screenshots/22-evenement-4740-verrouillage-client01.png)

L'analyse permet notamment d'identifier le compte concerné et le poste depuis lequel le verrouillage a été provoqué.

Dans ce laboratoire, les tentatives incorrectes proviennent de :

```text
CLIENT01
```

---

# 5. Déverrouillage du compte

Une fois l'incident identifié, le compte est déverrouillé depuis DC01 avec :

```powershell
Unlock-ADAccount -Identity "test.lockout"
```

Son état est ensuite contrôlé avec :

```powershell
Get-ADUser test.lockout -Properties LockedOut |
Select-Object SamAccountName,LockedOut
```

Le résultat attendu est :

```text
SamAccountName : test.lockout
LockedOut      : False
```

![Compte Active Directory déverrouillé](../screenshots/23-deverrouillage-compte-active-directory.png)

Une nouvelle ouverture de session sur CLIENT01 avec le bon mot de passe permet de confirmer le retour à un fonctionnement normal.

---

# 6. Scénario d'incident reproduit

Le laboratoire permet de reproduire un scénario courant de support informatique :

```text
Utilisateur incapable de se connecter
              ↓
Vérification du compte Active Directory
              ↓
Compte verrouillé
              ↓
Analyse des mauvaises tentatives
              ↓
Consultation de l'événement 4740
              ↓
Identification de CLIENT01
              ↓
Déverrouillage du compte
              ↓
Nouvelle connexion
              ↓
Connexion réussie
```

La démarche suivie est :

```text
Constater
   ↓
Vérifier
   ↓
Diagnostiquer
   ↓
Identifier la cause
   ↓
Corriger
   ↓
Valider
```

---

# Compétences mises en pratique

Cette partie du laboratoire a permis de travailler sur :

- les stratégies de groupe Active Directory ;
- Microsoft Defender Antivirus ;
- la protection en temps réel ;
- `gpupdate` ;
- `gpresult` ;
- les politiques de verrouillage des comptes ;
- `Get-ADDefaultDomainPasswordPolicy` ;
- `Get-ADUser` ;
- `Search-ADAccount` ;
- `Unlock-ADAccount` ;
- l'Observateur d'événements Windows ;
- l'événement de sécurité `4740` ;
- le diagnostic d'un incident d'authentification Active Directory.
