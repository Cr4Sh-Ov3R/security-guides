<h1 align="center">Comment bien configurer ses serveurs</h1>                   
<p align="center"><strong><i>-- Tyc-Tac & Cr4Sh --</strong></i><br/>members of Be Cyber Community</p>


***Pour qui ?***
>
>  Le Security Guide est un référentiel destiné à toute personne souhaitant sécuriser ses infrastuctures, que ce soit au niveau de ses serveurs et/ou son site basé sur un **CMS Wordpress**, même sans connaissances poussées en développement ni en réseau.
>
> Les propos seront donc ***le plus vulgarisés possibles*** afin de vous accompagner, étape par étape, pour la mise en place des bonnes pratiques et l'installation de plugins reconnus en la matière.



***Disclaimer***
>
> *Par définition un système d'information ne peut être inviolable et la cybersécurité évolue à une vitesse importante. Ce support ne peut garantir une sécurité sans faille face aux risques de piratage.*
>
> *Nous déclinons donc toute responsabilité quant à la mise en pratique des éléments de ce support, des outils et plugins utilisés, nous ferons cependant de notre mieux pour maintenir ce support à jour au fur et à mesure des évolutions dans le domaine.*
>
> Le Security Guide est rédigé et mis à disposition gratuitement pour toute personne souhaitant renforcer la sécurité de ses infrastructures. 
>
> Notre objectif est d'aider la communauté à réduire sa surface d'attaque pour contribuer à un Web plus sûr. Par conséquent, ce guide est partageable dans les conditions de la **licence CC BY-NC-ND 4.0 DEED** à des fins informatives et communautaire. Cependant, toute utilisation commerciale de ce référentiel autre que par ses auteurs est ***FORMELLEMENT INTERDITE***.

-----

***Date de mise à jour : 2024-04-19***

