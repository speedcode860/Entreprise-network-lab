![TOPOLOGY1](image/topology1.PNG)

# Description

Pour realiser notre reseaqu nous allons d'abord configurer le traffic de donnes , s'assurer que les sous reseau peuvent communiquer
entre eux.Pour se rapprocher d'une architecture de type entreprise , il y a des boucles reseaux et des liens etherchannel afin doptimiser le flux , le routage intervaln est prefere a celui du routage on stick , 
grace a sa capacite de routage plus efficace.

# Table des vlans

# Configuration des vlans

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













