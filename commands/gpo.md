# Commandes GPO

## Objectif

Cette fiche regroupe les commandes utiles pour appliquer, vérifier et diagnostiquer les stratégies de groupe sur un poste Windows membre du domaine.

---

## Forcer l'actualisation des stratégies

```cmd
gpupdate /force
```

Force Windows à récupérer et appliquer immédiatement les stratégies de groupe.

---

## Afficher les stratégies appliquées

```cmd
gpresult /r
```

Affiche les stratégies appliquées à l'utilisateur et à l'ordinateur.

---

## Afficher uniquement les GPO ordinateur

```cmd
gpresult /scope computer /r
```

Très utile pour vérifier les GPO liées à une OU contenant des postes.

---

## Afficher uniquement les GPO utilisateur

```cmd
gpresult /scope user /r
```

Permet de vérifier les stratégies appliquées au compte utilisateur connecté.

---

## Générer un rapport HTML

```cmd
gpresult /h C:\Temp\gpresult.html
```

Crée un rapport détaillé des stratégies appliquées.

Le fichier peut ensuite être ouvert avec un navigateur.

---

## Vérifier le serveur de connexion

```cmd
echo %logonserver%
```

Affiche le contrôleur de domaine ayant traité l'ouverture de session.

Exemple :

```text
\\DC01
```

---

## Vérifier le domaine du poste

```cmd
systeminfo | findstr /B /C:"Domaine"
```

Permet de confirmer que le poste est bien membre du domaine.

---

## Vérifier l'utilisateur connecté

```cmd
whoami
```

Exemple :

```text
ad\melanie.martin
```

---

## Vérifier les groupes de l'utilisateur

```cmd
whoami /groups
```

Affiche les groupes de sécurité présents dans le jeton de l'utilisateur.

---

## À retenir

Pour diagnostiquer rapidement une GPO :

```text
gpupdate /force
gpresult /scope computer /r
gpresult /scope user /r
echo %logonserver%
whoami
```
