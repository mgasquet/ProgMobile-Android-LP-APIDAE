---
title: Projet
subtitle: Application mobile, API REST
layout: tutorial
lang: fr
---

## Sujet

Votre projet de **developpement mobile Android** se fera en **binôme** ou exceptionnellement en **trinôme**. **Pas plus de membres autorisé**. Si vous êtes quatre, vous faites deux binômes séparés.

Le sujet s'intéresse au développement d'une **application mobile** reliée à une **API REST** proposant un service, comme nous l'avons vu dans le [TD2]({{site.baseurl}}/tutorials/tutorial2.html) et le [TD3]({{site.baseurl}}/tutorials/tutorial3.html). C'est un projet qui se découpe naturellement en mode binôme avec une partie **back-end** au niveau de l'API et une partie **front-end** au niveau de l'application mobile.

### API

La première partie de votre projet consiste au développement d'une **API REST** proposant un service web. Vous pouvez éventuellement reprendre l'idée réalisée lors du projet de développement web PHP. 

Sinon, quelques idées en vrac qui avez déjà été proposées pour le précédent projet :

* Une petite API de vente en ligne (factice) d'un ou plusieurs produits.

* Une API de vote sur des sujets lancés par des utilisateurs.

* Une API où les utilisateurs peuvent créer des tableaux pense-bêtes collaboratifs.

* Une API pour faire des petites annonces et y répondre.

* Une API de Quizz.

Vous êtes totalement libre sur le choix du thème, mais **vous ne pouvez pas reprendre "The Feed" ou un thème de réseau social**. 

Attention toutefois, nous n'avons pas vu comment uploader des fichiers avec une API, contrairement au projet web. Cela est possible, mais si vous voulez rester sur quelque chose de simple, ne choisissez pas un projet où il faut héberger des images, des vidéos, etc. Éventuellement, votre API peut stocker des liens d'images ou de vidéos qui pourront être lus par l'application mobile ! Pour les photos de profil, nous avons vu comment utiliser [Gravatar](https://fr.gravatar.com/).

Pour votre choix, prenez donc quelque chose qui vous plaît, mais qui vous semble faisable techniquement et dans les temps (un peu plus d'un mois). Si vous avez un doute, n'hésitez pas à demander mon avis.

Il y aura quelques contraintes à respecter sur cette partie :

* Pas deux fois le même sujet d'API entre deux groupes. Vous devrez faire valider votre sujet.

* Vous devrez utiliser obligatoirement **API Platform** (avec **Symfony**) comme nous l'avons vu dans le [TD2]({{site.baseurl}}/tutorials/tutorial2.html)

* Au moins **trois entités** (pour l'application et la base de données) et qui ont des **relations**.

* L'API doit pouvoir gérer des **utilisateurs**, donc un système d'inscription, de connexion et de vérification avec `JWT`. POur faciliter les choses, votre `JWT` doit avoir une longue durée de vie. Vous pouvez aussi avoir plusieurs rôles en plus de l'utilisateur "basique", comme un **admin**, etc. 

Attention, il faudra que votre **API** soit bien construite, notamment au niveau de la sécurité :

* Limitation des méthodes accessibles sur certaines entités.

* Contrôle de l'accès à une ressource/méthode selon l'utilisateur (s'il est connecté, s'il est propriétaire de la ressource, admin, etc...)

* Utilisation des assertions sur les propriétés des entités.

* Contrôle des propriétés visibles par l'utilisateur et celles qu'il a le droit d'écrire, selon l'action...

### Application mobile

La deuxième partie du projet consiste à développer une application mobile sous **Android** qui communique et exploite l'API que vous avez développée.

Ici aussi, quelques contraintes :

* L'application doit posséder au moins un écran supplémentaire en plus de celui d'accueil, de connexion et d'inscription.

* L'application doit exploiter toutes les routes proposées par votre API REST.

* Une fois l'utilisateur connecté, l'application ne doit pas redemander à l'utilisateur de se connecter tant que le token n'a pas expiré.

* L'application doit être **internationalisée** avec au moins deux langues disponibles : anglais (par défaut) et français.

* L'application doit être développée sous **JAVA** (pas de Kotlin).

* A la fin, il faudra produire le fichier **.apk** de l'application (application compilée).

En plus du bon fonctionnement de votre application, divers critères seront évalués :

* Design global.

* Ergonomie de l'application.

* Bonne organisation du code.

* Exploitation de notions vues en cours : Intent implicites, différents layouts, boîtes de dialogues et autres composants...

Vous êtes aussi libre d'utiliser, en plus de votre API, une autre API externe. Vous pouvez aussi importer n'importe quelle librairie utile via le `build.gradle`.

## Rendu

Le rendu devra se faire au plus tard le **28 mai** via un dépôt sur Moodle.

Un seul membre du trinôme dépose une archive **zip** nommée selon le pattern : `NomPrenomMembre1-NomPrenomMembre2.zip`. Cette archive devra contenir :

* Les sources de votre API REST **sans le dossier vendor**.

* Les sources de votre **application mobile** ainsi que le fichier **.apk** contenant l'application.

* Un fichier **README** qui contient :

    * Une présentation de l'API REST produite ainsi que sa **documentation**. Il faut expliquer ce qu'elle permet de faire et comment.

    * Une présentation de l'application mobile ainsi que son **manuel d'utilisation**. Globalement, il faut lister tout ce qu'il est possible de faire avec l'application et comment, écran par écran.

    * Si besoin, des indications pour faire fonctionner vos deux projets (précisions sur l'installation, les paramètres à régler, etc).

    * Un récapitulatif de l'investissement de chaque membre du groupe dans le projet (globalement, qui à fait quoi).

Si votre API est hébergée en ligne (sur le serveur **webinfo** ou autre) n'hésitez pas à préciser l'adresse dans le **README**.

## Aide et accompagnement

Le code produit lors du [TD2]({{site.baseurl}}/tutorials/tutorial2.html) et du [TD3]({{site.baseurl}}/tutorials/tutorial3.html) vous aidera grandement à avancer dans votre projet. Vous pouvez vous baser là-dessus, mais pour rappel, vous ne pouvez pas reprendre (de près ou de loin) le même thème !

Pour toute question, vous pouvez me contacter ici : [malo.gasquet@umontpellier.fr](mailto:malo.gasquet@umontpellier.fr)