# 🖥️ Déploiement des machines — Lab GNS3

> Configuration des VMs, adressage statique/DHCP, accès SSH et création des comptes administrateurs.

![Topology](image/topology5.PNG)

---

## 📋 Table des matières

- [Inventaire des machines](#-inventaire-des-machines)
- [Adressage des machines](#-adressage-des-machines)
- [Configuration SSH & compte admin](#-configuration-ssh--compte-admin)

---

## 📦 Inventaire des machines

| Hostname | OS | Rôle | Adresse IP | VLAN |
|---|---|---|---|---|
| user1 | Alpine Linux | Utilisateur | DHCP | VLAN 10 — Users |
| user2 | Alpine Linux | Utilisateur | DHCP | VLAN 10 — Users |
| admin | Alpine Linux | Admin | 192.168.20.10/24 | VLAN 20 — Admin |
| srv-dhcp | Alpine Linux | Serveur DHCP | 192.168.30.10/24 | VLAN 30 — Servers |
| srv-dns | Alpine Linux | Serveur DNS interne | 192.168.40.12/24 | VLAN 40 — DMZ |
| srv-files | Alpine Linux | Partage Samba/NFS/SFTP | 192.168.30.20/24 | VLAN 30 — Servers |
| srv-web | Alpine Linux | Serveur Web (Nginx) | 192.168.40.10/24 | VLAN 40 — DMZ |
| srv-proxy | Alpine Linux | Reverse Proxy | 192.168.40.11/24 | VLAN 40 — DMZ |
| admin-gui | Ubuntu 22.04 | Supervision (Prometheus/Grafana) | 192.168.20.2/24 | VLAN 20 — Admin |
| srv-ldap | Rocky Linux | Annuaire LDAP | 192.168.30.30/24 | VLAN 30 — Servers |

---

## 🌐 Adressage des machines

Pour adresser les machines, connectez-vous à la console et éditez les fichiers de configuration réseau. Des tutoriels dédiés sont disponibles en ligne — voici quelques exemples de configuration pour chaque OS utilisé dans ce lab.

---

### 🐧 Alpine Linux

![Exemple config Alpine](image/result12.PNG)
![Exemple config Alpine DHCP](image/result14.PNG)

---

### 🐧 Ubuntu 22.04

![Exemple config Ubuntu](image/result13.PNG)

---

### 🐧 Rocky Linux

![Exemple config Rocky Linux](image/result15.PNG)

---

## 🔐 Configuration SSH & compte admin

Une fois les machines adressées, on configure SSH et on crée un compte administrateur sur chacune d'elles.
Pour mettre les machines a jour et updae les packges regarder le fihier set up lab

---

D'abord un met les hostname

### 🐧 Alpine Linux
Configuration du hostname

exemple :

echo "srv-dhcp" > /etc/hostname
hostname -F /etc/hostname 


### 🐧 Ubuntu
Configuration du hostname

exemple :

hostnamectl set-hostname nom-de-ta-machine

#### Rocky

hostnamectl set-hostname nom-de-ta-machine


#### Installation de SSH
Apres avoir installe ssh selon les differnets machine et os voici les commandes pour les configurer :

PARTIE-SERVEUR

Rocky Linux : sudo systemctl enable --now sshd
Alpine Linux : sudo rc-service sshd start

Bien sur on ne configure ssh que sur les machines serveur

Maintenant on cree le user admin
sudo adduser admin
sudo passwd admin

On 'lajoute au group wheel

Rocky:sudo usermod -aG wheel admin
Alpine : sudo addgroup admin wheel

Ensuite on ouvre le fichier suo avec  : visudo 
. Puis on decomenete la ligne 
%wheel ALL=(ALL) ALL

Puis creer les fichiers de config ssh

Pour l'instant on va d'abord creeer les fichiers et ajouter les cles plus tard :

sudo mkdir -p /home/admin/.ssh
sudo vi /home/admin/.ssh/authorized_keys

Quand l'editeur nano s'ouvre refeermez le tout simplement

Maintenant rendons sur les machines clients cest a dire admin et admin-gui pour generer les cles ssh

ssh-keygen -t ed25519 -C "admin@homelab"

Copiez la cle qui se trouve dans 
~/.ssh/id_ed25519.pub










`



---

*Lab réalisé sur GNS3 — machines Alpine Linux, Ubuntu 22.04 et Rocky Linux.*
