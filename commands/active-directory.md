# Commandes Active Directory

## Objectif

Cette fiche regroupe les principales commandes PowerShell utilisées pour administrer et diagnostiquer Active Directory dans le laboratoire.

---

## Rechercher un utilisateur

```powershell
Get-ADUser melanie.martin
```

Permet de récupérer les informations de base d'un utilisateur Active Directory.

---

## Afficher des propriétés supplémentaires

```powershell
Get-ADUser melanie.martin -Properties *
```

Affiche l'ensemble des propriétés disponibles pour l'utilisateur.

À utiliser avec précaution car la sortie peut être très longue.

---

## Vérifier l'état de verrouillage d'un compte

```powershell
Get-ADUser test.lockout -Properties LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime |
Select-Object SamAccountName,LockedOut,BadPwdCount,LastBadPasswordAttempt,lockoutTime
```

Permet de vérifier :

* si le compte est verrouillé ;
* le nombre de mauvais mots de passe ;
* la dernière tentative incorrecte ;
* la date de verrouillage.

---

## Rechercher les comptes verrouillés

```powershell
Search-ADAccount -LockedOut
```

Affiche les comptes Active Directory actuellement verrouillés.

---

## Déverrouiller un compte

```powershell
Unlock-ADAccount -Identity "test.lockout"
```

Permet de déverrouiller un compte utilisateur.

---

## Vérifier la politique de mot de passe et de verrouillage du domaine

```powershell
Get-ADDefaultDomainPasswordPolicy
```

Affiche la politique de mot de passe du domaine.

Pour ne voir que les paramètres de verrouillage :

```powershell
Get-ADDefaultDomainPasswordPolicy |
Select-Object LockoutThreshold, LockoutDuration, LockoutObservationWindow
```

---

## Rechercher plusieurs utilisateurs

```powershell
Get-ADUser -Filter *
```

Affiche tous les utilisateurs du domaine.

---

## Rechercher un utilisateur précis avec un filtre

```powershell
Get-ADUser -Filter "SamAccountName -eq 'melanie.martin'"
```

Permet de rechercher un compte à partir de son identifiant.

---

## Voir les groupes d'un utilisateur

```powershell
Get-ADPrincipalGroupMembership melanie.martin
```

Affiche les groupes auxquels appartient l'utilisateur.

---

## Ajouter un utilisateur à un groupe

```powershell
Add-ADGroupMember -Identity "GG_IT" -Members "melanie.martin"
```

Ajoute l'utilisateur `melanie.martin` au groupe `GG_IT`.

---

## Retirer un utilisateur d'un groupe

```powershell
Remove-ADGroupMember -Identity "GG_IT" -Members "melanie.martin"
```

Retire l'utilisateur du groupe.

---

## Vérifier un groupe

```powershell
Get-ADGroup GG_IT
```

Affiche les informations du groupe.

---

## Afficher les membres d'un groupe

```powershell
Get-ADGroupMember GG_IT
```

Affiche les utilisateurs ou groupes membres de `GG_IT`.

---

## Vérifier un ordinateur du domaine

```powershell
Get-ADComputer CLIENT01
```

Permet de vérifier la présence du poste dans Active Directory.

---

## Afficher tous les ordinateurs

```powershell
Get-ADComputer -Filter *
```

Affiche tous les objets ordinateurs du domaine.

---

## À retenir

Les commandes les plus utiles pour du support Active Directory sont :

```text
Get-ADUser
Search-ADAccount
Unlock-ADAccount
Get-ADDefaultDomainPasswordPolicy
Get-ADGroupMember
Get-ADComputer
```
