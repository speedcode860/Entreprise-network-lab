Bon si vous ete arrives jusquici vous avez pu voir comment il est fastidiuex de configurer les serveurs manuelement surtout que dans un envoirnnment de production on aura acce a des serveurs par ssh donc moins evident de juste qcliquer sur icones et de faire ce quon veut .Mais heuresement
l'on a un outil qui va nous permetre dautomatiser led ploiement du res de notre infrastucre , notamment ansible 

Pour ne pas y aller trop en profonduer on va dabord faire simple , on va deployer notre serveur proxy , web , et ldap 

Pour eviter es erreurs de configuration on va configurer certains service manuelemnt notamment , le partage de fichier avec nfs et sftp que lon a pas termine et dautres encores , mais la majorite des configurations eront deployes depuis ansible 
vraiment la vie est plus facile ainsi ;)


Deja on va telecharger le package ansible

apk add ansible 
