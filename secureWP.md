<p align="center"> <h1>SECURE YOUR WORDPRESS</h1>                   
<srong>by Be Cyber Community</strong>
<i>-- Tyc-Tac & Cr4Sh --</i> 


***Pour qui ?***
>
>  Ce support est destiné à toute personnes souhaitant sécuriser son site / blog basé sur un **CMS Wordpress**, même sans connaissances poussées en développement ni en réseau.
>
> Les propos seront donc ***le plus vulgarisés possibles*** afin de vous accompagner, étape par étape, pour la mise en place des bonnes pratiques et l'installation de plugins reconnus en la matière.
>
> Nous vous proposons ici une liste non exhaustives de plugins et outils reconnus dans le domaine de la cybersécurité et qui ont fait leurs preuves. 


***Disclaimer***
>
> *Par définition un système d'information ne peut être inviolable et la cybersécurité évolue à une vitesse importante. Ce support ne peut garantir une sécurité sans faille face aux risques de piratage.*
>
> *Nous déclinons donc toute responsabilité quant à la mise en pratique des éléments de ce support, des outils et plugins utilisés, nous ferons cependant de notre mieux pour maintenir ce support à jour au fur et à mesure des évolutions dans le domaine.*

-----

***Date de mise à jour 2024-02-28***

