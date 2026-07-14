# KB-WS-003 - Windows Server installé sans interface graphique

## 📅 Date

10/07/2026

---

## 🎯 Contexte

Lors de la création de la machine virtuelle Windows Server destinée à accueillir Active Directory, le système démarrait uniquement sur une interface en ligne de commande (Server Core), alors que le laboratoire suivait un environnement avec interface graphique.

---

## 🚨 Symptôme

Après l'installation de Windows Server :

- Aucun bureau Windows n'est disponible.
- Le serveur démarre directement sur **SConfig**.
- Le **Server Manager** n'est pas accessible.

---

## 🔍 Diagnostic

L'installation s'est déroulée correctement, mais l'édition sélectionnée était **Windows Server Core** au lieu de **Desktop Experience**.

Depuis Windows Server 2016, Microsoft ne permet plus d'ajouter l'interface graphique après l'installation.

---

## ✅ Cause

Lors de l'installation, l'édition **Server Core** a été sélectionnée par erreur.

---

## 🛠️ Résolution

1. Arrêter la machine virtuelle.
2. Recréer une nouvelle installation de Windows Server.
3. Lors du choix de l'édition, sélectionner :

```text
Windows Server 2022 Standard Evaluation (Desktop Experience)
```

4. Poursuivre normalement l'installation.

---

## ✔️ Vérification

Après le redémarrage :

- Le bureau Windows est disponible.
- Le **Server Manager** s'ouvre automatiquement.
- L'environnement correspond au laboratoire Active Directory.

---

## 💡 Retour d'expérience

Avant de lancer l'installation d'un système d'exploitation, vérifier attentivement l'édition sélectionnée.

Pour un laboratoire Active Directory destiné à l'apprentissage, **Desktop Experience** est généralement le meilleur choix, car il correspond à la majorité des documentations et des formations.

---

## 🏷️ Mots-clés

Windows Server • Server Core • Desktop Experience • VirtualBox • Installation • Active Directory
