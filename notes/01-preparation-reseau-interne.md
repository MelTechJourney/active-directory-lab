## ⚠️ Difficulté rencontrée : erreur d'adresse IP

Lors de la préparation du réseau interne entre ma VM Windows et ma VM Ubuntu, j'ai configuré une adresse IP statique sur la machine Windows afin de tester la communication entre les deux systèmes.

Après configuration, le test avec la commande `ping` ne fonctionnait pas.

### Vérifications effectuées

- Vérification de la configuration IP de la VM Windows
- Vérification du masque de sous-réseau
- Vérification de la passerelle
- Vérification du réseau interne dans VirtualBox

### Cause identifiée

En relisant ma configuration, j'ai remarqué une erreur de saisie dans l'adresse IP : deux chiffres ne correspondaient pas à l'adresse que je pensais avoir configurée.

### Solution

J'ai corrigé l'adresse IP de la machine Windows, puis relancé le test de connectivité avec `ping`.

Après correction, la communication réseau a fonctionné correctement.

### Ce que j'ai appris

Cette erreur m'a rappelé l'importance de vérifier les paramètres de base avant de chercher une cause plus complexe.  
Une simple erreur dans une adresse IP peut empêcher deux machines de communiquer, même si le reste de la configuration est correct.
