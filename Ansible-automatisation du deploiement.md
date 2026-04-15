## 🚀 Automatisation du déploiement avec Ansible

Si vous êtes arrivés jusqu'ici, vous avez sûrement constaté à quel point la configuration manuelle des serveurs peut être fastidieuse. Dans un environnement de production, tout se passe en SSH — pas d'interface graphique, pas de clics, uniquement la ligne de commande.

C'est là qu'intervient **Ansible**, qui va nous permettre d'automatiser le déploiement du reste de l'infrastructure.

---

### 🎯 Objectif

Les services suivants seront déployés automatiquement via Ansible :

- Serveur proxy
- Serveur web
- Serveur LDAP

Certains éléments resteront configurés manuellement :

- Le partage NFS
- Le service SFTP
- Quelques autres composants non finalisés

La majorité des configurations étant gérée par Ansible, cela simplifie considérablement la gestion de l'infrastructure 😉

---

### 📦 Installation d'Ansible

Sur Alpine Linux, l'installation se résume à une seule commande :

```sh
apk add ansible
```

---

### ⚠️ Limitations du lab actuel

À cause des performances limitées de ma machine et du nombre de technologies déjà déployées dans ce lab, nous rencontrons :

- Une forte latence réseau
- Des déconnexions SSH intempestives
- Des lenteurs générales

En clair : **ma machine a atteint ses limites pour ce lab.**

---

### 📌 La suite

Je démarre un nouveau lab, plus orienté système, où nous découvrirons encore plus de technologies et de bonnes pratiques.

**Suivez la suite sur mon GitHub → [speedcode860](https://github.com/speedcode860)**

À très bientôt pour la suite de l'aventure 🚀🔥
