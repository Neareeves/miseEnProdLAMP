# Mise en production d’un site classique : mysql/php, serveur linux (debian) Apache. (stack LAMP).


#### Exemple avec le projet allocine, sur un serveur dédié. Hébergeur OVH


## I - Base de données

### 1 – Créer un utilisateur de base de données et une base associée.

- En ligne de commande :

1. Se connecter au serveur en ssh : `ssh root@serveur.domain`
2. Se connecter à MySQL : `sudo mysql -u root` si pas de mot de passe (connection en root "unix socket")
ou `mysql -u root -p` si il y un mdp root
3. Créer une base de données : ``CREATE DATABASE `allocine`; ``
4. Créer un utilisateur : `CREATE USER 'userallocine'@'localhost' IDENTIFIED BY 'password';`
5. Donner les droits au nouvel user sur la table : `GRANT ALL PRIVILEGES ON allocine.* To 'userallocine'@'localhost' IDENTIFIED BY 'password';`
6. recharger les privilèges : `FLUSH PRIVILEGES;`
7. Quitter mysql : ctrl+c;


- Avec Phpmyadmin : 


1. Comptes d'utilisateur > Ajouter un compte d'utilisateur
2. Remplir toutes les valeurs et cocher en bas "Créer une base portant son nom et donner à cet utilisateur tous les privilèges sur cette base."


 ### 2 – Importer la base

 - En ligne de commande :

1. copier le .sql sur le serveur (filezilla ou en ligne de commande `scp /chemin/du/fichier.sql root@serveur.domain:/chemin/du/fichier.sql`)
2. Se connecter à MySQL : `sudo mysql -u root` si pas de mot de passe (connection en root "unix socket")
ou `mysql -u root -p` si il y un mdp root
3. Se placer dans la base de données : `use allocine`
4. Importer le fichier : `source /chemin/du/fichier.sql`
5. Quitter mysql : ctrl+c;

- Avec Phpmyadmin : 

1. Sélectionner la base
2. En haut, onglet "importer"
3. Uploader le .sql et valider.

Vous pouvez maintenant utiliser l'user 'userallocine' avec le mdp 'password' pour votre connexion à la BDD dans les fichiers PHP.