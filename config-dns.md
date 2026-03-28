Configuration du DNS - BIND9

> Mise en place du service DNS avec BIND9 pour le mappage des noms de domaines et des adresse ip 

![Topology](image/topology5.PNG)

Deja on va commncer par installer BINDconfig

apk add bind bind-tools
rc-update add named default 

Nom FQDN Type Valeur
srv-dhcp.company.local A 192.168.30.10
srv-dns.lab.com A 192.168.40.12
srv-files.company.local A 192.168.30.20
srv-web.lab.com A 192.168.40.10
srv-proxy.lab.com A 192.168.40.11
srv-monitor.company.local A 192.168.20.20
srv-ldap.company.local A 192.168.30.30
gw-core.company.local A 192.168.99.1

Maintenant on va declarer ses zones  coome suit

![Topology](image/result19.PNG)



