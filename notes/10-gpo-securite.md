# 10 — Sécurisation des postes par stratégies de groupe (GPO)

## Objectif

Cette étape du laboratoire consiste à centraliser plusieurs paramètres de sécurité des postes Windows à l'aide des **stratégies de groupe Active Directory (GPO)**.

Les politiques mises en œuvre concernent notamment :

- le pare-feu Windows Defender ;
- Microsoft Defender Antivirus ;
- la politique de verrouillage des comptes ;
- l'application et la vérification des stratégies depuis un poste client ;
- le diagnostic d'un verrouillage de compte Active Directory.

L'objectif n'est pas uniquement de créer les GPO, mais également de vérifier leur application effective sur `CLIENT01`.

---

# 1. GPO Pare-feu Windows Defender

Une GPO dédiée au pare-feu a été créée puis liée à l'OU contenant les postes clients.

La stratégie permet de centraliser la configuration du pare-feu Windows Defender depuis Active Directory.

![GPO Pare-feu liée à l'OU Clients](../screenshots/06-gpo-parefeu-liee-ou-clients.png)

La configuration du pare-feu est réalisée depuis :

`Configuration ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Pare-feu Windows Defender avec fonctions avancées de sécurité`

![Configuration du pare-feu par GPO](../screenshots/07-configuration-parefeu-gpo.png)

Les différents profils Windows Defender Firewall peuvent ainsi être configurés de manière centralisée.

![Configuration des profils du pare-feu](../screenshots/08-parefeu-profil-domaine-configure.png)

---

## Vérification sur CLIENT01

Avant l'application de la GPO, l'état du pare-feu a été contrôlé directement depuis le poste client.

![État du pare-feu avant application de la GPO](../screenshots/09-parefeu-client-avant-gpo.png)

Une actualisation des stratégies a ensuite été forcée :

```powershell
gpupdate /force
```

![Actualisation de la stratégie Pare-feu](../screenshots/10-gpupdate-parefeu-reussi.png)

L'application de la GPO peut être vérifiée avec :

```powershell
gpresult /r
```

![Résultat GPResult](../screenshots/11-gpresult-parefeu-applique.png)

Enfin, la configuration réellement active du pare-feu peut être consultée avec :

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

![Configuration active du pare-feu sur CLIENT01](../screenshots/12-parefeu-active-store-client01.png)

Cette vérification est importante : l'affichage `NotConfigured` dans certaines commandes ne signifie pas nécessairement que le pare-feu n'est pas actif.

`ActiveStore` permet d'observer la configuration résultante réellement utilisée par Windows après combinaison des différentes sources de stratégie.

---

# 2. Microsoft Defender Antivirus

Une seconde GPO a été créée afin de gérer Microsoft Defender Antivirus sur les postes clients.

La GPO est liée à l'OU `Clients`.

![GPO Defender liée à l'OU Clients](../screenshots/13-gpo-defender-liee-ou-clients.png)

La protection en temps réel de Microsoft Defender a notamment été configurée par stratégie.

![Configuration de la protection en temps réel](../screenshots/14-defender-protection-temps-reel-configuree.png)

L'application de la GPO sur `CLIENT01` a ensuite été vérifiée avec :

```powershell
gpupdate /force
gpresult /r
```

![GPO Defender appliquée](../screenshots/15-gpresult-defender-applique.png)

Une vérification complémentaire a été effectuée directement depuis le poste client après actualisation des stratégies.

![Microsoft Defender après application de la GPO](../screenshots/16-defender-client-apres-gpupdate.png)

---

# 3. Politique de verrouillage des comptes

Une politique de verrouillage des comptes a ensuite été mise en place au niveau du domaine.

Les paramètres retenus pour le laboratoire sont :

| Paramètre | Valeur |
|---|---:|
| Seuil de verrouillage | 5 tentatives |
| Durée du verrouillage | 15 minutes |
| Réinitialisation du compteur | 15 minutes |
| Verrouillage du compte Administrateur | Activé |

![Configuration de la politique de verrouillage](../screenshots/17-gpo-politique-verrouillage-configuree.png)

La stratégie est appliquée au niveau du domaine afin de définir la politique de comptes Active Directory.

---

## Vérification de la politique effective

La politique réellement appliquée au domaine peut être interrogée directement avec le module PowerShell Active Directory :

```powershell
Get-ADDefaultDomainPasswordPolicy |
Select-Object LockoutThreshold, LockoutDuration, LockoutObservationWindow
```

Les valeurs attendues sont :

```text
LockoutThreshold         : 5
LockoutDuration          : 00:15:00
LockoutObservationWindow : 00:15:00
```

![Politique de verrouillage effective](../screenshots/18-politique-verrouillage-effective.png)

Cette vérification permet de ne pas se limiter à la configuration visible dans la console GPMC : elle confirme la politique réellement effective dans Active Directory.

---

# 4. Simulation d'un verrouillage de compte

Afin de tester la politique, un compte utilisateur dédié a été utilisé :

```text
test.lockout
```

Depuis `CLIENT01`, plusieurs mots de passe incorrects ont volontairement été saisis depuis l'écran **Autre utilisateur**.

Après cinq échecs consécutifs, Active Directory a verrouillé le compte conformément à la politique définie.

![Compte verrouillé depuis CLIENT01](../screenshots/19-compte-verrouille-client01.png)

---

## Vérification depuis Active Directory

L'état du compte peut être contrôlé depuis `DC01` avec PowerShell :

```powershell
Get-ADUser test.lockout -Properties LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime |
Select-Object SamAccountName,LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime
```

Cette commande permet notamment d'observer :

- l'état de verrouillage ;
- le compteur de mauvais mots de passe ;
- la dernière tentative incorrecte ;
- l'information de verrouillage du compte.

![Vérification du verrouillage avec PowerShell](../screenshots/20-verification-compte-verrouille-powershell.png)

Le compte apparaît alors comme verrouillé dans Active Directory.

![Compte test verrouillé dans Active Directory](../screenshots/21-compte-test-verrouille-active-directory.png)

---

# 5. Analyse du verrouillage dans les journaux Windows

Un verrouillage de compte ne doit pas uniquement être résolu : son origine doit pouvoir être recherchée.

Sur le contrôleur de domaine, le journal suivant a été consulté :

`Observateur d'événements > Journaux Windows > Sécurité`

L'événement particulièrement intéressant est :

```text
Event ID : 4740
A user account was locked out
```

Cet événement permet d'identifier un verrouillage de compte Active Directory et peut notamment fournir le poste à l'origine de celui-ci.

Dans ce laboratoire, le verrouillage a été provoqué depuis `CLIENT01`.

![Événement 4740 correspondant au verrouillage](../screenshots/22-evenement-4740-verrouillage-client01.png)

Cette information est particulièrement utile dans un contexte de support ou de cybersécurité.

Un verrouillage répété peut par exemple provenir :

- d'un utilisateur saisissant plusieurs fois un mauvais mot de passe ;
- d'identifiants enregistrés avec un ancien mot de passe ;
- d'un service Windows ;
- d'une tâche planifiée ;
- d'un lecteur réseau utilisant d'anciens identifiants ;
- ou d'une tentative d'authentification malveillante.

---

# 6. Déverrouillage du compte

Une fois l'origine du verrouillage identifiée, le compte peut être déverrouillé avec :

```powershell
Unlock-ADAccount -Identity "test.lockout"
```

L'état du compte est ensuite vérifié :

```powershell
Get-ADUser test.lockout -Properties LockedOut |
Select-Object SamAccountName,LockedOut
```

Le résultat attendu est :

```text
SamAccountName : test.lockout
LockedOut      : False
```

![Déverrouillage du compte Active Directory](../screenshots/23-deverrouillage-compte-active-directory.png)

Une nouvelle connexion avec les identifiants corrects depuis `CLIENT01` a permis de confirmer le retour à un fonctionnement normal.

---

# 7. Scénario d'incident reproduit

Le laboratoire permet finalement de reproduire un scénario proche d'un incident rencontré en support informatique :

```text
Utilisateur incapable de se connecter
              ↓
Vérification du compte Active Directory
              ↓
Compte verrouillé
              ↓
Analyse du compteur de mauvais mots de passe
              ↓
Recherche de l'événement 4740
              ↓
Identification du poste à l'origine du verrouillage
              ↓
Déverrouillage du compte
              ↓
Nouvelle tentative de connexion
              ↓
Connexion réussie
```

Cette approche permet de ne pas simplement appliquer une correction, mais de suivre une démarche de diagnostic :

**constater → vérifier → identifier la cause → corriger → valider.**

---

# Compétences mises en pratique

Cette partie du laboratoire a permis de travailler sur :

- la création et la liaison de GPO ;
- la gestion centralisée des postes Windows ;
- Windows Defender Firewall ;
- Microsoft Defender Antivirus ;
- les politiques de comptes Active Directory ;
- les politiques de verrouillage ;
- `gpupdate` et `gpresult` ;
- PowerShell et le module Active Directory ;
- `Get-ADDefaultDomainPasswordPolicy` ;
- `Get-ADUser` ;
- `Search-ADAccount` ;
- `Unlock-ADAccount` ;
- l'Observateur d'événements Windows ;
- l'analyse de l'événement de sécurité `4740` ;
- le diagnostic d'un incident d'authentification Active Directory.
