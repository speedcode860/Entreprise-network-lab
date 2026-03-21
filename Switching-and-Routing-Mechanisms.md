![TOPOLOGY1](image/topology1.PNG)

# Description

Pour realiser notre reseaqu nous allons d'abord configurer le traffic de donnes , s'assurer que les sous reseau peuvent communiquer
entre eux.Pour se rapprocher d'une architecture de type entreprise , il y a des boucles reseaux et des liens etherchannel afin doptimiser le flux , le routage intervaln est prefere a celui du routage on stick , 
grace a sa capacite de routage plus efficace.

# Configuration des vlans

## Table des vlans

## Configuration du Switch ESW1

### Creation des Vlans 
Vu que c'est un routeur avec un module de commutateur lesw commandes sont un peu differentes.
Sur la ligne de commande entrer :

vlan database
vlan 10 name Users
vlan 20 name Admin
vlan 30 name Servers
vlan 40 name DMZ
vlan 50 name MGMT
vlan 90 name Natif
vlan 99 name Poubelle
end

### Verification de la creation des vlan

show vlan-switch brief

### Affectation des interfaces aux vlans

conf t
int range fa1/1 - 2
switchport mode access
switchport access vlan 10
int range fa1/3-4
switchport mode access 
switchport access vlan 20
end

### Verification de l'affectation des interfaces

show vlan-switch brief

## Configuration du Switch ESW2 et ESW4

Repetez la meme procedure selon la table de vlan

## Configuration du Switch ESW3

Creez juste les vlans 

# Test 

A ce stade les utilisateur du meme vlan doivent pouvoir communiquer 
Ajouter deux machines alpines linux dans l4espace de travail
Connecter les machines aux switch de facon a ce que les machines elles soient sur le meme vlans 

![TOPOLOGY1](image/topology2.PNG)

PC-test1 et pc-test 2 sont connecte chacun a  fa1/1 et fa1/2 de ESW1
Demarrez les machines et lances le terminal

## Configurez l'addressage

Sur la PC-test1 , tapez 
ip addr add 192.168.10.10/24 dev eth0
ip a

Remarquez que votre machine a bien une ip

![TOPOLOGY1](image/result1.PNG)

Faites la meme chose pour PC-test2 mais avec l'adresse 192.168.10.11/24

Effectuez un ping sur l'une des machines pour tester la connectivite 
CA PING!!!!!!!!!!!!!!!!!!!!

![TOPOLOGY1](image/result2.PNG)

# Configuration de Etherchannel et du STP

Maintenant l'on va configurer les liens etherchanell de notre reseau m, les liens etherchannel permettent doptimiser lutilisation des cables reseaux , ce qui va nous permettre davoir deux liens de 100mb ce qui est differnet attention de un lien de 200mb . 

## Table des etherchannels 

Attention pour eviter les erreurs veuyilles shutdown les ports que vous utilise lors de la creation des etherchannel puis les reallumez seulemtn a la fin 

### Configuration du port channel 1

Conformement a la table voici les commandes a entrer sur ESW1 dans le mode de configuration globale  enfin de configurer le port-channel group 1

int range fa1/7 - 8
switchport
shutdown
channel-group 1 mode on
exit
int port-channel 1
switchport mode trunk
switchport trunk allowed vlan all
switchport trunk native vlan 90 
exit
int range fa1/7 -  8
no shutdown 

Pour configurer les aiutres ports channels la procedure reste la meme , il n y aque les parametre qui changent , faites attention a bien respecter les valeurs
qui sont indiquer dans le tableaux sinon vous pouvez avoir des erreurs .

### Verification de l'etherchannel 

Pour verifier etherchannel tapez la commande 

show etherchannel summary

Exemple de sortie pour ESW1

![TOPOLOGY1](image/result3.PNG)

Verifiez pour les autres liens etherchannel , rassurez que la sortie est conforme a la table en dessus.

### Configuration du spanning tree protocole 

L'objectif cest que notre switch de L3 face du routage e=intervlan , il doit donc etre le root pour tout les vlans 

#### Verifions si il est root ou pas 

OUvrez le ter inal de ESW# , entrez la commande :

show spanning-tree brief

Observez , votre switch ESW3 doit etre root dans tout les vlans . Si c'est le cas vous n'avez rien a faire , sinon entrez cette commande , ca va faire de lui le root bridge pour le vlan x