- [ ] [Installer et sécuriser Apache](#apache)
- [ ] [Installer et sécuriser Nginx](#nginx)
- [ ] [Installer et sécuriser PHP](#php)
- [ ] [Installer MariaDb pour Wordpress](#mariadb-wordpress)

<hr id="apache" />

# Installer et sécuriser Apache

## Installation d'Apache

La première chose à faire avant de faire des installations ... est de mettre à jour votre système.

> Note : Toutes les commandes sont ici considérées comme étant logué sur l'utilisateur ``root``, si ce n'est pas le cas, switchez en ``root`` ou rajoutez la commande ``sudo`` devant toutes les commandes. 

> Testé en environnement ``DEBIAN 12`` sur VPS

```bash
# Mise à jour du système et mise à jour des dépendances existantes
apt update && apt upgrade -y
```

Ensuite, vous pouvez installer les dépendances dont vous avez besoin.

```bash
# Installation des dépendences
apt install ca-certificates apt-transport-https software-properties-common lsb-release curl -y

# Installation d'Apache
apt install apache2-utils libapache2-mod-security2 apache2 -y
apt info apache2
```

## Créez un groupe pour Apache :

Nous allons créer un groupe et un utilisateur pour Apache 

Avant toute choses, pensez à stoper Apache 

```bash
systemctl stop apache2
```
Vous pouvez maintenant créer un nouveau groupe, un nouvel utilisateur, supprimer l'utilisateur www-data et le groupe www-data.

```bash
# Créons un nouveau groupe
addgroup --system "votre-groupe" 

# Créons un utilisateur pour Apache qui sera rattaché au groupe que vous avez créé :
adduser --system --no-create-home --disabled-login --ingroup "votre-groupe" --disabled-password "votre-utilisateur"
# Cette commande créée un utilisateur système nommé "votre-utilisateur" sans répertoire personnel (--no-create-home), sans possibilité de se connecter (--disabled-login), et le place dans le groupe "votre-groupe".


# Supprimez l'utilisateur www-data :
deluser www-data
# Assurez-vous qu'il n'y a pas de services ou de processus critiques qui dépendent de cet utilisateur avant de le supprimer.

# Supprimez le groupe www-data :
delgroup www-data
```

> Note : Si vous avez une erreur pour la suppression de l'utilisateur ``www-data``, regardez le processus utilisé et retourné dans le message d'erreur afin de vous assurer qu'il n'est pas critique. S'il n'est pas critique, terminez le processus avec la commande ``kill numéroProcessus``

Vu que nous avons créé un utilisateur et un groupe pour notre Apache, il va falloir apporter quelques modifications au fichier envvars:

> Note : Si vous avez peur de faire une erreur lorsque vous modifiez un fichier, il vous suffit de commenter la ligne en ajoutant un ``#`` devant, et de recopier la ligne juste en dessous avec les nouvelles valeurs. 
>
> Dans l'exemple ci-dessous, vous commentez ``#export APACHE_RUN_USER=www-data`` et écrivez juste en dessous ``export APACHE_RUN_USER="votre-utilisateur"``

```bash
# Une fois cela fait, on modifie le fichier envvars
nano /etc/apache2/envvars
```

Dans ce fichier, nous effectuons les modifications suivantes en remplaçant ``www-data`` par les informations de groupe et d'utilisateur que vous avez créé :

```
# Dans le fichier /etc/apache2/envvars

export APACHE_RUN_USER="votre-utilisateur"
export APACHE_RUN_GROUP="votre-groupe"
```

```bash
# On redémarre le serveur Apache
systemctl restart apache2
```

## Configuration apache

### Tout se passe dans le fichier */etc/apache2/conf-available/security.conf* :

Nous allons ensuite renforcer la sécurité de notre Apache en modifiant et ajoutant quelques règles dans notre fichier */etc/apache2/conf-available/security.conf*

```bash
# Nous allons modifier le fichier avec les règles pour le renforcer
nano /etc/apache2/conf-available/security.conf
```

Une fois dans notre fichier, cherchez les lignes correspondantes pour modifier les règles suivantes.

> Note : Si les lignes n'existent pas déjà et que vous avez bien vérifié, rajoutez les lignes manquantes à votre fichier de configuration :

```
# Dans le fichier etc/apache2/conf-available/security.conf

# Cachez la version du serveur apache
ServerTokens Prod
ServerSignature Off

# Désactivez la méthode TRACE
TraceEnable Off

# Configurez X-Frame-Options
Header always append X-Frame-Options SAMEORIGIN

# Activez X-XSS-Protection
Header always set X-XSS-Protection: "1; mode=block"

# Réduisez les risques de sécurité de type MIME
Header always set X-Content-Type-Options: "nosniff"

# HSTS - HTTP Strict Transport Sercurity
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"

# Sécurisation des cookies
Header always edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure
```


### Nous devons ensuite activer certains modules:

Avant d'activer le module ``a2enmod php8.*``, pensez à [installer PHP](#php)

```bash
# Modifcation des entêtes
a2enmod headers 

# Réécriture des urls
a2enmod rewrite

# Activation de ssl pour https
a2enmod ssl

# Activation de php
a2enmod php8.* 

# Autres options disponible
# a2enmod cache Pour activer le mode cache 
# a2enmod cache_disk Pour activer le mode cache

systemctl reload apache2
```

### Nous devons ensuite retirer les protocoles obsolètes

L'activation du module ``a2enmod ssl`` va créer un fichier de configuration */etc/apache2/mods-enabled/ssl.conf *

Il va nous falloir le modifier pour désactiver certains protocoles obsolètes.

```bash
# Désactivez les protocoles SSL/TLS obsolètes
nano /etc/apache2/mods-enabled/ssl.conf 
```

Trouvez les lignes correspondant à ces configurations et modifiez les valeurs.

```
# Dans le fichier /etc/apache2/mods-enabled/ssl.conf
SSLProtocol all -SSLv2 -SSLv3 -TLSv1
SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5:!SSLv3:!SSLv2:!TLSv1
```

Une fois effectué, nous pouvons vérifier que nos configuration ont bien été enregistrées :

```bash
grep -ir SSLProtocol /etc/apache2/*
```

### Pour tester en local nous utilisons curl

Dans certains cas, ``curl`` n'est pas installé, il faudra donc l'installé avec la commande ``apt install curl`` afant de lancer le test.

```bash
curl -v http://localhost:80/ | head
```

> Nous avons correctement configuré notre serveur Apache enjoy !!


<hr id="Nginx" />

# Installer et sécuriser Nginx

## Installation de Nginx

```bash
apt-get install nginx && apt-get install nginx-extras
systemctl enable nginx
systemctl start nginx
systemctl status nginx

curl -I http://127.0.0.1/
# On voit la version de notre serveur nginx
```

```bash
# Désactivez la signature
nano /etc/nginx/nginx.conf
```

```
#Dans le fichier /etc/nginx/nginx.conf

server_tokens off;

# Modifiez la ligne ssl comme suit TLS 1 et TLS 1.1 obsolète
ssl_protocols TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
```

```bash
systemctl reload nginx

curl -I http://127.0.0.1/
# La signature est bien désactivée, mais cela affiche Nginx corrigeons cela

nano /etc/nginx/nginx.conf
```

```
#Dans le fichier /etc/nginx/nginx.conf

# Ajoutez cela en dessous du commentaire basic
more_set_headers 'Server: ';
```

```bash
systemctl reload nginx

curl -I http://127.0.0.1/
# Voilà, parfait cela n'affiche plus Nginx

nano /etc/nginx/nginx.conf
```

```
# Dans le fichier /etc/nginx/nginx.conf

# Ajout des protections pour les headers
more_set_headers "X-Content-Type-Options : nosniff";
more_set_headers "X-XSS-Protection : 1; mode=block";
more_set_headers "X-Download-Options : noopen";
more_set_headers "X-Permitted-Cross-Domain-Policies : none";
more_set_headers "X-Frame-Options : SAMEORIGIN";

# Vous pouvez ajouter CSP
```

```bash
systemctl reload nginx

curl -I http://127.0.0.1/
# Pour la sécurité, c'est tout, mais vous pouvez envisager bien plus au niveau de vos optimisations
```

<hr id="php" />

# Installer et sécuriser PHP

## PHP

> Nous allons installer PHP8.3 qui est la dernière version de php

```bash
# Récuperation de la clé
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg

# Ajout aux sources
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

# Mise à jour
apt update

# Installation des paquets PHP utilisés par Wordpress
apt-get install php8.3 php8.3-cli php8.3-common php8.3-imap php8.3-redis php8.3-snmp php8.3-xml php8.3-mysqli php8.3-zip php8.3-mbstring php8.3-curl libapache2-mod-php8.3 -y

# Voir la version de PHP
php -v

# Ajout du fichier phpinfo pour voir notre configuration
echo "<?php phpinfo(); ?>" | tee /var/www/html/phpinfo.php
```

> ATTENTION : Votre fichier ``phpinfo.php`` révèle beaucoup trop d'information pour le laisser accessible, surtout sur un serveur en ligne.
> 
> Si vous avez ajouter le fichier phpinfo pour voir votre configuration, nous vous conseillons d'aller le supprimer dès que vous n'en aurez plus besoin et si possible, jamais sur un serveur en ligne. 

Dans le cas où vous ayez créé le fichier ``phpinfo.php``, vous pouvez l'utiliser de la sorte :

```
#Ouvrir la page
http://votreurl/phinfo.php
```

> Comme vous pouvez le voir un certain nombre de choses ne sont pas bonnes d'un point de vu sécurité

Pour supprimer le ``phpinfo.php`` si vous l'avez créé précédemment :

```bash
# Supprimer le fichier phpinfo.php
rm /var/www/html/phpinfo.php
```


### Sécurisation de php

```bash
# Tout se passe ici
nano /etc/php/8.3/apache2/php.ini
```

> Note : Dans le cas où vous ayez créer un fichier ``phpinfo.php`` précédemment, nous vous conseillons de rajouter ``,phpinfo`` à la fin de la ligne ``disable_function``

```
# Désactivation d'un certain nombre de fonctions inutiles dans notre cas

disable_functions = exec, shell_exec, system, passthru, popen, proc_open, symlink, eval, assert, pcntl_exec, pcntl_fork, pcntl_signal, pcntl_waitpid, pcntl_wexitstatus, pcntl_wifexited, pcntl_wifstopped, pcntl_wifsignaled, pcntl_wifcontinued, pcntl_wstopsig, pcntl_wtermsig, pcntl_strerror, pcntl_get_last_error, pcntl_signal_dispatch, pcntl_sigprocmask, pcntl_sigwaitinfo, pcntl_sigtimedwait, pcntl_async_signals, pcntl_unshare, pcntl_setpriority, pcntl_getpriority

# Désactivation de la version de php
expose_php = Off

# Révocation l'inclusion de fichier
allow_url_include = Off

# Seuls les fichiers dans votre site Web peuvent être inclus
allow_url_fopen = Off
```

> Félicitations PHP est maintenant sécurisé 

<hr id="mariadb-wordpress" />

# Installer MariaDb pour Wordpress

## Installation de Mariadb

```bash
# Installation de Mariadb
apt install mariadb-server -y

# Activation 
systemctl enable mariadb

# Démarrage
systemctl start mariadb

# Vérification
systemctl status mariadb

# Sécurisation de votre installation avec des mots de passe fort 
mariadb-secure-installation
# Par défaut, votre utilisateur root n'a pas de mot de passe, il vous faut en rentrer un le plus robuste possible pour cet utilisateur.

# Connexion à Mariadb
mysql -u root -p
```

```sql
--Création de nos tables pour Wordpress
CREATE USER 'anyone'@'localhost' IDENTIFIED BY 'YourStrongPasswordHere';
 CREATE DATABASE  tstSecure;
 GRANT ALL PRIVILEGES ON tstSecur.* TO 'anyone'@'localhost';
 FLUSH PRIVILEGES;
 EXIT;
```

## Nous devons ensuite Télécharger Wordpress

```bash
# Déplacez-vous dans le dossier html
cd /var/www/html

# Télécharger la dernière version de Wordpress
wget https://wordpress.org/latest.zip

# Dézipper le fichier reçu
unzip latest.zip

# Supprimer le fichier latest.zip après l'avoir dézippé
rm latest.zip

# Déplacer tous les fichiers contenus dans le dossier wordpress directement dans le dossier html
mv wordpress/* .

# Enfin supprimer le dossier Wordpress
rm -r wordpress/

```

> Note : Dans le dossier ``/var/www/html`` vous avez un fichier ``index.html`` qui est installé par défaut, il vous faudra le déplacer (ou le supprimer) afin que votre serveur puisse se lancer correctement sur votre site Wordpress.



<hr />

***Remerciements :***

Ce référentiel à été réalisé afin de permettre à toute personne, même peu à l'aise avec le code, de pouvoir améliorer la sécurisation de ses infrastructures, de manière la plus simple possible, en ce contexte cyber assez particulier.

Nous espérons que ce référentiel vous a plu, qu'il aidera le plus de monde possible, alors n'hésitez pas à le partager à toute personne qui en aurait besoin.

Nous le tiendrons à jour le plus régulièrement possible et nous le ferons évoluer sur différents sujet Cyber, alors gardez ce référentiel en favori pour en profiter 👩‍💻👨‍💻

Merci pour votre intérêt et nous vous disons à très bientôt pour plus de contenu.

<hr>

***Crédits :***

- **Tyc-Tac** : Be.Cyber Community

- **Cr4Sh** : Be.Cyber Community - Edu.Cyber
