<h1 align="center">Conservation des données à caractère personnel</h1>                   
<p align="center"><strong><i>-- Hacktorney - Tyc-Tac - Cr4Sh --</strong></i><br/>members of Be Cyber Community</p>


***Pour qui ?***
>
>  Le Security Guide est un référentiel destiné à toute personne souhaitant renforcer la sécurité de ses systèmes d'informations (Ordinateurs, serveurs, programmation, sites internet, Wordpress ...), de connaître et comprendre la gouvernance (PCA/PRA ...), mais également de se tenir au courant des éléments réglementaires et législatifs (RGPD, DORA, NIS) en ce contexte cyber assez particulier, même sans connaissances poussées en développement ni en réseau.
>
> Les propos seront donc ***le plus vulgarisés possibles*** afin de vous accompagner, étape par étape, avec un contenu le plus détaillé possible afin de ***contribuer à un Web plus sûr***.



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

***Date de mise à jour : 2024-04-20***

- [ ] [Principes de base](#basic-principles)
- [ ] [Définition de la durée de conservation](#data-retention-period)
- [ ] [Cycle de Vie de la Donnée](#data-life-cycle)
- [ ] [Outils d’aide à la définition des durées de conservation](#tools-retention-period)
- [ ] [Documentation et conformité](#documentation-compliance)
- [ ] [Suppression et archivage des données](#delete-archiving-data)

<hr id="basic-principles" />

## Principes de Base

Les obligations issues du RGPD ne concernent que les données à caractère personnel (nom, prénom, courriel, IP, données de localisation, données biométriques, etc.)

Le RGPD impose que les données à caractère personnel ne soient conservées que le temps nécessaire pour réaliser les objectifs pour lesquels elles ont été collectées. Ce principe de limitation de la conservation assure que les données ne sont pas gardées indéfiniment sans justification valide.

<hr id="data-retention-period">

## Définition de la durée de conservation
La durée de conservation doit être définie par le responsable du traitement (la personne physique ou morale, l'autorité publique, le service ou tout autre organisme qui, seul ou conjointement avec d'autres, détermine les finalités et les moyens du traitement des données à caractère personnel.), basée sur l'objectif du traitement des données. 

Par exemple, les données d'un candidat non retenu pour un emploi peuvent être conservées pendant un maximum de deux ans par le service des ressources humaines, sauf si le candidat demande leur suppression plus tôt.

<hr id="data-life-cycle" />

## Cycle de Vie de la Donnée

Les données personnelles subissent différentes phases au cours de leur cycle de vie :

-	Conservation en base active : Phase durant laquelle les données sont activement utilisées pour atteindre l'objectif initial. Par exemple, les données des employés actifs sont régulièrement utilisées pour la gestion de la paie.
	
-	Archivage intermédiaire : Une fois l'objectif initial atteint, les données peuvent encore servir pour des besoins administratifs comme la gestion de contentieux ou pour respecter des obligations légales (avocats, médecins, autorités publiques etc.).

-	Archivage définitif : Certaines données peuvent être conservées de manière permanente en raison de leur valeur historique ou légale.

La définition des phases dans le cycle de vie des données est importante pour gérer efficacement les données à caractère personnel conformément aux principes du RGPD, notamment en matière de limitation de la conservation et de protection de la vie privée. Dans le cadre des sauvegardes, il est donc nécessaire de déterminer dans quelle étape se trouve une donnée notamment pour en déduire les obligations de conservation ou d’effacement qui peuvent en découler.

<hr id="tools-retention-period">

## Outils d’aide à la définition des durées de conservation

La CNIL fournit des outils et des référentiels pour aider à déterminer la durée de conservation appropriée selon le secteur et le type de traitement. Ces référentiels suggèrent des durées obligatoires, basées sur la législation, ou recommandées, basées sur la doctrine de la CNIL pour la France. 

Si le responsable de traitement est localisé ailleurs en Europe, il est possible que l’autorité de protection des données dont relève le responsable détermine d’autres recommandations des durées ou que d’autres lois nationales impliquent certains délais particuliers. 

Il convient donc d’étudier systématiquement la situation au cas par cas.

Une fois les durées de conservation déterminée, il convient de s’assurer de l’horodatage des données pertinentes et de la sauvegarde et de l’archivage de celui-ci sur l’ensemble des supports choisi.

<hr id="documentation-compliance" />

## Documentation et conformité
Le responsable du traitement doit documenter soigneusement les décisions relatives à la conservation et aux durées dans le registre des activités de traitement. Cette documentation doit inclure les raisons pour les durées choisies, en tenant compte de la finalité du traitement, des obligations légales, et des potentiels besoins de défense en cas de litige.

<hr id="delete-archiving-data" />

## Suppression et archivage des données

Enfin, les politiques de suppression des données doivent être clairement établies pour garantir que les données ne sont pas conservées au-delà de leur période de conservation légitime. 

Les processus d’archivage doivent aussi être sécurisés et conformes aux principes de protection des données par défaut et dès la conception.

Le cas échéant, les différentes obligations doivent être remplies sur l’ensemble des supports de sauvegarde afin de s’assurer du respect du RGPD.

Ressource : [Guide sur la durée de conservation des données par la CNIL](https://www.cnil.fr/sites/cnil/files/atoms/files/guide_durees_de_conservation.pdf)


<hr />

###### Remerciements :

Nous espérons que ce référentiel vous a plu, qu'il aidera le plus de monde possible, alors n'hésitez pas à le partager à toute personne qui en aurait besoin.

Nous le tiendrons à jour le plus régulièrement possible et nous le ferons évoluer sur différents sujet Cyber, alors gardez ce référentiel en favori pour en profiter 👩‍💻👨‍💻

Merci pour votre intérêt et nous vous disons à très bientôt pour plus de contenu.

<hr>

###### Crédits :

- **Tyc-Tac** : Be.Cyber Community

- **Cr4Sh** : Be.Cyber Community - Edu.Cyber

- **Hacktorney** : Be.Cyber Community - lawgitech.eu