spanning-tree vlan 10 priority 4096

Dans cette exemple il est le root bridge [pour le vlan 10 . Vous n'avez plus qu'a iterer pour quil soit le root bridge dans tout les autres vlans.

### Verifications du spanning  

show spanning-tree brief

Vous pouvez retapez cette commande pour verifier le spanning tree , et ci tout est bien configure alors ESW3 est le root bridge et la liason entre ESW1 et ESW2 est bloquez , vous pouvez verifiez cette informations dans les les commandes de verifications du spanning tree.

### Isolation des ports

Par mesure de securite l'on va mettre les ports non utilise dans le vlan inutilise notamment le vla poubelle , plutot originale comme nom :)

Repertorie les ports non utilise ( attention puisque l'on utilise un routeur avec un module switch les port fa0/x sont des ports de routeur et non des ports de commutateur donc on peut lesignorer)

Rerpertorier les ports commutauer non tuilise puis les agffecter auxx vlans inutilise

conf t
int range fa1/x - y
shutdown
switchport mode access
switchport acces vlan 99

A la fin vous avez une sortie de ce type 

Sortie de ESW1

![TOPOLOGY1](image/result4.PNG)

**N'oubliez de faire ectte mesure de securite  pour le switch ESW4 ;)**

# Configuration du rooutage intervlan

La methode de routage par le switch de niveau trpoios est prefere ici car cest celle qui est utilise dans la plus part des entreprise , en effet le routage on stick a une limte de 50 vlans , ce qui peut tres vitent etre atteint.

# Tableau de vlans

Selon la table de vlan ci dessus nous allons creer les interfaces virtuelles de notre switch de niveau 3 . Vous remarquerez que il n y a pas d'interface de svi qui correspond a la dmz tout simplemet parce que la dmz etant sur un autre sous reseaule trafic pour la dmz va etre achemiimne vers le firewall tout simplement a l'aide de la route par defaut .

Commande a taper pour le vlan 10

int vlan 10
description Gateway vlan 10
ip add 192.168.10.1 255.255.255.0
no shutdown
exit

Faire de meme pour le reste de vlan , qui figurent dans le tableau ci dessus 

Ensuite dans le mode de config globale active le routage avec :

ip routing 

# Verification 

Entrez :
show ip int brief | section Vlan

Vous devez avoir cette sortie 

![TOPOLOGY1](image/result5.PNG)

Faut pas faire attention a la ligne vlan40 , jai fait une petite erreur hihiii

Maintenant l'on va verifier la table de routage

entrer :

show ip route

on a cette sortie

![TOPOLOGY1](image/result6.PNG)

On peut remarquet que il n y a pas de route vers le reseau dmz cest problematique , car ce reseau va herberger nois services web et dns et il faudra bien que nos hotes de notre sous reseau communiquent avec eux . Pour ce faire on va donc ajouter une route par defaut qui va rediger tout les pquets "hors vlan" de notre sous reseauvers le firewall

## Configuration de linterface connecte au firewall et de la route par defaut

Table d'adressage sous reseaux wan 1


dans Esw3 entrz 

conf t
int fa0/0
ip add 10.0.0.1 255.255.255.0
no shutdown
exit
ip route 0.0.0.0 0.0.0.0 fa0/0

Reverifions la table de routage 

![TOPOLOGY1](image/result6.PNG)

Parfait il ne nous reste plus qu'a configurer le firewall pour router les paquets vers la DMZ >Mais avant ca reverifions notre reseaux avec quelques


# Test

![TOPOLOGY1](image/result7.PNG)

Ajoutez d'autres machines Alpines Linux dans notre architecture vous pouvez voir j'ai ajoute pc3 et pc4 
Pc-test3 est connecte au port fa1/3 de notre ESW1 et il a pour ip 192.168.20.10
Pc-test4 est connecte au port fa1/1 de notre ESW2 et il a pour ip 192.168.30.10

Sur chaque machine ajoutez la gateway , sinon pas de routage.
Voici la commande : ip route add default via 192.168.x.1 
Avex x , l'id du vlan

Reffectuez de pings entre les machines .Si tout est bien configure vos ping devraient passer

![TOPOLOGY1](image/result8.PNG)

Dans mon exmeple je ping PC-test3 et Pc-test4.





















