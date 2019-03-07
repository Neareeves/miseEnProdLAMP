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


## II - Mise en place des fichiers

1. Copiez tous les fichiers du site dans /var/www/html/nomDuProjet.
    * Soit avec FIleZilla
    * Soit en se connectant dans votre explorateur linux (thunar, caja) : `sftp://root@serveur.domain` dans la barre d'adresse
    * Soit en ligne de commande : `scp /chemin/du/dossier root@serveur.domain:/var/www/html/nomDuProjet`

2. Adapter les infos de connexion à la base de données, éventuellement changer la rewriteBase dans le .htaccess (mettre /nomDuprojet/).

3. Créer un utilisateur unix pour le projet : `useradd allocine`;
4. Donner un mot de passe à l'user : `passwd allocine` puis entrer et confirmer le mot de passe

5. Mettre le nouvel utilisateur en propriétaire du dossier du projet : `(sudo) chown -R allocine /var/www/html/nomDuProjet`;
6. Ajouter l'utilisateur au groupe apache pour permettre à apache d'écrire dans les dossiers (pour l'upload, le cache des frameworks etc) : `sudo usermod -a -G www-data  allocine`

Le projet est maintenant accessible à l'adresse http://serveur.domain/nomDuProjet .


## III - Https et sécurité.

### 1 – Https avec lets'encrypt.

Doc let'sencrypt : [https://certbot.eff.org/lets-encrypt/debianstretch-apache](https://certbot.eff.org/lets-encrypt/debianstretch-apache)

1. Installer certbot `(sudo) apt-get install certbot python-certbot-apache -t stretch-backports`
2. Lancer certbot `certbot --apache`
3. Suivre les instructions, en général il faut rentrer le nom de domaine. 

### 2 – Configurer apache pour éviter de lister les dossiers.
1. Éditer le fichier /etc/apache2/apache2.conf , `nano /etc/apache2/apache2.conf` , `vi /etc/apache2/apache2.conf` ou en interface graphique, filezilla
2. Trouver le bloc :
```
<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```
3. Enlever "Indexes" :
```
<Directory /var/www/>
        Options FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```

4. Enregistrer le fichier et relancer apache : `(sudo) service apache2 restart`

Il est aussi possible d'ajouter "Options -Indexes" dans un .htaccess


## IV - Créer un sous domaine

Pour créer un sous-domaine du type allocine.domain.com : 

1. Se placer dans le dossier /etc/apache2/sites-available `cd /etc/apache2/sites-available`
2. Créer le fichier de configuration de base : `touch allocine.domain.com.conf`
3. éditer la configuration : `nano allocine.domain.com.conf`
4. Remplir la configuration, au minimum : 
```
    <VirtualHost *:80>
    	ServerName allocine.domain.com
    	DocumentRoot /var/www/htmlnomDuProjet
    </VirtualHost>
```
5. Activer le site : `(sudo) a2ensite allocine.domain.com.conf`
6. Relancer apache : `service apache2 restart`

7. Créer chez votre hébergeur un sous domaine dans les DNS. Chez OVH : 
    * Cliquez sur votre domaine
    * allez dans "Zone DNS"
    * Cliquez sur "ajouter une entrée"
    * Choisissez "A"
    * Entrez allocine dans le champ sous-domaine
    * Entrez l'ip du serveur dans le champ Cible et validez.

8. Le site devrait être accessible à l'adresse allocine.domain.com. après quelques minutes (propagation des DNS)
