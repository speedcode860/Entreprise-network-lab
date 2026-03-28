Configuration dhcp

Maintenant que nos serveurs sont pret a etre adminstres on va commencer par configurer le service dhvp pour que nos machines utilisateur recoivent leurs ip 
Voila les information sur le pool dhcp

USERS (10) 192.168.10.100 – .200 192.168.10.1 192.168.30.11 24h
gateway : 192.168.10.1
dn: 192.168.40.12 

Pour cette config on va utiliser le daemon kea , qui est la version la plus utilise en entreprise et est moderne par rapport a ISC DHCP
Connectez vous au serveur en ssh:

d'abord installons kea

apk update
apk add kea-dhcp4

Apres installation ouvrez le fichier  : /etc/kea/kea-dhcp4.conf

Supprimez tout son contenu pour coller celui ci :

{
"Dhcp4": {
    "interfaces-config": {
        "interfaces": ["eth0"] 
    },

    "control-socket": {
        "socket-type": "unix",
        "socket-name": "/run/kea/kea-dhcp4-ctrl.sock"
    },

    "lease-database": {
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/kea-leases4.csv",
        "lfc-interval": 3600
    },

    "subnet4": [
        {
            "id": 1,
            "subnet": "192.168.10.0/24",
            "pools": [ { "pool": "192.168.10.50 - 192.168.10.150" } ],
            "option-data": [
                {
                    "name": "routers",
                    "data": "192.168.10.1"
                },
                {
                    "name": "domain-name-servers",
                    "data": "192.168.40.12"
                }
            ]
        }
    ],

    "loggers": [
    {
        "name": "kea-dhcp4",
        "output_options": [
            {
                "output": "/var/log/kea-dhcp4.log"
            }
        ],
        "severity": "INFO"
    }
    ]
}
}

 rassurez vous de ces autres points : 

Verifiez que le fichier de log existe et que cest kea le proprietaire
ls -l /var/log/kea-dhcp4.log

Si il n'existe pas alors crreons le comme ci :

touch /var/log/kea-dhcp4.log
chown kea:kea /var/log/kea-dhcp4.log
chmod 664 /var/log/kea-dhcp4.log

Verifeiz l'ip statique :

ip addr show eth0

C'est l'endroit où Kea note les adresses IP qu'il a déjà données.

Vérifier si le dossier existe :

ls -ld /var/lib/kea/

Si le dossier n'existe pas :

mkdir -p /var/lib/kea/
chown kea:kea /var/lib/kea/

Avant de lancer le service "pour de vrai", Kea possède un outil pour vérifier s'il y a des erreurs de frappe ou des problèmes de droits dans ton fichier JSON :

kea-dhcp4 -t /etc/kea/kea-dhcp4.conf

Lancer Kea au démarrage :

rc-update add kea-dhcp4 default


Démarrer le service maintenant :

rc-service kea-dhcp4 start

Vérifier le statut :

rc-service kea-dhcp4 status


MAintenant que cest bon il faut configurer le relais dhcp pour que notre switch core envoit les request dhcp a notre serveur 

Comnectez vous au swicth en ssh 

