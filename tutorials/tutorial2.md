---
title: TD2 &ndash; Développement d'une API REST
subtitle: API, REST, API Platform, PHP, Symfony
layout: tutorial
lang: fr
---

## Introduction

Dans ce TD, nous allons mettre en place une **API REST** pour l'application **The Feed** que nous avions développé lors des cours de développement web. Nous avions déjà brièvement abordé cette notion lors de ce cours, mais pas de panique, vous n'avez pas besoin d'avoir terminé les TD de web pour réaliser ce TD, nous allons utiliser un nouvel outil dédié à la création d'API REST avec **Symfony**.

Votre objectif est donc de développer cette API afin de pouvoir l'utiliser dans l'application mobile **The Feed** que vous allez développer lors du TD3 et ainsi avoir un sérieux conçurent à Twitter ! (non)

Tout d'abord, petite mise au point sur les **API** et plus particulièrement sur les **API REST**.

De nos jours, les architectures qui séparent activement la partie `back-end` (serveur, routage, services, accès aux données) de la partie `front-end` (ce qui est rendu niveau client, pages html, etc...) sont de plus en plus privilégiées. En effet, une telle séparation permet notamment d'utiliser un même serveur applicatif avec plusieurs technologies clientes (une application sur smartphone, plusieurs sites web, etc...).

Dans ce fonctionnement, le serveur ne doit alors que renvoyer des données (généralement au format `JSON`, ou bien parfois `XML`) mais il ne se charge pas du rendu de la page. C'est alors les technologies clientes qui, une fois les données récupérées auprès du serveur, les utilisent pour mettre à jour leur interface. On appelle alors le programme côté back-end une `API` pour `Application Programming Interface`. Cela signifie en fait que ce programme est lui-même un `service` qui sert à exécuter des actions et récupérer de l'information, mais pas de document.

Dans le monde des **API**, il existe un **style architectural** nommé `REST` pour **representational state transfer**. Dans cette architecture on parle de **ressources** pour faire référence aux différentes entités de notre service (publications, utilisateurs, commentaires...). Ces ressources doivent être désignées à travers des routes formulées de manière précise, par exemple :

* `/utilisateurs` : désigne tous les utilisateurs

* `/utilisateurs/1` : désigne l'utilisateur 1

* `/publications` : désigne toutes les publications

* `/publications/3` : désigne la troisième publication

* `/utilisateurs/1/publications` : désigne toutes les publications de l'utilisateur numéro 1

* `/utilisateurs/1/publications/5` : désigne la 5ème publication de l'utilisateur numéro 1

Grossièrement, on retrouve le schéma `/{ensemble}/{id}/...` etc...

Pour manipuler ces ressources, on utilise les méthodes `HTTP` suivantes :

* `GET` : Récupère les données de la ressource désignée par la route. On utilise généralement cette méthodes sous deux formes : soit pour récupérer un ensemble de ressources (liste) ou bien pour récupérer une ressource précise.

* `POST` : Crée une ressource dans l'ensemble désignée par la route avec les informations fournies dans le corps de la requête.

* `PUT` : Remplace complétement la ressource désignée par la route avec les informations fournies dans le corps de la requête (tous les attributs doivent donc être spécifiés, comme pour `POST`).

