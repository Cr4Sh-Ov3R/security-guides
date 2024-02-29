##########################################################################################
######                                                                              ######
######                              SECURE YOUR WORDPRESS                           ######
######                              by Be Cyber Community                           ######
######                              -- Tyc-Tac & Cr4Sh --                           ######
######                                                                              ######
##########################################################################################


> Pour qui ?
>
>  Ce support est destiné à toute personnes souhaitant sécuriser son site / blog basé sur un CMS Wordpress, même sans connaissances poussées en développement ni en réseau.
>
> Les propos seront donc le plus vulgarisés possibles afin de vous accompagner, étape par étape, pour la mise en place des bonnes pratiques et l'installation de plugins reconnus en la matière.
>
> Nous vous proposons ici une liste non exhaustives de plugins et outils reconnus dans le domaine de la cybersécurité et qui ont fait leurs preuves. 


> Disclaimer
>
> Par définition un système d'information ne peut être inviolable et la cybersécurité évolue à une vitesse importante. Ce support ne peut garantir une sécurité sans faille face aux risques de piratage.
>
> Nous déclinons donc toute responsabilité quant à la mise en pratique des éléments de ce support, des outils et plugins utilisés, nous ferons cependant de notre mieux pour maintenir ce support à jour au fur et à mesure des évolutions dans le domaine.

-----

# Date de mise à jour 2024-02-28

- [ ] Avant de commencer l'installation //TODO : Tyc-Tac
- [ ] [Installation Best Practice](#best-practice) //TODO : Tyc-Tac
- [ ] [Installation de plugins](#install-plugins)
- [ ] [Hide Login Page](#hide-login-page) 
- [ ] [Limit Brute Force Login](#bf-login)
- [ ] Firewall & scanning 
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



<hr id="hide-login-page" />

# Hide Login Page

Les premières tentatives de brute force login via wordpress se font généralement aux adresses  ``votresite.fr/wp-admin.php`` ou ``votresite.fr/login`` ou ``votresite.fr/wp-login.php`` qui sont les 3 moyens d'accéder nativement à votre interface de connexion.

Il est donc primordial de "cacher" l'accès à cette interface du grand public en la personnalisant grâce à un plugin de type ``WPS Hide Login`` qui vous permettra de personnaliser le lien d'accès.

## Les étapes d'installation

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

