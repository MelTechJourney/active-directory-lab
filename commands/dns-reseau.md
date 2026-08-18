# Commandes DNS et réseau

## Objectif

Cette fiche regroupe les principales commandes utilisées pour vérifier la configuration réseau et le fonctionnement du DNS dans le laboratoire Active Directory.

---

## Afficher la configuration IP

```cmd
ipconfig
```

Affiche les informations principales des interfaces réseau.

---

## Afficher la configuration complète

```cmd
ipconfig /all
```

Permet notamment de vérifier :

* l'adresse IPv4 ;
* le masque ;
* la passerelle ;
* le serveur DNS ;
* l'adresse MAC ;
* l'état DHCP.

---

## Tester la connectivité

```cmd
ping 172.16.0.1
```

Teste la communication avec une machine distante.

---

## Tester un nom DNS

```cmd
ping dc01.ad.meltech.local
```

Permet de vérifier à la fois :

* la résolution DNS ;
* la connectivité réseau.

---

## Résoudre un nom DNS

```cmd
nslookup ad.meltech.local
```

Interroge le serveur DNS configuré sur la machine.

---

## Interroger un serveur DNS précis

```cmd
nslookup ad.meltech.local 172.16.0.1
```

Envoie directement la requête DNS au serveur `172.16.0.1`.

---

## Rechercher le service LDAP Active Directory

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.ad.meltech.local
```

Permet de vérifier que les contrôleurs de domaine sont correctement publiés dans DNS.

---

## Vider le cache DNS

```cmd
ipconfig /flushdns
```

Supprime les entrées DNS mises en cache localement.

---

## Afficher le cache DNS

```cmd
ipconfig /displaydns
```

Affiche les entrées DNS enregistrées dans le cache local.

---

## Afficher la table ARP

```cmd
arp -a
```

Permet de voir les correspondances entre :

* adresses IPv4 ;
* adresses MAC.

Très utile pour diagnostiquer des problèmes de communication sur le réseau local.

---

## Vider la table ARP

```cmd
arp -d *
```

Supprime les entrées ARP locales afin de forcer leur redécouverte.

---

## Vérifier le chemin réseau

```cmd
tracert 172.16.0.1
```

Affiche les équipements traversés pour atteindre une destination.

Dans un réseau interne simple, le chemin est généralement direct.

---

## Vérifier les connexions réseau

```cmd
netstat -ano
```

Affiche :

* les connexions actives ;
* les ports en écoute ;
* les PID des processus associés.

---

## À retenir

Pour un problème de communication Active Directory :

```text
ipconfig /all
ping
nslookup
arp -a
ipconfig /flushdns
```
