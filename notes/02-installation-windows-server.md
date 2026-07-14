# 🖥️ Installation de Windows Server 2022

## 🎯 Contexte

Dans le cadre de la mise en place de mon laboratoire Active Directory, la première étape consiste à installer une machine virtuelle Windows Server qui servira par la suite de contrôleur de domaine.

L'objectif est de disposer d'un environnement propre afin de configurer les services nécessaires à la création d'un domaine Active Directory.

---

## 🎯 Objectif

- Installer Windows Server 2022 dans une machine virtuelle VirtualBox.
- Préparer le serveur avant les premières configurations réseau.
- Installer les dernières mises à jour disponibles avant de poursuivre le laboratoire.

---

## 🛠️ Environnement

- Oracle VirtualBox
- Windows Server 2022
- Réseau interne VirtualBox

---

## 🛠️ Étapes réalisées

### 1. Création de la machine virtuelle

Création d'une nouvelle machine virtuelle dédiée à Windows Server.

> 📸 *Ajouter une capture de la configuration de la VM.*

---

### 2. Installation du système d'exploitation

Montage de l'image ISO de Windows Server puis lancement de l'installation du système.

> 📸 *Ajouter une capture de l'installation.*

---

### 3. Installation des mises à jour

Une fois le système installé, lancement de **SConfig** afin de rechercher et installer les mises à jour disponibles.

Les mises à jour permettent de sécuriser le serveur et de bénéficier des derniers correctifs avant de commencer la configuration d'Active Directory.

> 📸 *Ajouter une capture de SConfig pendant l'installation des mises à jour.*

---

## ⚠️ Difficultés rencontrées

### L'image ISO ne démarrait pas correctement

Lors de la première tentative, la machine virtuelle ne démarrait pas correctement sur l'image ISO de Windows Server.

Après plusieurs vérifications, j'ai constaté que le problème provenait de la configuration de la machine virtuelle. J'ai supprimé le disque virtuel créé lors de la première installation puis recréé la machine en montant manuellement l'image ISO.

Cette manipulation a permis de lancer correctement l'installation de Windows Server.

---

### Découverte de SConfig

Lors du lancement de **SConfig**, je ne savais pas immédiatement quelle option utiliser pour installer les mises à jour.

Après avoir pris le temps de lire les différentes options proposées, j'ai compris qu'il fallait utiliser l'option **T** afin d'installer l'ensemble des mises à jour disponibles.

Cette étape m'a permis de me familiariser avec l'outil d'administration en ligne de commande intégré à Windows Server.

---

## 💡 Ce que j'ai appris

Cette première installation m'a permis de découvrir les spécificités de Windows Server par rapport à Windows 11.

J'ai également compris l'importance de :

- vérifier la configuration d'une machine virtuelle avant de remettre en cause l'image ISO ;
- installer les mises à jour dès la fin de l'installation ;
- prendre le temps de lire les options proposées par les outils d'administration avant d'agir.

---

## 🚀 Prochaine étape

Configurer une adresse IP statique sur le serveur avant l'installation du rôle **Active Directory Domain Services (AD DS)**.
