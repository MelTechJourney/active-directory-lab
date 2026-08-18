# Commandes de diagnostic

## Objectif

Cette fiche regroupe plusieurs commandes utilisées pour diagnostiquer les problèmes rencontrés dans le laboratoire Windows Server et Active Directory.

---

## Vérifier Active Directory et DNS

```cmd
dcdiag
```

Analyse l'état général du contrôleur de domaine.

---

## Tester uniquement le DNS

```cmd
dcdiag /test:dns
```

Permet de vérifier le fonctionnement du DNS nécessaire à Active Directory.

---

## Test DNS ciblé sur DC01

```cmd
dcdiag /test:dns /s:DC01 /DnsBasic
```

Effectue les tests DNS de base sur DC01.

---

## Vérifier la découverte du contrôleur de domaine

```cmd
nltest /dsgetdc:ad.meltech.local
```

Permet de vérifier qu'un poste peut localiser un contrôleur de domaine.

---

## Vérifier le compte connecté

```cmd
whoami
```

Permet de distinguer un compte local d'un compte de domaine.

Exemple domaine :

```text
ad\melanie.martin
```

---

## Vérifier le contrôleur utilisé

```cmd
echo %logonserver%
```

Permet d'identifier le contrôleur de domaine utilisé lors de l'ouverture de session.

---

## Vérifier les GPO appliquées

```cmd
gpresult /scope computer /r
```

Affiche les GPO appliquées à l'ordinateur.

---

## Vérifier Microsoft Defender

```powershell
Get-MpComputerStatus |
Select-Object AntivirusEnabled, RealTimeProtectionEnabled, BehaviorMonitorEnabled
```

Permet de vérifier l'état de Microsoft Defender.

---

## Vérifier la configuration effective du pare-feu

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
```

Affiche la configuration réellement utilisée par le pare-feu Windows.

---

## Vérifier les comptes verrouillés

```powershell
Search-ADAccount -LockedOut
```

Permet de détecter rapidement un compte Active Directory verrouillé.

---

## Vérifier précisément un compte

```powershell
Get-ADUser test.lockout -Properties LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime |
Select-Object SamAccountName,LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime
```

Permet d'analyser un incident de verrouillage.

---

## Déverrouiller un utilisateur

```powershell
Unlock-ADAccount -Identity "test.lockout"
```

Permet de résoudre un verrouillage de compte après diagnostic.

---

## Vérifier les événements de sécurité

Dans l'Observateur d'événements :

```text
Journaux Windows
└── Sécurité
```

Quelques événements utiles :

| ID   | Signification                  |
| ---- | ------------------------------ |
| 4624 | Connexion réussie              |
| 4625 | Échec de connexion             |
| 4740 | Compte utilisateur verrouillé  |
| 4776 | Validation d'identifiants NTLM |

---

## Démarche de diagnostic

Une méthode simple à retenir :

```text
Observer
   ↓
Vérifier la configuration
   ↓
Tester la connectivité
   ↓
Vérifier DNS
   ↓
Vérifier Active Directory
   ↓
Consulter les journaux
   ↓
Identifier la cause
   ↓
Corriger
   ↓
Tester à nouveau
```