- [ ] Avant de commencer l'installation //TODO : Tyc-Tac
- [ ] [Installation Best Practice](#best-practice) //TODO : Tyc-Tac
- [ ] [Installation de plugins](#install-plugins)
- [ ] [Hide Login Page](#hide-login-page) 
- [ ] [Limit Brute Force Login](#bf-login)
- [ ] [Firewall & scanning](#waf-scan) 
- [ ] Remove WP version
- [ ] Remove Yoast SEO Version
- [ ] Remove Remove Divi Version
- [ ] Remove Cookie Notice Version
- [ ] Remove Lite Speed Version
- Bonnes pratiques sauvegardes + mise à jour.
- Zip + hash (Comment vérifier l'intégrité ex => fichier base lors de mise en ligne + à chaque sauvegarde + garder antériotité)

# Avant de commencer l'installation

Base APACHE NGINX ....


<hr id="best-practice" />

# Installation - Best practice 

## Installation Apache
```bash
#installation des dépendences
apt install ca-certificates apt-transport-https software-properties-common lsb-release curl -y

#installation d'apache
apt install apache2-utils libapache2-mod-security2 apache2 -y
apt info apache2
```
## Configuration apache
### Tout se passe dans le fichier */etc/apache2/conf-available/security.conf* :

```
#Cachez la version du serveur apache
ServerTokens Prod
ServerSignature Off
#Désactivez la méthode TRACE
TraceEnable Off
#Configurez X-Frame-Options
Header always append X-Frame-Options SAMEORIGIN
#Activez X-XSS-Protection
Header always set X-XSS-Protection: "1; mode=block"
#Réduire les risques de sécurité de type MIME
Header always set X-Content-Type-Options: "nosniff"
#HSTS
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
#Sécurisation des cookies
Header always edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure
#Désactivez les protocoles SSL/TLS obsolètes
nano /etc/apache2/mods-enabled/ssl.conf 
SSLProtocol -all +TLSv1.2
SSLCipherSuite HIGH:!aNULL:!MD5
```
### Nous devons ensuite activer certains modules:
```
# Modifcation des entêtes
a2enmod headers 
#Réécriture des urls
a2enmod rewrite
#Activation de ssl pour https
a2enmod ssl
#Activation de php
a2enmod php8.*
#Autres options disponible
#a2enmod cache Pour activer le mode cache 
#a2enmod cache_disk Pour activer le mode cache
systemctl reload apache2
```
### Pour tester en local nous utilisons curl
```bash
curl -v http://localhost:80/ | head
```

> Nous avons correctement configuré notre serveur apache enjoy !!

## PHP
> Nous allons installer php8.3 qui est la dernière version de php

```bash
#Récuperation de la clé
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg

#Ajout aux sources
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

#Mise à jour
apt update

#Installation des paquets php utilisés par Wordpress
apt-get install php8.3 php8.3-cli php8.3-common php8.3-imap php8.3-redis php8.3-snmp php8.3-xml php8.3-mysqli php8.3-zip php8.3-mbstring php8.3-curl libapache2-mod-php8.3 -y

#Voir la version
php -v

#Ajout du fichier phpinfo pour voir notre configuration
echo "<?php phpinfo(); ?>" | tee /var/www/html/phpinfo.php

#Ouvrir la page
http://url/phinfo.php
```
> Comme vous pouvez le voir un certain nombre de choses ne sont pas bonnes d'un point de vu sécurité

### Sécurisation de php
```
#Tout se passe ici
nano /etc/php/8.3/apache2/php.ini

#Désactivation d'un certain nombre de functions inutiles dans notre cas
disable_functions = exec,passthru,shell_exec,system,proc_open,popen,curl_exec,curl_multi_exec,parse_ini_file,show_source,eval,assert,stream_socket_server,stream_socket_accept,stream_socket_client,stream_set_blocking,fsockopen,fputs,fwrite,create_function,pcntl_exec,pcntl_fork,pcntl_signal,pcntl_waitpid,pcntl_wexitstatus,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wifcontinued,pcntl_wstopsig,pcntl_wtermsig,pcntl_strerror,pcntl_get_last_error,pcntl_signal_dispatch,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_async_signals,pcntl_unshare,pcntl_setpriority,pcntl_getpriority
#Désactivation de la version de php
expose_php = Off
#Révocation l'inclusion de fichier
allow_url_include = Off
#Seuls les fichiers dans votre site Web peuvent être inclus
allow_url_fopen = Off
```

> php est sécurisé 

## Installation de Mariadb
```bash
#Installation de Mariadb
apt install mariadb-server -y

#Activation 
systemctl enable mariadb

#Démmarrage
systemctl start mariadb

#Vérification
systemctl status mariadb

#Sécurisation de votre installation avec des mots de passe fort 
mariadb-secure-installation

#Connexion à Mariadb
mysql -u root -p

#Création de nos tables pour Wordpress
```

```sql
CREATE USER 'anyone'@'localhost' IDENTIFIED BY 'YourStrongPasswordHere';
 CREATE DATABASE  tstSecure;
 GRANT ALL PRIVILEGES ON tstSecur.* TO 'anyone'@'localhost';
 FLUSH PRIVILEGES;
 EXIT;
```
## Nous devons ensuite Télécharger Wordpress

```bash
cd /var/www/html

wget https://wordpress.org/latest.zip

unzip latest.zip

rm latest.zip
```
## To be continued lol




<hr id="hide-login-page" />

# Hide Login Page

Les premières tentatives de brute force login via wordpress se font généralement aux adresses  ``votresite.fr/wp-admin.php`` ou ``votresite.fr/login`` ou ``votresite.fr/wp-login.php`` qui sont les 3 moyens d'accéder nativement à votre interface de connexion.

Il est donc primordial de "cacher" l'accès à cette interface du grand public en la personnalisant grâce à un plugin de type ``WPS Hide Login`` qui vous permettra de personnaliser le lien d'accès.

<a id="install-plugin-step"></a>

## Les étapes d'installation d'un plugin

1. Sur votre interface d'administration Wordpress, rendez-vous sur ``Extensions > Ajouter une extension``
2. Dans le champs de recherche tapez ``WPS Hide Login``
3. Sélectionnez le plugin ``WPS Hide Login`` et cliquez sur "plus de détails"

> NB : Pensez à vérifier les informations d'un plugin ou d'un thème avant installation, notamment : 
> - ``Auteur/autrice`` ici WPServeur, NicolasKulka,wpformation
> - ``Dernière mise à jour`` (une mise à jour trop éloignée n'est généralement pas signe de fiabilité)
> - ``Nécessite Wordpress en version`` ET ``Compatible jusqu'à la version``, vous trouverez la version de votre Wordpress sur le coin inférieur droit de votre espace d'administration
- ``Installations actives``, un nombre élevé, avec de bonnes notes est généralement signe d'un plugin de qualité, surtout si la dernière mise à jour est assez récente.

4. Si le plugin vous convient, cliquez sur le bouton ``Installer maintenant``

## L'activation & les réglages

1. Votre module est maintenant installé, il faut maintenant cliquer sur le bouton ``Activer``. 
> NB : Si vous ne voyez pas le bouton Activer ou que vous avez cliqué sur un autre onglet sans faire exprès, il vous suffit de vous rendre dans la partie ``Extensions``, vous retrouverez alors la liste des plugins installés, il vous suffit maintenant de l'activer. 

2. Une fois activé, cliquez sur ``Réglages``sur votre plugin ou rendez-vous dans la partie ``Réglages > WPS Hide Login`` depuis votre menu administrateur.

3. Tout en bas de cette page, vous trouverez la partie liée à votre plugin :
  
  - ``URL de connexion`` qui sera le lien avec lequel vous vous connecterez : modifiez "login" par le mot que vous souhaitez (ici en exemple à ne pas réutiliser : ``super-connexion``)
  
  - ``URL de redirection``qui sera la page vers laquelle seront redirigés les personnes tentant d'accéder aux pages ``votresite.fr/wp-admin.php``, ``votresite.fr/wp-login.php``, ``votresite.fr/login`` (nous garderons dans l'exemple la page 404 qui sert aux pages introuvables)

  - Pensez ensuite à bien ``Enregistrer les modifications``

> NB : À partir de maintenant, rappelez-vous que pour vous connecter, vous devrez utiliser le lien que vous avez indiqué en ``URL de connexion``, dans notre exemple : ``votresite.fr/super-connexion``


<hr id="bf-login" />


# Limit Brute Force Login

*Une attaque par force brute (bruteforce attack) consiste à tester, l’une après l’autre, chaque combinaison possible d’un mot de passe ou d’une clé pour un identifiant donné afin se connecter au service ciblé.* source:[CNIL](https://www.cnil.fr/fr/definition/force-brute-attaque-informatique)

La première chose à faire pour en limiter les effets est de limiter le nombre de tentatives et de bloquer les attaquants sur une période définie afin de les ralentir et de leur compliquer la vie.

Il existe plusieurs manières de faire et plusieurs outils, nous allons donc ici vous en présenter un qui se nomme ``WPS Limit Login``.

De la même manière que lors de l'installation du plugin ``WPS Limit Login``, il faut le rechercher, l'installer et l'activer.

> NB : si vous avez besoin d'un rappel, je ne vais pas rentrer dans le détail, mais vous trouverez la procédure ici => [étapes d'installation d'un plugin](#install-plugin-step)

## Les réglages

Lorsque vous accédez aux réglages de ``WPS Limit Login``, vous tombez sur le premier onglet ``Configuration``, dans lequel vous trouverez tous les paramétrages liés aux tentatives de connexion et aux limites que vous souhaitez fixer. (Nous verrons les autres onglets plus tard).

### Onglet Configuration

1. Définissez le nombre de tentatives que vous souhaitez autoriser avant blocage ainsi que le temps de blocage souhaité pour l'adresse IP ayant fait les tentatives de brute force. 

> NB : Par défaut, 3 tentatives sont autorisées avant blocage et la durée pour le premier blocage est de 20 minutes. (Le temps durant lequel cette adresse IP ne pourra pas recommencer la procédure de connexion.)

2. Paramétrez le nombre d'heures jusqu'à ce que le nombre de tentatives soient réinitialisées. 

> NB : Par défaut, le plugin est paramétré sur 12 heures, ce qui veut dire que, si une adresse IP tente de se connecter plusieurs fois au cours d'une période de 12 heures d'affilés et que les identifiants de connexions sont erronés, les paramètres définis au point 1 seront pris en compte.

3. Paramétrez le nombre de tentatives supplémentaires et la durée de blocage pour cette nouvelles série de tentatives.

> NB : L'adresse IP ayant été bloquée 20 min suite à 3 tentatives de connexion erronées ( données par défaut comme expliqué au point 1), pourra de nouveau faire un certains nombre de tentatives avant d'être à nouveau bloquées selon cette nouvelle règle.
> Par défaut, le plugin autorise 2 tentatives supplémentaire une fois l'adresse IP débloquée. Si ces tentatives sont erronées, l'adresse IP sera cette fois bloquée pour 24 heures.

4. Afin de pouvoir être informé en cas de tentative de brute force, nous vous conseillons d'activer l'envoi d'e-mail à l'administrateur. 

> NB : Attention, un nombre trop élevé ici, peut être dangereux car vous risquez de ne pas voir suffisamment tôt les tentatives. Cependant, un nombre très bas, peut engendrer un grand nombre de mails. N'hésitez pas à tester, pour l'exemple, nous allons le laisser à 2. 

5. Nous allons donc, pour l'exemple, décocher ``Afficher le lien de crédit``.

> NB : Concernant le lien de crédit, nous partons du principe que plus il y a d'informations visible sur un système d'informations (ici pour votre site internet), plus on facilite la reconnaissance / énumération et la découverte de failles potentielles. 
> 
> Attention, il est primordial de soutenir les plugins et tout l'environnement gratuit / open source que vous utilisez et c'est pourquoi nous vous conseillons d'émettre un avis sur un plugin ou outils que vous utiliseriez ou d'en parler autour de vous, mais ici, le fait d'afficher publiquement que le *formulaire de connexion est protégé par WPS Limit Login* pourrait aider lors d'une phase de reconnaissance ce qui serait de notre avis contre productif.

6. Pensez à enregistrer vos modifications avant de changer d'onglet.

### Onglet Liste blanche

Cet onglet vous permet de définir les adresses IP qui n'auront aucune limite de tentatives et qui ne seront jamais bloquées. 

Sauf cas particulier (et auquel cas, vous avez suffisamment de compétence pour gérer ce point), nous vous conseillons de ne poser aucune adresse IP en liste blanche. 

> NB : Vous connaissez votre mot de passe, en cas d'oubli, vous pouvez demander un nouveau mot de passe via le bouton ``Mot de passe oublié`` qui vous enverra un e-mail sur votre adresse d'administration.

### Onglet Liste noire

Cet onglet vous permet de définir les adresses IP que vous connaissez comme malveillantes via les outils de Cyber Threat Intelligence ou car elles apparaîssent souvent dans vos logs ou dans les tentatives de connexion par brute force. 

Par exemple, avec Be Cyber Community, nous avons référencé un grand nombre d'adresses IP malveillantes que vous trouverez ici [Malicious Ip Adresses - Be Cyber Community](https://github.com/duggytuxy/malicious_ip_addresses) et que vous pouvez utiliser pour enrichir votre liste noire.

### Onglet Journal de blocage

Enfin, vous retrouverez ici les statistiques de tentatives de brute force sur votre page de connexion ainsi que le journal de blocage.

<hr id="waf-scan" />

# Firewall & scanning & 2FA

***Firewall***
> Un pare-feu (firewall) sur votre wordpress vous aidera à protéger votre site wordpress des attaques en identifiant et bloquant le trafic malveillant.

***Scanning***
> Un scanner vous permet de bloquer du code ou contenu malveillant, de vérifier si vos fichiers ont été modifiés ...

***2FA***
> Two Factor Authentication (2FA) vous permet de rajouter une couche de sécurité sur votre site Wordpress lors de la connexion

Pour ce faire nous pouvons vous conseiller 2 outils, CrowdSec et Wordfence


## CrowdSec 

De la même manière que précédemment, il faut rechercher le plugin ``CrowdSec``, l'installer et l'activer.

> NB : si vous avez besoin d'un rappel, vous trouverez la procédure ici => [étapes d'installation d'un plugin](#install-plugin-step)

// TODO


## Wordfence

De la même manière que précédemment, il faut rechercher le plugin ``Wordfence Security – Pare-feu, scanner de logiciels malveillants, et sécurité de connexion ``, l'installer et l'activer.

> NB : si vous avez besoin d'un rappel, vous trouverez la procédure ici => [étapes d'installation d'un plugin](#install-plugin-step)

## Les réglages

Une fois le plugin installé, vous devriez arriver sur 