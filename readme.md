# Création de serveur et vhosts

1. Ajouter les sites dans hosts : 127.0.0.1 site1.lan
    + Windows : C:\Windows\System32\drivers\etc\hosts
	+ Mac : /etc/hosts
2. Lancer le serveur depuis la VM et afficher IP : ifconfig qui donne chez moi 10.0.2.15
3.  Redirection de ports dans configuration/Réseau/avancé/Redirection de ports sur la VM dans virtualbox
```
Ssh	tcp	127.0.0.1	2222	10.0.2.15	22
Web	tcp	127.0.0.1	2080	10.0.2.15	80
```
4. Se connecter au serveur via terminal externe : ssh -l student -p 2222 127.0.0.1
5. Mettre librairie à jour : sudo apt-get update
6. Mettre les paquets à jour : sudo apt-get upgrade
7. Installer apache : sudo apt-get install apache2
8. Installer php : sudo libapache2-mod-php
9. Active le mode rewrite : sudo a2enmod rewrite
10. Créer la configuration site1 :
    + cd /etc/apache2/sites-available
    + sudo cp 000-default.conf site1.lan.conf
11. Désactiver et supprimer site par défaut d’apache
    + sudo a2dissite 000-default-conf
    + sudo rm 000-default-conf
12. Editer la configuration site 1 : sudo nano site1.lan.conf
```
    <VirtualHost *:80>
        erverName site1.lan
        DocumentRoot /home/student/site1
        DirectoryIndex index.html
        <Directory /home/student/site1>
            Require all granted
            Options +Indexes
            AllowOverride All
        </Directory>
    </VirtualHost>
```
13. Copier configuration site1 pour site2 : sudo cp site1.lan.conf site2.lan.conf
14. Editer la configuration site 2 : sudo nano site2.lan.conf et remplacer partout site1 par site2 :
    + Raccourci clavier nano pour windows : CTRL + ALTGR + \
    + site1 enter
    + site2 enter
    + a enter
15. Activer les sites : sudo a2ensite site1.lan.conf site2.lan.conf
16. Transfert des sites du pc local vers le serveur via SFTP :
    + Ouvrir le client (FileZilla Client)
    + Fichier/ gestionnaire de sites
    + hôte : 127.0.0.1
    + port : 2222
    + protocole : SFTP
    + type d’authentification : normal
    + identifiant : student
    + mot de passe : student
    + cliquer sur Connexion
    + déplacer le dossier site1 dans /home/student et pareil pour site2
17. Ajouter un fichier .htaccess dans site1 et site2 contenant :
```
RewriteEngine on
RewriteRule ^debug$ /info.php [QSA,L]
RewriteRule ^about/([a-zA-Z_-]*)/([a-zA-Z_-]*) /test.php?lastname=$1&firstname=$2 [QSA,L]
```
> (ajoutez le fichier info.php contenant <?php  phpinfo();  ?>  à la racine de chaque site
18. Pour finir, rechargez apache : sudo service apache2 reload
19. tester sur navigateur :
    + site1.lan:2080
    + site1.lan:2080/debug
    + site2.lan:2080/about/tonNom/tonPrénom
20. En cas de bug, vérifiez les log dans /var/log/apache2/
