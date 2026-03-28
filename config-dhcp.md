Configuration dhcp

Maintenant que nos serveurs sont pret a etre adminstres on va commencer par configurer le service dhvp pour que nos machines utilisateur recoivent leurs ip 
Voila les information sur le pool dhcp

USERS (10) 192.168.10.100 – .200 192.168.10.1 192.168.30.11 24h
gateway : 192.168.10.1
dn: 192.168.40.12 

Pour cette config on va utiliser le daemon kea , qui est la version la plus utilise en entreprise et est moderne par rapport a ISC DHCP

d'abord installons kea

apk update
apk add kea-dhcp4
