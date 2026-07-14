# WS-001 - Impossible de démarrer sur l'image ISO

## Symptôme

La machine virtuelle ne démarre pas correctement sur l'image ISO de Windows Server.

## Cause

Le disque virtuel créé lors de la première tentative empêchait le démarrage normal de l'installation.

## Résolution

- Supprimer le disque virtuel.
- Recréer la machine virtuelle.
- Monter manuellement l'image ISO.
- Relancer l'installation.

## Vérification

L'installation de Windows Server démarre correctement.

## Mots-clés

Windows Server • VirtualBox • ISO • Installation
