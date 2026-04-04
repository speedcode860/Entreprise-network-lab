# 📁 Serveur de fichiers — srv-files

> Centralisation, sécurisation et distribution des données via trois services de partage distincts : Samba, NFS et SFTP.

![Topology](image/topology5.PNG)

---

## 🏛️ Architecture & philosophie

Cette partie se concentre sur la mise en place de `srv-files`. Son rôle est de centraliser et distribuer les données à travers toute l'infrastructure via trois protocoles de partage complémentaires.

La sécurité ne repose pas sur la confiance, mais sur le **contrôle strict**. Toute la configuration s'articule autour du principe du **Moindre Privilège**.

---

## 🔒 Le principe du Moindre Privilège

> N'accorder que l'accès strictement nécessaire — ni plus, ni moins.

- **Accès sélectif** — un utilisateur n'accède qu'aux ressources utiles à sa mission
- **Droits restreints** — si la lecture suffit, l'écriture est interdite
- **Isolation** — une faille sur un service ne doit pas compromettre les autres

Ce principe est appliqué à travers **trois couches de verrouillage** :

### Par la segmentation réseau (VLAN)

- Le flux **NFS** est réservé au VLAN `SERVERS`
- Le flux **Samba** est réservé aux VLANs `USERS` et `ADMIN`
- Le flux **SFTP** est l'unique porte d'entrée pour la `DMZ`

### Par la configuration des services

- **Samba** — le dossier public est forcé en lecture seule pour les utilisateurs
- **SFTP** — chaque utilisateur est enfermé dans une *Chroot Jail* (prison virtuelle) qui l'empêche de remonter à la racine du serveur

### Par les permissions système Linux

- Utilisation de groupes dédiés (`smb-users`, `smb-admins`)
- Chaque dossier appartient à un propriétaire précis avec des droits `755` ou `770` pour bloquer les accès non autorisés au niveau du disque

---

## 📊 Table des partages

| Partage | Protocole | Chemin local (`/srv/`) | Utilisateurs / Zone | Droits |
|---|---|---|---|---|
| Public | Samba | `/shares/public` | USERS, ADMIN | RO (Users) / RW (Admin) |
| Admin | Samba | `/shares/admin` | ADMIN seulement | RW |
| Srv-Data | NFS | `/nfs/data` | SERVERS seulement | RW |
| Web-DMZ | SFTP | `/sftp/web` | Serveur Web (DMZ) | RW — Chrooté |
| Proxy-DMZ | SFTP | `/sftp/proxy` | Reverse Proxy (DMZ) | RW — Chrooté |
| DNS-DMZ | SFTP | `/sftp/dns-ext` | DNS Externe (DMZ) | RW — Chrooté |

![Schéma des partages](image/srv-files.png)

---

## 🗂️ Samba

### 1. Installation
```sh
apk update
apk add samba samba-common-tools
```

### 2. Création des dossiers partagés
```sh
mkdir -p /srv/shares/public
mkdir -p /srv/shares/admin
```

### 3. Création des groupes
```sh
addgroup smb-users
addgroup smb-admins
```

### 4. Attribution des droits
```sh
# Root est propriétaire, les groupes ont l'accès
chown -R root:smb-users /srv/shares/public
chown -R root:smb-admins /srv/shares/admin

# 755 = lecture seule pour le groupe / 770 = accès privé
chmod 755 /srv/shares/public
chmod 770 /srv/shares/admin
```

### 5. Configuration de Samba

Videz le fichier `/etc/samba/smb.conf` et collez la configuration suivante :
```ini
# =================================================================
# CONFIGURATION GLOBALE DU SERVEUR
# =================================================================
[global]
   workgroup = WORKGROUP
   server string = srv-files

   # Force l'authentification par compte utilisateur
   security = user

   # Les utilisateurs inconnus sont traités comme invités
   # Indispensable pour que [public] fonctionne sans mot de passe
   map to guest = Bad User

   log file = /var/log/samba/log.%m
   max log size = 50


# =================================================================
# PARTAGE PUBLIC — LECTURE SEULE POUR TOUS
# =================================================================
[public]
   path = /srv/shares/public
   comment = Partage Public
   browseable = yes
   read only = yes

   # Exception : les admins peuvent écrire
   write list = @smb-admins

   # Accès sans mot de passe (mode invité)
   guest ok = yes


# =================================================================
# PARTAGE ADMIN — ACCÈS PRIVÉ ET CACHÉ
# =================================================================
[admin]
   path = /srv/shares/admin
   comment = Zone Admin

   # Dossier invisible dans la liste des partages réseau
   # Il faut taper manuellement \\IP\admin pour y accéder
   browseable = no

   valid users = @smb-admins
   writable = yes
   guest ok = no
```

### 6. Création des utilisateurs
```sh
# Utilisateur standard (VLAN Users)
adduser -D -H -G smb-users marc
smbpasswd -a marc
smbpasswd -e marc

# Utilisateur admin
adduser -D -H -G smb-admins luc
smbpasswd -a luc
smbpasswd -e luc
```

> 💡 Ajoutez autant d'utilisateurs que nécessaire en suivant le même modèle.

### 7. Démarrage du service
```sh
rc-update add samba default   # Lancement automatique au démarrage
rc-service samba start        # Démarrage immédiat
```

---

## ✅ Tests

Depuis la machine `admin-gui`, entrez l'adresse du serveur dans la barre d'adresse du gestionnaire de fichiers :

![Accès au partage depuis admin-gui](image/result24.PNG)
![Authentification Samba](image/result25.PNG)

> 💡 Le partage peut prendre quelques secondes à s'afficher selon les performances de votre machine.

![Partage public](image/result26.PNG)
![Partage admin](image/result27.PNG)
