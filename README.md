#Mise en production d’un site classique : mysql/php, serveur linux (debian) Apache. (stack LAMP).


####Exemple avec le projet allocine, sur un serveur dédié. Hébergeur OVH


##I - Base de données

###1 – Créer un utilisateur de base de données.

- En ligne de commande :

1. Se connecter au serveur en ssh : `ssh root@serveur.domain`
2. Se connecter à MySQL : `sudo mysql -u root` si pas de mot de passe (connection en root "unix socket")
ou `mysql -u root -p` si il y un mdp root
3. Créer une base de données : `CREATE DATABASE \`allocine\`;` 
4. Créer un utilisateur : `CREATE USER 'userallocine'@'localhost' IDENTIFIED BY 'password';`
5. Donner les droits au nouvel user sur la table : `GRANT ALL PRIVILEGES ON allocine.* To 'userallocine'@'localhost' IDENTIFIED BY 'password';`;
6. recharger les privilèges : `FLUSH PRIVILEGES;`;