* `PATCH` : Met à jour partiellement les données de la ressource désignée par la route avec les informations fournies dans le corps de la requête (seul les attributs qui ont besoin d'être mis à jour doivent être spécifiés).

* `DELETE` : Supprime la ressource désignée par la route.

Donc, par exemple, on pourrait avoir :

* `GET /publications` : Renvoie toutes les publications.

* `GET /publication/1` : Renvoie les informations de la publication ayant pour identifiant 1.

* `POST /publication` : Créé une nouvelle publication (et renvoie ses informations).

* `DELETE /utilisateurs/3` : Supprime l'utilisateur numéro 3.

Bien entendu, pour une ressource donnée, le développeur n'est pas obligé d'implémenter toutes les méthodes. Parfois, il n'est pas souhaitable que toutes les opérations soient disponibles pour agir sur une ressource.

Dans les requêtes de type **création / modification** (`POST`, `PUT`, `PATCH`), le client envoie des données dans le corps de la requête, généralement sous le même format que les réponses de l'API (donc, généralement, du `JSON`). Ce corps de données est appelé `payload` et diffère du système clé-valeur que vous connaissiez jusqu'ici. Côté serveur, on ne récupère pas un tableau, mais plutôt tout un document de données (`JSON`, `XML`, etc...).

En plus de cela, une `API REST` doit être **sans état** (stateless), c'est-à-dire que le serveur **ne doit pas stocker d'informations sur l'état du client**. Donc, par exemple, l'utilisation d'une session est interdite. À la place, le client peut stocker des données sous la forme de `token` qui peuvent être lus et vérifiés par le serveur. C'est notamment grâce à ce système qu'on pourra authentifier nos utilisateurs et ajouter de la sécurité sur certaines routes.

## Mise en place avec Symfony

Pour développer le plus simplement possible notre **API**, nous allons utiliser un outil appelé **API Platform** exploitable au travers du framework **Symfony** que vous avez appris à manipuler cette année.

Cet outil est assez puissant, car il permet de centraliser une grande partie de la logique de l'API au niveau des classes **entités**. Il y a peu de code à écrire dans d'autres classes (sauf pour certaines opérations spécifiques plus complexes) et pas du tout de contrôleur à mettre en place (du moins, pas dans notre cas). À partir des données des entités et des différentes annotations, **API Platform** se charge de générer les routes et appliquer les différentes opérations de récupération, de sauvegarde, de vérification, etc...

Vous allez vite vous rendre compte qu'il est possible de configurer beaucoup d'aspects de l'API au travers des annotations. D'ailleurs, nous n'allons pas explorer toutes les possibilités qu'offre l'outil aujourd'hui, mais vous pourrez pousser plus loin dans votre projet !

### Création du projet

Tour d'abord, il va falloir créer un projet avec **Symfony**. Nous pouvons faire cela simplement en utilisant l'outil **composer**.

<div class="exercise">

1. Pour le placement du dossier du projet, vous avez **trois choix**, selon votre situation :

    * Si vous souhaitez utiliser le serveur de l'IUT : rendez-vous dans le dossier `public_html` pour que votre projet soit accessible publiquement au travers du serveur `webinfo`.

    * Si vous êtes sur votre machine et que **PHP est installé**, vous pourrez créer le dossier n'importe où et utiliser le **serveur local de Symfony** à télécharger ici :

        * [Windows](https://github.com/symfony-cli/symfony-cli/releases/download/v5.5.2/symfony-cli_windows_386.zip)
        * [macOS](https://github.com/symfony-cli/symfony-cli/releases/download/v5.5.2/symfony-cli_darwin_all.tar.gz)
        * [Linux](https://github.com/symfony-cli/symfony-cli/releases/download/v5.5.2/symfony-cli_linux_386.tar.gz)

    Vous placerez le fichier `symfony` dans le dossier du projet une fois créé, nous verrons comment l'utiliser. Vous pouvez aussi utiliser ce serveur Symfony local sur les machines l'IUT.

    * Si vous êtes sur votre machine, vous pouvez aussi utiliser un programme de serveur local (`XAMPP`, `apache`, `Wamp`, `UwAmp`, `Mamp`). Il faudra simplement créer le projet dans un dossier accessible par votre serveur.

2. Dans le répertoire où vous souhaitez placer le dossier du projet, exécutez la commande suivante :

    ```bash
    composer create-project symfony/skeleton:"6.2.*" the_feed_api
    ```

    Si vous êtes sur votre machine, il faudra bien entendu avoir installé **composer** : [téléchargement sur le site](https://getcomposer.org/download/).

3. Aussi, **si vous travaillez sur le serveur web de l'iut**, assurez-vous qu'il dispose de tous les droits nécessaires par rapport à votre dossier `public_html` :

   ```bash
   setfacl -R -m u:www-data:r-w-x ~/public_html
   setfacl -R -m d:u:www-data:r-w-x ~/public_html
   ```

   Il peut y avoir quelques permissions non accordées, ce n'est pas grave.

4. Ouvrez le répertoire du projet avec votre **IDE** favori (de préférence, `PHPStorm`).

5. Téléchargez le [fichier d'accès au serveur]({{site.baseurl}}/assets/TD2/htaccess), renommez-le `.htaccess` et placez-le dans le sous-dossier `public`.

6. Téléchargez un [deuxième fichier d'accès]({{site.baseurl}}/assets/TD2/htaccess2), renommez-le `.htaccess` et placez-le à la racine de votre projet. Le rôle de ce fichier est capital, car il permet de protéger vos fichiers de configuration pour qu'ils ne soient pas lisibles par tout le monde ! (par exemple, éviter d'exposer le mot de passe BDD...)

7. Testez que votre projet a bien été initialisé en vous rendant à l'adresse correspondant à votre situation :

    * Sur le serveur de l'IUT : [https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/](https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/)

    * Ou bien sur votre serveur local : [https://localhost/chemin_dossier_projet/public/](https://localhost/chemin_dossier_projet/public/)

    * Ou bien en utilisant le serveur de `Symfony` : placez le fichier `symfony` issu de l'archive téléchargé précédemment dans le dossier du projet puis exécutez la commande `./symfony serve`. Le serveur est alors accessible à cette adresse : [https://localhost:8000/](https://localhost:8000/)

</div>

### Installation d'API Platform

Si tout fonctionne, nous allons pouvoir installer **API Platform** et commencer le développement ! Il faudra aussi configurer la **base de données** pour la relier à votre base de l'IUT ou bien à celle de votre serveur local, si vous travaillez sur votre machine personnelle.

<div class="exercise">

1. Depuis la racine du projet, installez le package correspondant :

    ```bash
    composer require api
    ```

    Répondez `y` (yes) à la question demandée.

2. Comme nous l'annonce `API Platform`, nous devons maintenant configurer la base de données. Rendez-vous dans le fichier `.env`, supprimez ou commentez les lignes `DATABASE_URL`.

3. Pour utiliser une base `MySQL`, ajoutez cette ligne :

    `DATABASE_URL=mysql://username:password@ip:port/nom_base`

    Pour les paramètres :

    * `username` : votre nom d'utilisateur pour accéder à votre base de données. À l'IUT, il s'agit du même login que pour accéder aux machines.

    * `password` : votre mot de passe pour accéder à la base. À l'IUT, vous devriez connaître ce mot de passe. Sur votre serveur local, à vous de voir. Parfois ce mot de passe est vide, dans ce cas, il suffit de laisser ce champ vide dans la ligne de configuration.

    * `ip` : l'adresse IP de la base de données. À l'IUT, il s'agit de `webinfo.iutmontp.univ-montp2.fr`. Sur votre machine personnelle, sur votre serveur local, `127.0.0.1`.

    * `port` : le port de la base de données. Sauf paramétrage serveur, il s'agit de `3306`.

    * `nom_base` : le nom de la base de données. À l'IUT, il s'agit encore de votre login, sinon, vous pouvez mettre l nom que vous voulez (doctrine se chargera de la créer).

    **Attention**, si vous comptez déposer votre projet sur un repository git **public**, utilisez plutôt un fichier `.env.local` à la place, qui ne sera ignoré lors des commits (pour ne pas exposer vos mots de passe).

4. Exécutez la commande suivante pour créer la base de données (inutile si vous travaillez sur la base de l'IUT) :

    ```bash
    php bin/console doctrine:database:create
    ```

5. En vous rendant dans le dossier du projet, videz le cache :

    ```bash
    php bin/console cache:clear
    ```

    Retenez bien cette commande, vous en aurez besoin plusieurs fois afin que l'application puisse prendre en compte certains changements effectués.

6. Rendez-vous sur votre site à l'adresse : [https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/](https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/). Adaptez l'URL selon votre situation (avec le serveur local de `Symfony`, il ne faut pas mettre le "public" dans l'URL). Si cela fonctionne, tout est prêt. Vous devriez voir une page liée à **API Platform**.

</div>

## Les publications

Nous allons commencer par créer le code correspondant à la ressource **publication** (les feedies!). Pour cela, nous allons utiliser l'ORM `doctrine` pour faciliter la création de l'entité (génération du code de base de la classe et du repository associé).

Nous pourrons très rapidement tester cette première ressource grâce au récapitulatif donné par `API Platform` et via l'outil `Postman`. Dans un second temps, nous allons configurer l'entité pour fixer des règles plus précises.

Pour le moment, nos publications n'auront pas d'auteurs. Nous reviendrons sur cet aspect quand nous ajouterons les **utilisateurs**.

### Création de l'entité

Pour générer le code de base lié à l'entité publication puis le synchroniser avec la base de données, `Symfony` puis `doctrine` fournissent un ensemble de commandes afin de faire tout cela de manière interactive, en console. Concernant `Symfony`, il faut importer un nouveau `bundle` (composant).

<div class="exercise">

1. Toujours depuis la racine du projet, installez le package suivant :

    ```bash
    composer require symfony/maker-bundle --dev
    ```

2. Exécutez la commande suivante :

    ```bash
    php bin/console make:entity
    ```

    Dans le programme de création de l'entité, répondez aux questions de la manière suivante :

    * Nom de l'entité : Publication

    * Marquer la classe comme une ressource liée à API Platform

    * Ajouter une propriété `message` de type `string` et de longueur `200` (`null` non autorisé).

    * Ajouter une propriété `datePublication` de type `datetime` (`null` non autorisé)

3. Allez observer le code de la nouvelle classe créée dans `src/Entity`.

4. Videz le cache.
</div>

Comme vous pouvez le constater, en plus des annotations liées à doctrine que vous devez déjà connaître, une annotation `#[ApiResource]` est présente. Elle permet simplement d'indiquer à l'application que cette entité est une ressource de l'API. Avec cette configuration simple, notre ressource est manipulable avec toutes les méthodes HTTP.

Vous constaterez aussi que `Symfony` a généré un attribut `id` par défaut qui jouera le rôle d'identifiant unique des publications. Cet identifiant est autoincrémenté, par défaut.

Nous allons maintenant synchroniser cette classe avec la base de données.

<div class="exercise">

1. **Si vous travaillez sur la base de l'IUT**, il y a des chances pour que vous ayez deja des tables et des données importantes dans votre base. Pour ne pas tout chambouler et que doctrine n'écrase pas ces tables, vous pouvez ajouter une l'annotation `#[ORM\Table(name: 'nouveau_nom')]` au-dessus de la classe pour donner un nom custom à la table qui va être créée. Ici, choisissez `publication_api` :

    ```php
    #[ORM\Table(name: 'publication_api')]
    class Publication {
        ...
    }
    ```

    Ensuite, au niveau du fichier `config/paclage/doctrine.yaml`, ajoutez le paramètre suivant dans `dbal` :

    ```yml
    doctrine:
        dbal:
            ...
            schema_filter: ~(_api|doctrine_migration_versions)$~
    ```

    Cela permettra de ne prendre en compte que les tables qui finissent par `_api` (et donc de ne pas supprimer les autres).

    Enfin, si vous avez déjà une table `doctrine_migration_version` dans votre base, supprimez-la.


2. Pour déclencher la mise à jour de la structure de la base, exécutez les commandes suivantes :

    ```bash
    php bin/console make:migration
    php bin/console doctrine:migrations:migrate
    ```

3. Rendez-vous sur l'interface de gestion de votre base (par exemple, via `phpMyAdmin`) et observez la nouvelle table. Si vous utilisez la base de l'IUT, vous pouvez y accéder via [ce lien](https://webinfo.iutmontp.univ-montp2.fr/my/).
</div>

### Premières requêtes

Tout est en place pour faire nos premières requêtes ! Nous pouvons manipuler les **publications** via la route `/api/publications` à partir de la racine de notre site. Mais tout d'abord, nous allons visualiser les détails de notre nouvelle ressource.

<div class="exercise">

1. Rechargez la page web de présentation de l'API : [https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/](https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/)

2. Explorez la documentation des différentes méthodes proposées. Vous pouvez même envoyer des requêtes avec le bouton `Try it out`.

</div>

Pour aller déclencher notre `API`, nous n'allons pas directement utiliser l'interface web proposée par `API Platform`, mais plutôt un logiciel nommé `Postman` (dont nous avions parlé lors du dernier TP de développement web).

Ce logiciel va permettre de paramétrer et d'envoyer des requêtes de manière interactive et de visualiser le résultat très simplement. À l'IUT, il est installé sur vos machines, chez-vous, vous pouvez le télécharger [ici](https://www.postman.com/downloads/?utm_source=postman-home).

Nous allons donc tenter de manipuler notre ressource `publication` à l'aide de ce logiciel!

<div class="exercise">

1. Allumez **postman**. L'application vous propose de créer un compte, mais vous n'en avez pas besoin. Cliquez simplement sur "**Skip signing in and take me straight to the app**" tout en bas.

2. Sur l'interface, créez un nouvel onglet et paramétrez-le ainsi :

    * Dans la liste déroulante du choix des méthodes, sélectionnez `POST`.

    * Juste à côté, le champ permet de rentrer l'URL de la ressource à viser. Par exemple (à adapter) : [https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/publications](https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/publications)

    * Comme mentionné plus tôt, avec les requêtes de **création / modification**, nous avons besoin d'un `payload` au format `JSON`. Pour le configurer, rendez-vous dans `Body` puis sélectionnez l'option `raw` et enfin, à droite, sélectionnez `JSON` dans la liste déroulante (à la place de `Text`).

    * Remplissez la zone de saisie des données avec le `payload` suivant :

    ```json
    {
        "message": "Hello world!",
        "datePublication": "2023-04-07T21:18:27.568Z"
    }
    ```

3. Cliquez sur "**Send**" et observez le résultat. La publication devrait vous être renvoyée (avec son identifiant) ! Ajoutez-en quelques autres.

4. Ouvrez un nouvel onglet et faites en sorte de récupérer la liste de toutes les publications (il n'y a pas de `payload` à mettre dans ce cas...).

5. Ouvrez un nouvel onglet et faites en sorte de récupérer une publication précise. Ici il n'y a encore pas de `payload`, l'identifiant doit être précisé au niveau de l'URL.

6. Ouvrez un nouvel onglet et faite en sorte de supprimer une publication. 
</div>

### Configuration plus poussée

À ce stade, vous avez pu tester diverses routes pour manipuler les publications, mais vous avez pu constater quelques soucis de logique. En effet, la **date** est un paramètre rentré par le client. Ce qui implique qu'il doit connaître précisément la date d'envoi, mais aussi qu'il peut la falsifier (par exemple, mettre 2050 pour être la publication la plus récente...). D'ailleurs, la date qui vous ai donné plus tôt dans le `payload` est antérieure à aujourd'hui ! Comme l'identifiant, il faut donc trouver un moyen de générer automatiquement cette date et ne pas permettre son écriture par le client.

De plus, on aimerait aussi ajouter le comportement suivant :

* Faire en sorte que l'ordre des publications renvoyées soit de la plus récente à la plus ancienne.

* Limiter les messages entre 4 et 200 caractères.

Pour nous aider à tout cela, nous pouvons utiliser les fameuses **annotations**. Voici celles qui pourront vous aider :

* `#[Assert\...](param1 : ..., param2: ...)` : à placer au-dessus d'une propriété de la classe. Permet de réaliser une `assertion` (vérification d'une condition) sur le contenu de la propriété avant de la sauvegarder. Si la condition n'est pas vérifiée, l'application renvoie une erreur (avec un message explicatif)! Les paramètres dépendent du type de l'assertion. Quelques exemples :

    * `#[Assert\Email]` : vérifie que la propriété est une adresse email bien formatée.

    * `#[Assert\Count](min : x, max: y)` : vérifie que la collection possède bien entre `x` et `y` éléments.

    * `#[Assert\Length(min: x, max: y)` : vérifie que la propriété a une taille entre `x` et `y` caractères.

    * `#[Assert\Regex](pattern: ...)` : vérifie que la propriété vérifie l'expression régulière spécifiée.

    * `#[Assert\NotBlank]` : vérifie que la propriété est bien présente dans le `payload`. Cela peut paraître redondant avec le fait que la propriété ne peu pas être nulle dans la base, mais avec cette assertion la vérification est faite au niveau de l'application et non pas d côté de la base (ce qui économise une requête.)

    * `#[Assert\Blank]` : vérifie que la propriété n'est pas présente dans le `payload`. Cela aura un intérêt si on veut autoriser la présence d'une propriété dans le `payload` avec certaines méthodes seulement.


    * `#[Assert\NotNull]` : vérifie que la propriété n'est pas nulle (du côté de l'application). Cela sginifie que la propriété est présente, mais a une valeur nulle.

    Bref, il en existe des tas. On peut aussi ajouter un paramètre `message` pour préciser un message d'erreur customisé en cas d'échec. Il est bien sûr possible d'apposer plusieurs annotations d'assertions au-dessus d'une propriété. Vous pouvez retrouver la liste des types d'assertions disponibles [ici](https://symfony.com/doc/current/reference/constraints.html).

    Au niveau `PHP`, il faut importer les assertions ainsi :

    ```php
    use Symfony\Component\Validator\Constraints as Assert;
    ```

* `#[ApiProperty(param1: ..., param2: ...)]` : à placer au-dessus d'une propriété de la classe. Permet de configurer la présentation de la propriété et ajouter certaines règles. Par exemple :

    * `writable: true/false` : autorise ou interdit l'écriture de cette propriété lors de la création/modification (par défaut, autorisé si non précisé). Si la propriété est présente dans le payload, elle est ignorée.

    * `readable: true/false` : renvoie ou non la propriété lorsqu'on lit la ressource (par défaut, autorisé).

    * `required : true/false` : oblige ou non l'écriture de la propriété lors de la création/modification. On précise cette propriété dans le cas où la logique à appliquer est la même pour toutes les opérations (on verra que ce n'est pas toujours souhaitable). On préférera plutôt utiliser les assertions `Blank` ou `NotBlank`.

    * `description : ...` : permet de décrire le rôle de la propriété plus en détail (pour la documentation destinée aux personnes souhaitant utiliser l'API).

* Dans l'annotation `#[ApiResource]` au-dessus de la classe, il est possible de rajouter un paramètre `order` pour spécifier comment sont ordonnés les résultats d'une requête renvoyant une collection de cette ressource. On le spécifie ainsi : `#[ApiResource](order : ["attribut1" => "ASC ou DESC", "attribut2" => "ASC OU DESC", ...])`. On trie les résultats par rapport au premier attribut spécifié puis, en cas d'égalité, par rapport au second, et ainsi de suite (similaire au `ORDER BY` en SQL). Les valeurs `ASC` ou `DESC` permettent de spécifier le sens du tri (croissant ou décroissant).

Concernant la **génération automatique** d'une propriété, il suffit de créer un **constructeur vide** et d'initialiser la propriété ici. Pour la date, pour avoir celle actuelle un simple appel à `new \DateTime()` suffit.

<div class="exercise">

1. Faites les modifications nécessaires au niveau de l'entité `Publication` afin q'une date entrée dans le `payload` soit ignorée (qu'on ne puisse pas l'écrire) et que celle-ci soit plutôt générée automatiquement du côté du serveur.

2. Faites en sorte de limiter les messages entre 4 et 200 caractères.

3. Faites en sorte que la collection de publications renvoyées soit triées de la plus récente à la plus ancienne.

4. En utilisant des **assertions**, faites en sorte qu'il soit obligatoire de préciser le message dans le `payload` et que celui-ci ne soit pas null.

5. Videz le cache.

6. Sur `Postman`, testez que tous vos changements sont bien pris en compte. Il est important de noter qu'au niveau de la requête `POST`, il ne doit plus qu'y avoir que le message dans le `payload`.

</div>

Maintenant, nous aimerions interdire l'utilisation de certaines méthodes. En effet, nous ne voulons pas que les publications soient modifiables. Il faut donc interdire les méthodes `PUT` et `PATCH` ou plutôt, autoriser seulement les autres méthodes. Pour cela, il suffit d'utiliser le paramètres `operations` au niveau de l'annotation `#[ApiResource]`. Ce paramètre est une **liste** des opérations permises, sous la forme d'objets (qu'on peut d'ailleurs configurer de manière ciblée).

Les opérations possibles sont :

* `GetCollection` : récupération d'un ensemble de ressources.

* `Get` : récupération d'une ressource ciblée.

* `Post`

* `Put`

* `Patch`

Ainsi, la configuration suivante permet seulement l'utilisation de la méthode `Get` ciblée et `Delete` :

```php
#[ApiResource(
    operations: [
        new Get(),
        new Delete()]
)]
```

Petite note à part, par défaut, `GetCollection` utilise un système de pagination afin de limiter le nombre de ressources renvoyées (par défaut, 30). Il est alors possible de préciser un paramètre `page` dans le **query string** de la route, pour naviguer. Par exemple, `/api/publications` renvoie les publications de 1 à 30. Et `/api/publications?page=3` renvoie les publications de 61 à 90.

Ce système est nécessaire afin de limiter les données lues côté client et ainsi charger le contenu au fur et à mesure (imaginiez si vous deviez charger tout **Twitter** à chaque accès !!!). Il est possible d'augmenter le nombre de ressources renvoyées par page ou bien simplement désactiver ce système (et donc tout renvoyer à chaque fois). Par convenance dans le cadre de l'application mobile que nous allons développer, nous allons donc désactiver ce système, mais retenez bien que dans un contexte réel, il faudrait le conserver et charger le contenu petit à petit, au fil du parcours de l'utilisateur.

Pour configurer tout cela :

```yaml
# Dans config/packages/api_platform.yaml (à créer)
api_platform:
    defaults:
        pagination_items_per_page: 30 # Pour changer le nombre de ressources renvoyées si la pagination est activée
        pagination_enabled: true/false # Active ou désactive la pagination
```

<div class="exercise">

1. Empêchez l'utilisation des méthodes `PUT` et `PATCH` sur les publications.

2. En utilisant `Postman`, vérifiez qu'il n'est effectivement plus possible d'utiliser ces méthodes. Vérifiez aussi que les autres méthodes fonctionnement toujours.

3. Créez le fichier `config/package/api_platform.yaml` et désactivez la pagination.

4. Videz le cache.

</div>

Parfait, vous avez configuré votre première ressource ! Comme vous l'avez constaté, nous n'avons pas quitté le code de la classe `Publication` pour faire tout cela.

## Les utilisateurs

Il est temps de nous attaquer à la création de nos **utilisateurs** qui vont, eux aussi, être des **ressources** de l'application.

## Création de la ressource

Pour la création de la ressource, nous allons avoir besoin de préciser un paramètre pour garantir le caractère **unique** de certains attributs, au niveau de la base de données (afin de créer une contrainte d'unicité). Pour cela, il suffit simplement de préciser le paramètre `unique: true` au niveau de l'annotation `[#ORM\Column]` de l'attribut concerné. Du côté de la base, cela signifie simplement qu'une valeur associée à cet attribut est unique, même si elle ne fait pas partie de la clé primaire.

En plus de l'unicité côté base de données, il est aussi possible d'effectuer cette vérification du côté de l'application avant la vérification faite sur la base de données. Pour cela, on utilise au niveau de la déclaration de la classe l'annotation `#[UniqueEntity(['nom_attr1', 'nom_attr2', ...])]` qui permet de lister les propriétés qui doivent être uniques.

Pour configurer certains attributs, n'hésitez pas à aller consulter la partie sur les annotations présentée précédemment.

Note à part, pour la méthode `PATCH`, par défaut, **API Platform** attend un format `application/merge-patch+json` qui est sensiblement la même chose que du `json`. Pour autoriser l'envoi d'un document au format `json` simple (ce qui sera plus pratique, notamment avec `Postman`), on peut ajouter ce paramètre dans la configuration :

```yml
# Dans config/packages/api_platform.yaml
api_platform:
  patch_formats:
      json: ['application/json']
```

<div class="exercise">

1. De la même manière que pour les publications, utilisez la commande de `Symfony` pour créer une ressource **Utilisateur**. Pour l'instant, on souhaite avoir pour cette ressource :

    * Un **login** (type string, non null, longueur max 20)
    * Une **adresse email** (type string, non null, longueur max 255)

2. Faites en sorte de rendre le login et l'adresse email **uniques** aussi bien au niveau de la base qu'au niveau de l'application.

3. Imposez une limite entre 3 et 20 caractères pour le login.

4. Faites en sorte que l'adresse email respecte le bon format.

5. Faites en sorte de rendre obligatoire et non null le login et l'adresse email dans le `payload`.

6. Faites en sorte d'interdire l'utilisation de la méthode `PUT` (mise à jour partielle avec `PATCH` autorisée, mais pas de mise à jour complète avec `PUT`).

7. Videz le cache.

8. Comme pour les publications, si vous **travaillez sur la base de l'iut**, utilisez l'annotation pour donner le nom de table `utilisateur_api` :

    ```php
    #[ORM\Table(name: 'utilisateur_api')]
    class Utilisateur {

    }
    ```

9. Synchronisez vos changements avec la base de données (retrouvez les 2 commandes à utiliser)

10. Rechargez la page listant les opérations de l'API et vérifiez que votre utilisateur apparait bien.

11. Testez de créer des utilisateurs et vérifiez que vos contraintes sont respectées (essayez de rentrer un login trop court ou trop long, une adresse email au mauvais format, etc...)

12. Faites l'ajout nécessaire dans la configuration d'**API Platform** pour changer le format d'envoi des données de la méthode `PATCH` pour du `json` simple.

13. Testez d'autres méthodes.
</div>

## Connexion entre les publications et les utilisateurs

Avant de mettre en place un système d'authentification, nous allons relier nos publications à nos utilisateurs. En effet, fini les publications anonymes, il faut que chaque publication possède un **auteur** faisant référence à un utilisateur. À l'inverse, on aimerait pouvoir connaître toutes les publications d'un utilisateur donné.

### Associations

Avec **doctrine**, pour associer deux entités, il suffit de créer un attribut faisant référence à une autre classe et utiliser les annotations nécessaires pour préciser le sens et la cardinalité de cette association. Il est aussi possible de paramétrer la stratégie en cas de suppression (mettre à null les colonnes faisant référence ou bien supprimer les entités associées...).

Voici la liste des annotations disponibles :

* `#[ORM\ManyToOne(targetEntity: Target::class, inversedBy: ...)]` : À utiliser dans une relation **1 - plusieurs**, du côté de l'entité qui doit posséder **une instance** de l'entité ciblée. Le paramètre `inversedBy` permet de spécifier le nom de l'attribut de la classe cible qui fait référence à l'entité (où on place cette annotation). Le paramètre `targetEntity` permet de spécifier la classe cible.

On peut aussi ajouter une annotation supplémentaire `#[ORM\JoinColumn(onDelete="SET NULL")]` si on veut appliquer la stratégie de mettre l'attribut référencé à `null` lors de la suppression de l'entité référencée (au lieu de supprimer complétement la ressource qui lui est liée).

* `[ORM\OneToMany](targetEntity : Target::class, mappedBy: ..., cascade: [...])` : À utiliser dans une relation **1 - plusieurs**, du côté de l'entité qui doit posséder une **collection** de l'entité ciblée (l'attribut est de type `iterable`). Le paramètre `mappedBy` fonctionne de la même manière que `inversedBy`. Le paramètre `targetEntity` fonctionne de la même manière que l'entité précédente et enfin, `cascade` (optionnel) permet de définir un comportement "en cascade". Par exemple, on peut spécifier `remove` pour supprimer les entités liées à celle-ci lors de sa suppression. Si on fait cela, on ne doit pas aussi appliquer la stratégie de mise à `null` que nous avons vu juste avant.

* `#[OneToOne(targetEntity: Target::class, mappedBy: ..., cascade: [...])]` : À utiliser dans une relation `1 - 1`. Dans l'autre entité, on utilise la même annotation en remplaçant `mappedBy` par `inversedBy`.

* `#[ManyToMany(targetEntity: Target::class, mappedBy: ...)]` : À utiliser dans une relation **plusieurs - plusieurs**. Dans l'autre entité, on utilise la même annotation en remplaçant `mappedBy` par `inversedBy`. Dans ce cas, une nouvelle table sera créée dans la base de données (table de jointure). Dans une des deux entités, au niveau de l'attribut concerné, il faut alors ajouter une autre annotation `#[JoinTable(name: 'nom_table_jointure')]` afin de nommer cette table.

La configuration des annotations présentée implique un système **bi-directionnel** où l'entité A connait l'entité B et inversement. Il est bien entendu possible de faire un système unidirectionnel. Pour cela, il faut placer seulement l'annotation dans une des entités concernées, de ne pas spécifier les paramètres `mappedBy` et `inversedBy` et de rajouter l'annotation `#[JoinColumn(name: 'parent_id', referencedColumnName: 'id')]` où `parent_id` référence le nom de l'attribut "clé étrangère" (qui va être créé) et `referencedColumnName` le nom de la clé primaire de la table référencée. Il est aussi possible de créer des auto-référence (référence vers la même entité).

Attention, au niveau des attributs des relations `OneToOne` ou `ManyToOne`, une clé étrangère est générée dans la base :

* Si on veut que cet attribut ne puisse pas être null dans la base, on ajoute une annotation `#[ORM\JoinColumn(nullable: false)]`.

* Si on veut autoriser le comportement de suppression en cascade du côté de la base lors de la suppression de l'entité cible, il faut aussi spécifier le paramètre `onDelete: "CASCADE"` dans cette même annotation. Par exemple `#[ORM\JoinColumn(nullable: false, onDelete: "CASCADE")]`

* Si on veut qu'il soit obligatoirement présent (et non null) dans le `payload`, on ajoute les deux assertions `#[Assert\NotBlank]` et `#[Assert\NotNull]`.

Exemple : On souhaite pouvoir connaître le groupe d'un étudiant.

```php
#[ORM\Entity(repositoryClass: EtudiantRepository::class)]
class Etudiant {

    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private ?string $nom = null;

    #[ORM\Column(length: 255)]
    private ?string $prenom = null;

    #[ORM\ManyToOne(targetEntity: Groupe::class)]
    #[JoinColumn(name: 'groupe', referencedColumnName: 'id', nullable: false)]
    #[Assert\NotBlank]
    #[Assert\NotNull]
    public ?Groupe $groupe = null;

}

#[ORM\Entity(repositoryClass: GroupeRepository::class)]
class Groupe {

    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private ?string $nomGroupe = null;

}
```

Mais maintenant, je souhaite aussi que le groupe puisse connaître la liste des étudiants. Si le groupe est supprimé, je veux supprimer tous les étudiants.

```php
#[ORM\Entity(repositoryClass: EtudiantRepository::class)]
class Etudiant {

    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private ?string $nom = null;

    #[ORM\Column(length: 255)]
    private ?string $prenom = null;

    #[ORM\ManyToOne(targetEntity: Groupe::class, inversedBy: 'etudiants')]
    #[Assert\NotBlank]
    #[Assert\NotNull]
    #[JoinColumn(nullable: false, onDelete: "CASCADE")]
    public ?Groupe $groupe = null;

}

#[ORM\Entity(repositoryClass: GroupeRepository::class)]
class Groupe {

    #[ORM\Id]
    #[ORM\GeneratedValue]
    #[ORM\Column]
    private ?int $id = null;

    #[ORM\Column(length: 255)]
    private ?string $nomGroupe = null;

    #[ORM\OneToMany(mappedBy: 'groupe', targetEntity: Etudiant::class, cascade : ["remove"])]
    public iterable $etudiants;

}
```

Vous pouvez retrouver [une documentation et des exemples plus complets](https://www.doctrine-project.org/projects/doctrine-orm/en/2.14/reference/association-mapping.html) de ce système.

Attention, comme vous aurez pu le remarquer, les propriétés faisant références à d'autres classes (le groupe et la collection d'étudiants) doivent avoir une visibilité **public**. Sinon, certaines fonctions comme la suppression ne marcheront pas.

<div class="exercise">

1. Faites en sorte d'ajouter un **auteur** référençant un **utilisateur** du côté des publications. Cette association doit être bidirectionnelle, donc, on doit pouvoir aussi lister les publications d'un utilisateur donné. Si un utilisateur est supprimé, toutes ses publications doivent être également supprimées. L'auteur doit être obligatoirement présent et **non null** dans le `payload` (pour les requêtes `POST` et `PATCH`) et ne doit pas pouvoir être **null** dans la base de données. Les deux propriétés que vous allez créer doivent avoir une visibilité **public**.

2. Dans les deux entités, ajoutez les **getters** et les **setters** correspondants aux nouveaux attributs ajoutés. Inspirez-vous de la forme des autres méthodes déjà présentes dans ces classes.

3. Dans votre base de données, supprimez toutes vos publications (un champ non null va être ajouté ce qui va causer des problèmes si on laisse les publications actuelles, sans auteur).

4. Synchronisez votre base de données avec votre application. Vérifiez qu'une clé étrangère a bien été ajoutée à la table des publications.

5. Videz le cache.

</div>

Maintenant nous allons essayer de créer une publication en précisant son auteur.

<div class="exercise">

1. Sur `Postman`, vérifiez que vous obtenez un message d'erreur si vous tentez d'ajouter une publication sans préciser son auteur.

2. Maintenant, tentez d'ajouter une publication en précisant l'identifiant numérique de l'utilisateur pour la partie `auteur`. Analysez le message d'erreur.

3. La valeur à préciser pour faire référence à une autre ressource est appelé `IRI` (International Ressource Identifier) qui est une référence (un "lien") interne à l'application. Pour le trouver, récupérez (avec une requête `GET`) les détails d'un de vos utilisateurs. Il faut regarder au niveau de la propriété `@id`.

4. Une fois le mécanisme des `IRI` compris, retentez de créer une publication en affectant un utilisateur.

5. Ajouter plusieurs publications liées à un utilisateur et vérifiez que la suppression de l'utilisateur (toujours en faisant une requête !) entraîne la suppression des publications qui lui sont liées.

</div>

### Les groupes de sérialisation

Vous remarquerez que quand on affiche la liste des publications ou une publication précise, au niveau de l'auteur, seul son `IRI` est indiqué. Cela est gênant, car si on veut connaître les détails de l'auteur (par exemple son login) on est obligé de faire une requête supplémentaire.

Il serait donc plutôt préférable d'afficher les détails de l'utilisateur à la place. Nous pouvons faire cela grâce aux **groupes de sérialisation**.

Quand on manipule nos ressources, il peut exister **deux types de contextes** :

* La **normalisation** : quand on transforme un objet de l'application en `JSON` et qu'on le renvoie au client.

* La **dénormalisation** : quand on charge un `payload` au format `JSON` et qu'on le transforme en objet de l'application.

Dans les deux cas, il peut être possible de définir des **groupes** pour contrôler la présence d'une propriété. Dans le cas de la **normalisation**, il est donc possible de contrôler quelles propriétés sont **sérialisées**, c'est-à-dire, renvoyées au client sous le format `JSON`.

Un paramètre de l'annotation `#[ApiResource(...)` nommé `normalizationContext : ["groups" => ['nom_groupe1', ...]]` permet d'activer certains groupes pendant la phase de **normalisation**. Au niveau des propriétés, l'annotation `#[Groups(['nom_groupe1', 'nom_groupe2', ...])]` permet de faire en sorte qu'une propriété est affichée ou non dans le document `JSON` selon le groupe activé.

Par exemple :

```php
#[ApiResource(
    ...
    normalizationContext: ["groups" => ["etudiant:read"]],
)]
class Etudiant {

    #[Groups(['etudiant:read'])]
    private ?int $id = null;

    #[Groups(['etudiant:read'])]
    private ?string $nom = null;

    #[Groups(['etudiant:read'])]
    private ?string $prenom = null;

    public ?Groupe $groupe = null;
}

#[ApiResource(
    ...
    normalizationContext: ["groups" => ["groupe:read"]],
)]
class Groupe {

    #[Groups(['groupe:read'])]
    private ?int $id = null;

    #[Groups(['groupe:read'])]
    private ?string $nomGroupe = null;

    public iterable $etudiants;

}
```

Ici, quand on récupère les données d'un étudiant, on affiche seulement son id, son nom, son prénom, mais pas son groupe (même pas d'IRI).

Pour les groupes, on affiche tout sauf la liste des étudiants (qui aurait était une liste d'IRI aussi).

Pour que quand on lit un étudiant, on obtient l'id et l'identifiant du groupe il faut d'abord ajouter `groupe` au groupe `user:read` et il faut activer un second groupe `groupe:read` dans le contexte de normalisation de l'étudiant pour qu'il soit utilisé quand on lit le groupe en question ! Par exemple : 

```php
#[ApiResource(
    ...
    normalizationContext: ["groups" => ["etudiant:read", "groupe:read"]],
)]
class Etudiant {

    #[Groups(['etudiant:read'])]
    private ?int $id = null;

    #[Groups(['etudiant:read'])]
    private ?string $nom = null;

    #[Groups(['etudiant:read'])]
    private ?string $prenom = null;

    #[Groups(['etudiant:read'])]
    public ?Groupe $groupe = null;
}

#[ApiResource(
    ...
    normalizationContext: ["groups" => ["groupe:read"]],
)]
class Groupe {

    #[Groups(['groupe:read'])]
    private ?int $id = null;

    #[Groups(['groupe:read'])]
    private ?string $nomGroupe = null;

    public iterable $etudiants;

}
```

Si à l'inverse on aurait voulu avoir seulement la liste des étudiants quand on lit les détails d'un groupe (ce sens est plutôt à éviter, en règle générale) :

```php
#[ApiResource(
    ...
    normalizationContext: ["groups" => ["etudiant:read"]],
)]
class Etudiant {

    #[Groups(['etudiant:read'])]
    private ?int $id = null;

    #[Groups(['etudiant:read'])]
    private ?string $nom = null;

    #[Groups(['etudiant:read'])]
    private ?string $prenom = null;

    public ?Groupe $groupe = null;
}

#[ApiResource(
    ...
    normalizationContext: ["groups" => ["groupe:read", "etudiant:read"]],
)]
class Groupe {

    #[Groups(['groupe:read'])]
    private ?int $id = null;

    #[Groups(['groupe:read'])]
    private ?string $nomGroupe = null;

    #[Groups(['groupe:read'])]
    public iterable $etudiants;

}
```

Si vous avez bien compris ce mécanisme, à vous de jouer !

<div class="exercise">

1. En utilisant les groupes de sérialisation, faites en sorte que quand on lit une publication, on obtienne aussi les détails de l'utilisateur (id, login, adresse email).

2. Videz le cache.

3. Testez en appelant la méthode `GET` générale, mais aussi en ciblant une publication en particulier.

</div>

### Publications d'un utilisateur

Comme nous l'avons vu en introduction de ce TD, avec l'architecture `REST`, il doit être possible d'accéder à la liste des publications d'un utilisateur précis en utilisant cette route :

`/utilisateur/{id}/publications`

Par exemple : `/utilisateur/2/publications` : les publications de l'utilisateur 2.

Pour cela, rien de plus simple : il suffit de configurer une deuxième annotation `#[ApiResource]` au niveau de la ressource cible (ici les publications) en indiquant un **template d'URL** et des **variables** afin d'aller chercher la ressource au bon endroit.

```php
#[ApiResource(
    //Template avec bom de variables intégrées entre accolades. Il est possible d'en indiquer plusieurs
    uriTemplate: '/chemin/{identifiant1}/ressource',
    //Liste des opérations autorisés
    operations: [...],
    uriVariables: [
        //On indique comment accèder à la sous-ressource (dont l'identifiant correspondant à celui passé dans la route)
        'identifiant1' => new Link(
            //La propriété qui contient la ressource ciblée dans la classe cible
            fromProperty: 'nom_propriete',
            //La classe cible dont on veut récupérer une instance à partir de l'identifiant 
            fromClass: Target::class
        )
    ],
)]
```
Pour mieux illustrer cela, reprenons notre exemple d'étudiants et de groupe. On souhaite avoir une route `/groupe/{id}/etudiants` pour obtenir tous les étudiants d'un groupe précis :


```php
//La première annotation, pour l'accès générale à la ressource
#[ApiResource(...)]

//La seconde annotation, pour l'accès aux étudiants d'un groupe précis via la méthode GetCollection :
#[ApiResource(
    uriTemplate: '/groupes/{idGroupe}/etudiants',
    //On autorise seulement le GetCollection (liste de tous les etudiants du groupe)
    operations: [new GetCollection()],
    uriVariables: [
        'idGroupe' => new Link(
            fromProperty: 'etudiants',
            fromClass: Groupe::class
        )
    ],
)]
class Etudiant {
    private ?int $id = null;

    private ?string $nom = null;

    private ?string $prenom = null;

    public ?Groupe $groupe = null;
}
```

Il est bien sûr possible d'activer des groupes lors de la normalisation ainsi que d'autres paramètres, comme `order`.

<div class="exercise">

1. En ajoutant une nouvelle annotation de type `#[ApiResource]` dans `Publication`, faites en sorte d'ajouter une route qui permet d'obtenir la liste des publications d'un utilisateur précis.

2. Il faut aussi que ces publications soient triées de la plus récente à la plus ancienne et n'oubliez pas d'activer les mêmes groupes que pour l'autre annotation lors de normalisation !

3. Videz le cache.

4. Testez votre nouvelle route en essayant d'afficher la liste des publications d'un de vos utilisateurs.

</div>

## Authentification

Nous avons enfin pu relier nos publications à nos utilisateurs efficacement, mais il reste plusieurs problèmes :

* Un utilisateur qui poste une publication doit connaître son `IRI`.

* On peut affecter une publication à n'importe quel utilisateur !

* N'importe qui peut supprimer un utilisateur ou une de ses publications.

* N'importe qui peut modifier le profil d'un utilisateur

Pour régler cela, nous allons mettre en place un système d'authentification pour faire en sorte qu'une publication soit affectée à l'utilisateur authentifié qui la poste et nous rajouterons un système de sécurité pour empêcher la modification ou la suppression de ressources qui n'appartiennent pas à l'utilisateur qui emet la requête.

Comme nous l'avons mentionné au début de ce TD, l'architecture **REST** implique la notion de **stateless**, c'est-à-dire que le serveur ne garde aucune information sur l'utilisateur en mémoire vive, avec une session, etc... Mais alors, comment mettre en place un système d'authentification et faire comprendre au serveur que l'utilisateur est légitime ? Pour cela, nous allons utiliser le mécanisme des `JSON Web Tokens` souvent abrégé en `JWT`.

Un `JWT` est une chaîne de caractères appelée `token` encodée en `base64` qui contient de l'information. Une fois déchiffré, ce jeton se décompose en trois parties :

* Le `header` (au format `JSON`) qui contient des informations sur la nature du jeton (le type de jeton, le type d'algorithme utilisé)

* Le `payload` (au format `JSON`) qui contient de l'information utile (que l'on souhaite lire) placée dans ce jeton.

* La `signature` qui permet de vérifier l'authenticité du jeton.

Pour créer un `JWT`, on utilise une paire clé publique/clé privée. La **clé privée** sert à créer la `signature` du jeton à partir de la concaténation du `header` et du `payload`. Elle est conservée par l'émetteur et n'est pas transmisse. La **clé publique** quant à elle sert à vérifier la signature du `token` (pour attester qu'il n'a pas été modifié) et peut être distribuée. Le jeton final est encodé en `base64`.

Tout le monde peut décoder un `JWT` et lire son contenu. Donc il **ne doit pas contenir d'informations sensibles** (mot de passe, numéro de carte de crédit, etc...) Cependant, bien qu'il soit lisible par tous, il est impossible de le **falsifier**. En effet, seul l'émetteur qui possède la clé privée ayant servi à signer le jeton peut le modifier ! Si quelqu'un tente de falsifier un `JWT`, il ne pourra pas connaître la signature adéquate à apposer pour ce nouveau corps de données. Ainsi, grâce à la signature, un token falsifié sera rejeté.

Vous pouvez aller voir à quoi rassemble un `JWT` [à cette adresse](https://jwt.io/).

De nos jours, les **API** utilisent un système d'authentification par `token` comme avec les `JWT`. La logique est la suivante :

* Le serveur possède une paire clé publique/clé privée.

* Quand un utilisateur veut s'authentifier, il envoie ses identifiants, le serveur les vérifie et, si tout est bon, créé un `JWT` contenant les informations utiles au serveur, par exemple, l'id de l'utilisateur.

* Le serveur renvoie le `JWT` et le client le conserve.

* Dès qu'il veut accéder à une route sécurisée, le client envoie le `JWT` au serveur, en plus de sa requête.

* Quand le serveur reçoit le `JWT`, il vérifie qu'il n'a pas été falsifié (avec les deux clés) et peut donc le décoder en toute confiance et récupérer les informations de l'utilisateur à partir de son identifiant stocké dans le `JWT` (en faisant une requête sur la base pour obtenir le reste des informations, par exemple) et ainsi vérifier s'il a le droit d'effectuer cette requête.

Malgré le fait que le `JWT` soit décodable côté client, il est impossible le falsifier pour changer l'id de l'utilisateur contenu, car le serveur le détectera. Il est aussi impossible de simplement créer un `JWT` avec l'identifiant d'un autre utilisateur, car le client ne peut pas générer la signature adéquate (car il ne possède pas la clé privée).

On respecte la notion de **stateless** car le serveur ne garde aucune information sur l'utilisateur dans une session ou en mémoire vive. Le `JWT` est transmis à chaque requête par le client, quand on en a besoin, ce qui permet de vérifier sa légitimité.

Néanmoins, si ce token venait à être dérobé, alors on pourrait usurper un utilisateur. C'est pour cela que les `JWT` ont une date d'expiration (généralement, 3600 secondes, mais on peut mettre plus). Pour éviter que l'utilisateur n'ait à se reconnecter manuellement lors de l'expiration, il faut alors mettre en place tout un système de rafraîchissement que nous n'étudierons pas cette année, par manque de temps (mais vous pouvez aller vous [documenter](https://github.com/markitosgv/JWTRefreshTokenBundle) plus tard sur la manière de mettre en place un tel système). 

Dans le cadre du développement de notre application mobile, nous augmenterons la durée de vie des `JWT` et nous ferons en sorte de vérifier l'expiration des **tokens**.

### Ajout des utilisateurs

Tout abord, nous allons devoir intégrer nos utilisateurs au système d'authentification de `Symfony` pour que par la suite, ils puissent être vérifiés et récupéré automatiquement lors de l'envoi des identifiants au serveur. `Symfony` va permettre de sécuriser le mot de passe en le **hachant**.

<div class="exercise">

1. Supprimez tous les utilisateurs de votre base de données.

2. Dans votre classe `Utilisateur` ajoutez cette propriété :

    ```php
    #[ORM\Column(length: 255, nullable: false)]
    #[ApiProperty(readable: false, writable: false)]
    private ?string $password = null;
    ```

    Il s'agit du mot de passe tel qu'il sera stocké en base de données (donc **haché** et donc sécurisé). Vous l'aurez remarqué, on interdit toute écriture (depuis un payload) et nous ne plaçons **aucun groupe de sérialisation** sur cette propriété. Elle ne doit jamais être retournée au client dans le document `JSON`.

3. Ajoutez un attribut `$plainPassword` de type `string` à la classe `Utilisateur`. Cet attribut ne doit pas être stocké dans la base (donc pas d'annotation `ORM`...). En fait, cet attribut servira à l'envoi du mot de passe **en clair** dans le `payload` lors de la création ou la modification de l'utilisateur ! Il sera ensuite converti et placé dans `$password` à terme. Ici aussi, pas de groupe de sérialisation, cet attribut n'a pour vocation que d'être envoyé (et d'ailleurs il n'est pas sauvegardé tel quel).

4. Faites en sorte d'appliquer les assertions suivantes à `$plainPassword` :

    * Présence obligatoire dans le `payload`.
    * Non null.
    * Entre 8 et 30 caractères.
    * Respecte l'expression régulière suivante : `#^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]{8,30}$#`. (au moins une minuscule, une majuscule et au moins un nombre).

5. Enfin, ajoutez cette propriété :

    ```php
    #[ORM\Column(type: 'json')]
    private array $roles = [];
    ```

    Il s'agit d'une liste de rôles de l'utilisateur, stocké sous le format `json`. Cela est utile s'il y a plusieurs rôles avec des privilèges différents, dans votre application (par exemple, "premium", "admin", etc...). Par défaut, tous les utilisateurs authentifiés auront le rôle `ROLE_USER` (qui ne sera d'ailleurs pas stocké en base, car tout le monde le possède).

    Ici aussi, pas de groupe, car c'est une propriété qui a pour but d'être lue de manière itnerne pas l'application.

6. Videz le cache.

7. Synchronisez vos changements avec la base de données.

</div>

Maintenant, nous allons implémenter deux interfaces qui permettront d'intégrer notre classe `Utilisateur` au système d'authentification de `Symfony`.

<div class="exercise">

1. Ajoutez des **getters** et des **setters** pour les propriétés `$password`, `$plainPassword` et `$roles`. Pour le **getter** de `$roles`, vous pouvez copier la méthode suivante :

    ```php
    public function getRoles(): array
    {
        $roles = $this->roles;

        $roles[] = 'ROLE_USER';

        return array_unique($roles);
    }
    ```

    L'objectif est simplement de concaténer le rôle `ROLE_USER` aux autres rôles de l'utilisateur, car tout utilisateur authentifié possède ce rôle.

2. Faites implémenter à la classe les interfaces `UserInterface` et `PasswordAuthenticatedUserInterface`. A ce stade, vous devez alors implémenter les deux méthodes suivantes :

    ```php
    public function eraseCredentials()
    {
        //Remet plainPassword à null
    }

    public function getUserIdentifier(): string
    {
        //Retourne la propriété avec laquelle l'utilisateur s'identifie
    }
    ```

    Dans la première méthode, il s'agit d'une méthode pour supprimer de la mémoire le mot de passe **en clair**. Dans la seconde méthode, il faut retourner la propriété qui sera utilisée pour se connecter (en combinant avec le mot de passe). Il faut que cela soit quelque chose d'unique. Dans notre cas, on a donc le choix entre : l'id, le login et l'adresse email. Nous allons choisir le **login**.

    Implémentez ces deux méthodes.

3. Dans la classe `src/Repository/UtilisateurRepository.php`, implémentez l'interface `PasswordUpgraderInterface` puis ajoutez la méthode suivante (n'oubliez pas d'importer les classes manquantes) :

    ```php
    public function upgradePassword(PasswordAuthenticatedUserInterface $user, string $newHashedPassword): void
    {
        if (!$user instanceof Utilisateur) {
            throw new UnsupportedUserException(sprintf('Instances of "%s" are not supported.', \get_class($user)));
        }
        $user->setPassword($newHashedPassword);
        $this->save($user, true);
    }
    ```

    Cette méthode est demandée par `Symfony` afin de gérer la mise à jour du mot de passe haché auprès de la base de données.
</div>

Maintenant, nous devons faire en sorte qu'en cas de création (`POST`) ou de mise à jour (`PATCH`) le mot de passe **en clair** soit convertit en mot de passe haché. Pour cela, nous allons utiliser une classe dite de `processeur` dont le but est de réaliser des opérations sur une ressource lors de l'exécution d'une méthode HTTP avant la vérification des données et l'enregistrement dans la base de données.

Pour spécifier un **processeur** à utiliser, il suffit de préciser la classe via un paramètre `processor` au niveau de l'objet lié à la méthode dans le paramètre `operations` de l'annotation `#[ApiResource]`.

Par exemple :
```php
#[ApiResource(
    operations: [
        ...
        new Put(processor: MonProcesseur::class),
    ],
    ...
)]
```

Dans notre cas, le **processeur** que nous allons ajouter est le suivant :

```php
# Dans src/State
namespace App\State;

use ApiPlatform\Metadata\Operation;
use ApiPlatform\State\ProcessorInterface;
use Symfony\Component\PasswordHasher\Hasher\UserPasswordHasherInterface;

class UserPasswordHasher implements ProcessorInterface
{
    public function __construct(private readonly ProcessorInterface $processor,
                                private readonly UserPasswordHasherInterface $passwordHasher)
    {
    }

    public function process($data, Operation $operation, array $uriVariables = [], array $context = [])
    {
        if (!$data->getPlainPassword()) {
            $this->processor->process($data, $operation, $uriVariables, $context);
            return;
        }

        $hashedPassword = $this->passwordHasher->hashPassword(
            $data,
            $data->getPlainPassword()
        );
        $data->setPassword($hashedPassword);
        $data->eraseCredentials();

        $this->processor->process($data, $operation, $uriVariables, $context);
    }
}
```

Analysons ce code de plus près :

* `$data` représente l'objet manipulé (dans notre cas, un utilisateur).

* L'opération `process` déclenche le processus de vérification et d'enregistrement des données.

* Si le mot de passe (en clair) n'est pas transmis (dans le cas d'un `PATCH`, par exemple), on ne fait rien et on déclenche `process`.

* Sinon, on **hache** le mot de passe, on l'affecte à l'utilisateur, on supprime le mot de passe en clair de la mémoire et on déclenche `process`.

Concernant les deux paramètres dont a besoin cette classe, ils seront **injectés** par `Symfony`. Il faut configurer cette injection de dépendances dans le fichier `config/services.yaml` :

```yml
# Dans config/services.yaml
    App\State\UserPasswordHasher:
        bind:
            $processor: '@api_platform.doctrine.orm.state.persist_processor'
            $passwordHasher: '@security.password_hasher'
```

<div class="exercise">

1. Ajoutez la classe `UserPasswordHasher` telle que définie ci-dessus dans le dossier `src/State` (que vous devez créer).

2. Réalisez l'injection de dépendances nécessaire en éditant `config/services.yaml`

3. Enfin, ajoutez ce nouveau processeur lors des opérations `POST` et `PATCH` sur la ressource `Utilisateur`.

4. Videz le cache et essayez de créer un nouvel utilisateur. Il faudra bien préciser `plainPassword` dans le `payload`. Allez vérifier le résultat dans la base de données.
</div>

### Contexte de validation

Actuellement, si on essaye de faire un `PATCH` sur l'utilisateur, celui-ci nous demandera de rentrer le mot de passe. Or, dans un patch, on doit pouvoir mettre à jour seulement les attributs que l'on veut. Nous allons utiliser un mécanisme appelé **contextes de validation** pour rendre optionnelles certaines **assertions** selon la méthode utilisée.

Au niveau d'une `assertion`, il est possible de spécifier une liste de **groupes** pour lesquels l'assertion doit être vérifié. Par exemple :

`#[Assert\NotBlank(groups: ['user:create'])]` : n'est appliqué que quand le groupe `user:create` est activé.

`#[Assert\Length(min: 2, max: 10, groups: ['user:create', 'user:update'])]` : n'est appliqué que quand le groupe `user:create` ou `user:update` est activé.

Pour définir quel groupe activer sur telle ou telle méthode, on spécifie un paramètre `validationContext` au niveau de l'objet lié à la méthode dans le paramètre `operations` de l'annotation `#[ApiResource]`.

Par exemple :
```php
#[ApiResource(
    operations: [
        new Put(validationContext: ["groups" => ["Default", "user:udpate"]]),
    ],
    ...
)]
```

Le groupe `Default` est généralement nécessaire, car il permet de prendre en compte les assertions où aucun groupe n'est spécifié.

<div class="exercise">

1. Pour l'opération `POST` (toujours sur les utilisateurs), activez les groupes `Default` et `user:create` pour le contexte de validation.

2. Pour chaque attribut où vous avez placé les assertions `NotBlank` et `NotNull`, spécifiez le groupe de validation `user:create`.

3. Videz le cache. Tentez de mettre à jour seulement le login d'un utilisateur (avec `PATCH`). Cela devrait fonctionner.

4. Tentez de créer un utilisateur sans spécifier le mot de passe, cela ne devrait pas fonctionner.

5. On voudrait interdire à un utilisateur de modifier son login. Pour l'opération `PATCH`, activez les groupes `Default` et `user:update` pour le contexte de validation. Ajoutez une assertion `Blank` sur la propriété `login` (signifie "ne doit pas apparaitre") valide seulement pour le groupe `user:udpate`.

6. Videz le cache puis vérifiez que la création d'un utilisateur (avec `POST`) marche toujours et qu'avec `PATCH` il est possible de mettre à jour son adresse mail, mais pas son login.

</div>

### Prise en charge des JWT

Maintenant, il ne nous reste plus qu'à mettre en place un système de connexion afin de délivrer un `JWT` qui permettra de nous identifier auprès du serveur, à chaque requête.

<div class="exercise">

1. Installez le package suivant (système d'authentification par JWT) :

    ```bash
    composer require "lexik/jwt-authentication-bundle"
    ```

2. Comme nous l'avons vu, la création et la vérification de `JWT` nécessite une paire de clés (publique/privée). Utilisez la commande suivante pour générer ces clés :

    ```bash
    php bin/console lexik:jwt:generate-keypair
    ```

    Elles sont placées dans le dossier `config/jwt`. Les fichiers de configuration se mettent à jour automatiquement (jetez un œil à `.env`, par exemple). Il faut aussi protéger ce dossier pour qu'on ne puisse pas récupérer vos clés. Téléchargez ce fichier [htaccess]({{site.baseurl}}/assets/TD2/htaccess3), placez-le dans le dossier contenant les clés puis renommez-le en `.htaccess`.

3. Au niveau du fichier `config/lexik_jwt_authentication.yaml`, ajoutez le paramètre suivant pour augmenter la durée de vie des `JWT` :

    ```yml
    lexik_jwt_authentication:
        ...
        token_ttl: 604800 #Une semaine
    ```

4. Au niveau du fichier `config/routes.yaml` ajoutez une nouvelle route :

    ```yml
    auth:
        path: /api/auth
        methods: ['POST']
    ```

    Il s'agira de la route où les utilisateurs devront envoyer leur login/mot de passe pour obtenir leur `JWT`.

5. Éditez la partie `providers` du fichier `config/package/security.yaml` comme suit :

    ```yml
    providers:
        users:
            entity:
                class: App\Entity\Utilisateur
                property: login
    ```

    Cette partie de la configuration sert à indiquer quelle classe fournit les utilisateurs de l'application ainsi que l'identifiant unique utilisé pour se connecter (nous avions décidé de choisir le `login`).

6. Toujours dans le même fichier, éditez la partie `main` de `firewalls` ainsi :

    ```yml
    main:
        stateless: true
        provider: users
        json_login:
            check_path: auth
            username_path: login
            password_path: password
            success_handler: lexik_jwt_authentication.handler.authentication_success
            failure_handler: lexik_jwt_authentication.handler.authentication_failure
        jwt: ~
    ```

    Quelques commentaires :

    * Le paramètre `provider` fait référence au nom du `provider` que nous avons configuré juste avant.
    * `check_path` correspond au nom de la route que nous avons défini, pour la connexion.
    * `username_path` correspond au nom du paramètre correspondant au nom d'utilisateur dans le `payload` de connexion. Ici, nous avons choisi `login`.
    * Pour `password_path`, c'est la même chose que `username_path`, mais pour le mot de passe.

    Pour se connecter, on devra donc envoyer un `payload` de la forme :

    ```json
    {
        "login": "test",
        "password" : "testp"
    }
    ```

7. Videz le cache.

8. En utilisant `Postman`, envoyez une requête `POST` à la route `.../public/api/auth` (à adapter avec votre URL) en précisant un `payload` de connexion d'un utilisateur de votre application (avec un login / mot de passe valide). Vous devriez obtenir un `JWT` !

9. Sur le site [https://jwt.io/](https://jwt.io/), tentez de décoder ce jeton et observez l'information qu'il contient.
</div>

En plus du **login**, on aimerait rajouter son **identifiant**. Pour cela, on peut créer une classe qui permettra de détecter l'événement de création du `JWT` et d'y ajouter l'identifiant.

Voici cette classe :

```php
namespace App\EventListener;

use Lexik\Bundle\JWTAuthenticationBundle\Event\JWTCreatedEvent;
use Symfony\Component\HttpFoundation\RequestStack;

class JWTCreatedListener
{
    /**
     * @param JWTCreatedEvent $event
     * @return void
     */
    public function onJWTCreated(JWTCreatedEvent $event)
    {
        $payload = //A compléter
        //Insertion de données ICI - A compléter
        $event->setData($payload);
    }
}
```

Le but de la méthode `onJWTCreated` est de capter l'événement de création du token. L'événement `$event` nous permet d'utiliser diverses méthodes :

* `getData` : renvoie le `payload` du token sous la forme d'un tableau clé-valeur associatif. Il est donc possible d'y insérer des données comme dans un tableau associatif classique, en associant la donnée à un nom (une clé).

* `getUser` : renvoie l'objet `Utilisateur` correspondant au token. Il est donc possible de récupérer son id et de l'insérer dans le `payload`.

* `setData` permet de réaffecter le contenu du `payload` du token.

Une fois la classe créée, il faut l'enregistrer en tant que receveuse de l'événement dans le fichier `config/services.yml` :

```yml
    # Dans config/services.yml

    # Nom custom
    jwt_created_listener:
        # Classe prenant en charge l'événement
        class: App\EventListener\JWTCreatedListener
        # Précision de l'evenement à capter
        tags:
            - { name: kernel.event_listener, event: lexik_jwt_authentication.on_jwt_created, method: onJWTCreated }
```

<div class="exercise">

1. Créez un dossier `EventListener` dans `src` et ajoutez la classe `JWTCreatedListener` telle que définie ci-dessus puis complétez-la afin d'enregistrer l'identifiant de l'utilisateur dans le payload.

2. Éditez le fichier `config/services.yml` pour enregistrer ce gestionnaire d'événement.

3. Videz le cache puis tentez de vous authentifier de nouveau. Décodez le nouveau `JWT` obtenu et vérifiez que l'id de l'utilisateur est bien présent.

</div>

## Sécurité

Maintenant que nous pouvons nous authentifier, nous pouvons sécuriser l'accès à nos routes ! Nous allons donc voir comment :

* Autoriser l'accès à certaines routes seulement aux utilisateurs authentifiés.

* Vérifier qu l'utilisateur qui modifie/supprime une ressource en est bien le propriétaire.

* Affecter automatiquement l'utilisateur effectuant la requête comme auteur d'une publication.

### Sécurisation de l'accès aux routes

On peut limiter l'accès à une méthode sur une ressource donnée en utilisant le paramètre `security` au niveau de la méthode en question. On peut alors spécifier du **code** pour vérifier le droit d'accès à la route. Généralement, on utilise la fonction `is_granted` pour vérifier que l'utilisateur possède un certain **rôle**.

Par exemple :

```php
#[ApiResource(
    operations: [
        new Post(security: "is_granted('ROLE_USER')"),
        new Delete(security: "is_granted('ROLE_ADMIN')")
    ]
)]
```

Ici, la route utilisant la méthode `POST` sur cette ressource est uniquement accessible aux utilisateurs avec le rôle `ROLE_USER` (donc, tous les utilisateurs authentifiés). La méthode `DELETE` elle par contre n'est accessibles qu'aux utilisateurs ayant le rôle `ROLE_ADMIN`.

<div class="exercise">

1. Faites en sorte que seuls les utilisateurs authentifiés puissent créer et supprimer des publications.

2. Faites en sorte que seuls les utilisateurs authentifiés puissent modifier ou supprimer un utilisateur.

3. Videz le cache.

4. Tentez d'accéder à une des routes sécurisées et vérifiez que l'accès vous est refusé.

5. Pour pouvoir accéder à une route sécurisée, il faut vous authentifier auprès du serveur en lui envoyant le `JWT` dans les `Headers` de la requête. Pour faire cela sur `Postman`, suivez ces étapes :

    * Dans l'onglet de la requête, rendez-vous dans `Headers`.

    * Dans la première ligne, au niveau de `Key` précisez `Authorization`.

    * Au niveau de `Value`, précisez `Bearer votre_jwt` (en remplaçant par votre JWT, bien entendu)

6. Réessayez de soumettre la requête. Cette fois, cela devrait fonctionner !

</div>

### Vérification de la propriété d'une ressource

Au niveau du paramètre `security` que nous venons de voir, nous avons accès à deux objets :

* `user` : représente l'instance de l'utilisateur connecté.

* `object` : représente l'instance de la ressource en cours d'accès ou de modification.

Si l'objet ciblé a un lien avec l'utilisateur, il est alors possible de comparer un attribut de l'objet avec l'utilisateur.

Par exemple, dans le contexte d'une application de gestion de films :

```php
#[ApiResource(
    operations: [
        new Delete(security: "is_granted('ROLE_USER') and object.realisateur = user")
    ]
)]
```

La suppression d'un film n'est permisse que par un utilisateur connecté et seulement si celui-ci est le réalisateur du film.

Dans le cas où on agit directement sur la ressource `utilisateur`, on peut directement comparer `object` à `user`. Par exemple de manière simple avec `user == object`.

<div class="exercise">

1. Faites en sorte qu'une **publication** ne puisse être supprimée que par son auteur.

2. Faites en sorte qu'un utilisateur ne puisse être modifié ou supprimé que par lui-même.

3. Videz le cache puis testez vos ajouts en utilisant un `JWT` d'un utilisateur pour tenter de supprimer les publications ou le profil appartenant à un autre utilisateur.

</div>

### Affectation automatique de l'auteur d'une publication

Enfin, avec ce nouveau système d'authentification, nous pouvons logiquement automatiquement affecter l'utilisateur qui créé une publication comme auteur de celle-ci et de plus avoir à spécifier l'`IRI`(ce qui posait aussi un problème de sécurité, car on peut ainsi affecter un autre utilisateur à une publication !).

Pour cela, nous allons réutiliser le mécanisme des `processeurs` que nous avions vu plus tôt dans le cadre du hachage du mot de passe. Il nous suffit de créer un `processeur` affecté à la création d'une publication.

Voici le squelette de la classe que nous pouvons utiliser`à cet effet :

```php
namespace App\State;

use ApiPlatform\Metadata\Operation;
use ApiPlatform\State\ProcessorInterface;
use Symfony\Component\Security\Core\Authentication\Token\Storage\TokenStorageInterface;

class PublicationUserSetter implements ProcessorInterface
{
    public function __construct(private readonly ProcessorInterface $processor,
                                private readonly TokenStorageInterface $storage)
    {
    }

    public function process($data, Operation $operation, array $uriVariables = [], array $context = [])
    {
        //A compléter

        $this->processor->process($data, $operation, $uriVariables, $context);
    }

}
```

Votre objectif sera de compléter la méthode `process` :

* À partir de l'attribut `storage` il vous est possible de récupérer le `JWT` en appelant la méthode `getToken`.

* Vous pouvez accéder à l'objet `Utilisateur` représentant l'utilisateur authentifié en appelant la méthode `getUser` sur le token.

* L'objet `$data` représente la publication en train d'être créée, il est donc possible de lui **affecter un auteur**.

* Les attributs `$processor` et `$storage` seront injectés (à préciser dans la configuration du service).

<div class="exercise">

1. Créez la classe `PublicationUserSetter` dans le dossier `src/State` avec le squelette défini ci-dessus puis complétez la méthode `process`.

2. Dans `config\services.yaml`, configurez ce service comme nous l'avions fait pour `UserPasswordHasher`. Les services à injecter sont :

    ```yml
    $processor: '@api_platform.doctrine.orm.state.persist_processor'
    $storage: '@security.token_storage'
    ```

3. Au niveau de la classe `Publication`, spécifiez votre nouveau processeur au niveau de la méthode `POST`.

4. Toujours dans la même classe, servez-vous de l'annotation `#[ApiProperty]` pour interdire l'écriture de l'auteur. Retirez également les annotations `NotBlank` et `NotNull` que vous aviez sans doute placé sur cette propriété, précédemment.

5. Videz le cache puis tentez de créer une nouvelle publication (toujours en attachant votre `JWT` aux `Headers` de la requête). Vérifiez alors que la publication est bien créée et que l'auteur a bien été affecté par rapport à l'utilisateur représenté par le `JWT` que vous utilisez.

</div>

## Conclusion

Nous avons terminé de construire notre `API` ! A priori, nous ne la modifierons plus (ou très peu). Elle est complète et prête à l'emploi pour être utilisée dans notre application mobile de **The Feed** que nous allons construire lors du [TD3]({{site.baseurl}}/tutorials/tutorial3.html)!

Vous avez pu constater la puissance de l'outil `API Platform`. Nous ne sommes pas beaucoup sortis des classes **entités** et la majeure partie de la logique métier de l'application est spécifiée grâce aux annotations dans ces classes.