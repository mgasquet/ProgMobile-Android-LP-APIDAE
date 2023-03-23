---
title: TD1 &ndash; Introduction à Android et utilisation d'une API
subtitle: Android Studio, Java, IHM, API
layout: tutorial
lang: fr
---

## Installation d'Android Studio

**Android Studio** est un **IDE** dérivé de l'IDE **IntelliJ IDEA** et développé par Google afin de faciliter la création d'applications mobiles pour le système **Android**. Il permet de visualiser les différentes vues de l'application, mais aussi de créer et d'exécuter différents **émulateurs** (smartphones, tablettes, montres connectées, panneau de commandes d'une voiture...).

Le programme et les émulateurs de matériel sont assez volumineux (et peuvent consommer pas mal de ressources...), votre session étant limitée en espace, nous allons mettre en place des astuces pour pouvoir faire tourner ce programme.

Si vous possédez votre propre machine, l'installation de cet IDE sera plus simple, mais attention, le téléchargement peut prendre un certain temps.

Selon votre situation, référez-vous donc à la section correspondante.

### Sur les machines de l'IUT

Afin qu'Android Studio n'explose pas votre quota disque, nous allons mettre en place des astuces à base de liens symboliques et d'utilisation du dossier temporaire global. **Attention**, ces étapes devront être **répétées au début de chaque TD**, car cette configuration est effacée après un redémarrage.

*Remerciements à [**Fabien Michel**](https://www.lirmm.fr/~fmichel/) et à l'équipe technique de l'IUT pour leurs ressources et explications à ce sujet*.

1. Ouvrez un terminal, et positionnez-vous à la racine de votre compte :

```bash
cd ~
```

2. Exécutez les commandes suivantes :

```bash
rm -rf .config/Google
rm -r .gradle .android
rm -r Android
ln -s /tmp .gradle
ln -s /tmp .android
rm -rf /home/vb/gradle/daemon
```

3. Lancez **Android Studio** avec la commande suivante :

```bash
/opt/android-studio/bin/studio.sh
```

4. Choisissez l'option **Do not import Settings**.

5. Dans l'écran **Install Type** sélectionnez **Custom**.

6. Poursuivez jusqu'à la fenêtre **SDK Components Setup**. Au niveau du champ **Android SDK Location** sélectionnez le répertoire **/home/vb/android-sdk**.

7. Acceptez les licences et poursuivez. **Une erreur peut se produire**. Si cela arrive, pas de panique, nous allons régler cela à la prochaine étape.

8. Dans le cas où une erreur s'est produite à l'étape précédente, poursuivez, le menu de l'IDE devrait quand même s'ouvrir. Cliquez sur le bouton **More actions** au milieu du menu puis sélectionnez **SDK Manager**. Au niveau du champ **Android SDK Location** tout en haut, sélectionnez (de nouveau) le répertoire **/home/vb/android-sdk**, appliquez et validez. Cette fois-ci, cela devrait fonctionner!

Vous voilà sur le menu de lancement!

### Sur votre machine personnelle

Au niveau de votre machine personnelle, il vous suffit de télécharger **Android Studio** sur le [site officiel](https://developer.android.com/studio). Cependant, sur les machines de l'IUT, nous allons utiliser une version un peu plus ancienne d'Android Studio. Certaines explications pourraient alors (léégérement) différées (boutons / menus pas au même endroit). Cela ne devrait pas être trop gênant, mais si vous souhaitezutiliser exactement la même version que celle installée à l'IUT vous pouvez la [télécharger à cette adresse](https://developer.android.com/studio/archive?hl=fr). Il s'agit de la version : **Android Studio Chipmunk, 2021.2.1 Correctif 2, 3 août 2022**.

## Découverte

**Avant de commencer**, inscrivez-vous sur ce site : [https://home.openweathermap.org/users/sign_up](https://home.openweathermap.org/users/sign_up) afin d'obtenir par mail une **clé d'API** qui sera necessaire plus loin dans le TD. La clé met un certain temps à être activée, il vaut mieux donc faire cette étape dès le début du TD. Notez bien la clé reçue par mail, il vous sera donc utile plus tard, dans la suite du TD.

Dans cette première section, vous allez créer des **applications** basiques et créer divers **émulateurs** afin de vous familiariser avec **Android Studio**.

Tout au long du **TD**, vous pouvez retrouver les explications nécessaires et les exemples au niveau du [cours]({{site.baseurl}}/classes/cours1.pdf).

### Les émulateurs

<div class="exercise">

1. Sur **Android Studio** et créez un nouveau projet nommé `AppliIntro`. Android Studio devrait vous proposer de choisir un modèle d'**activity** initial. Choisissez `Empty Activity`. Dans l'écran suivant, choisissez `Java` comme langage et l'**API 26** comme `Minimum SDK`. Le projet s'initialise, cela prend un certain temps.

2. Dans le menu `AVD Manager` (accessible depuis `Tools`), cliquez sur `Create Virtual Device` et créez un **nouvel émulateur de téléphone** en choisissant le modèle `Nexus 5X` et **l'API 26** d'Android. Celle-ci n'étant pas encore installée sur votre ordinateur, il faudra cliquer sur le bouton **download** avant de poursuivre. Choisissez l'orientation `portrait`, donnez le nom de votre choix puis finalisez la création de l'émulateur.

3. Au niveau du menu `AVD Manager`, lancez votre émulateur. Sur le panneau qui s'ouvre en bas à droite d'Android Studio, effectuez un clic droit sur l'onglet de l'émulateur puis sélectionnez `View Mode` ⇾ `Window`. Explorez un peu le téléphone. **Attention**, pour fermer proprement l'émulateur du téléphone, fermez l'onglet, dans la fenêtre.

4. En répétant les étapes précédentes, créez un émulateur de `tablette` avec le modèle **Nexus 10**, utilisant aussi **l'API 26**. Explorez votre nouvelle tablette.

5. Au niveau de la **barre d'outils** (en haut à droite) choisissez votre émulateur de téléphone puis **exécutez l'application** avec le bouton de lancement (icône verte d'un bouton "play"). Un écran affichant "Hello World!" devrait s'ouvrir sur votre émulateur. Fermez l'application et explorez votre téléphone. Vous devriez pouvoir retrouver votre application.

6. Éventuellement, si vous avez amené votre téléphone et que vous possédez un câble de branchement USB permettant de le connecter à l'ordinateur, vous pouvez tester de lancer votre application sur votre téléphone personnel (mais pas si c'est un iPhone, bien sûr). Pour cela, il faut au préalable activer le **mode développeur** sur votre appareil :

    * Rendez-vous dans les paramètres du téléphone, puis sur l'écran **"A propos du téléphone"** et **appuyez 7 fois sur "Numéro de build"**. 
    
    * Revenez en arrière et trouvez le menu **"Options pour développeur"**. Si vous ne trouvez pas ces options, vous pouvez chercher sur internet en précisant le modèle de votre téléphone. Dans ce menu, activez l'option **"Débogage USB"**. 
    
    * Sur Android Studio, vous devriez être en mesure de sélectionner votre appareil comme périphérique de lancement pour votre application.

7. Vous avez sans doute remarqué la présence d'autres types d'émulateurs (pour montre, pour télévision, pour voiture...). Vous pouvez essayer de créer un émulateur d'un de ces types d'hardware.

</div>

Vous êtres fin prêts à développer des **applications mobiles**!

### Une application basique

Dans le [cours](({{site.baseurl}}/classes/cours1.pdf)), nous avons vu que le développement des "vues" d'une application (les différentes interfaces) se faisait au travers de fichiers `XML` afin de séparer la partie présentation du code métier (un peu à la manière d'un fichier `HTML` séparé de son fichier `JS`). Nous allons donc commencer par développer cette interface.

<div class="exercise">

1. Ouvrez le fichier `activity_main.xml` se situant dans `res/layout`. Réglez l'affichage en mode `split` (en haut à droite).

2. Remplacez le `ContraintLayout` par un `LinearLayout` (changez seulement le nom de la balise) puis supprimez le composant `TextView` présent dans le code.

3. Donnez une orientation `vertical` à votre `LinearLayout` puis spécifiez une `gravity` de type `center`.

4. Dans votre `LinearLayout`, ajoutez un `TextView`. Précisez la largeur et la hauteur avec la valeur `wrap_content`. Réglez le **margin vertical** à `10dp` et le contenu textuel à **"Texte d'exemple!"**. Enfin, changez la **taille du texte** pour qu'elle soit égale à `20sp`.

5. Dans votre `LinearLayout`, ajoutez un autre `LinearLayout` (après le TextView) avec une orientation `horizontal`. Précisez la largeur et la hauteur avec la valeur `wrap_content`.

6. Dans votre second `LinearLayout`, ajoutez deux `Button` qui possèdent chacun une **marge horizontale** (margin...) de `10dp` et une largeur / hauteur affectée avec `wrap_content`. Le premier bouton doit afficher le texte **"Rouge"** et son attribut `backgroundTint` doit être égal à **#FF0000**. Le second bouton doit afficher **"Vert"** et son `backgroundTint` doit être égal à **#008000**.

7. Donnez un **identifiant** au `TextView` et aux deux éléments `Button`.

</div>

Maintenant que nous avons réalisé l'interface, il faut coder le **comportement** (code métier) lié à cette interface. Pour cela, nous pouvons relier le document `XML` à une classe **Java**. Dans cette classe, il est alors possible de récupérer les différents composants grâce à leur **identifiant**.

<div class="exercise">

1. Dans votre classe `MainActivity` (dans `com.example.appliintro`), faites en sorte de récupérer les `TextView` et les deux éléments `Button` comme attributs de la classe, en utilisant leurs **identifiants** dans la méthode **onCreate** (vous pouvez trouver comment faire dans le [cours](({{site.baseurl}}/classes/cours1.pdf))).

2. Toujours dans `MainActivity`, créez une méthode `changerTexteEnRouge` prenant un objet `View` en paramètre et ne retournant rien. Cette méthode doit changer la couleur de l'élément `TextView` en **rouge**. Pour cela, vous devrez utiliser la méthode `setTextColor` et la valeur `Color.RED`. Nous n'utiliserons pas l'attribut `View` passé en paramètre, mais il nous est utile pour réaliser simplement la **programmation événementielle**.

3. Faites en sorte de gérer l'événement `onClick` sur le bouton **rouge**. Lorsqu'on clique sur ce bouton, le programme doit déclencher la méthode `changerTexteEnRouge` (il faut donc modifier le fichier `XML` de l'interface).

4. Implémentez un comportement similaire avec le bouton **vert** afin de changer le texte en vert (nouvelle méthode + gestion de l'événement).

5. Lancez votre application (sur un émulateur de téléphone ou sur votre propre téléphone) et vérifiez que tout fonctionne.

</div>

Bravo, vous avez créé (et testé) votre première application! Nous allons maintenant développer une application un peu plus poussée avec plusieurs vues/interfaces.

### Une interface de connexion

Nous allons créer une petite application avec un système (semi-factice) de connexion. Le but sera de rediriger l'utilisateur vers une nouvelle interface (son espace personnel) lorsqu'il se connecte.

<div class="exercise">

1. Créez un nouveau projet nommé `AppConnexion` en sélectionnant une `Empty Activity` comme activité de base.

2. Nous voulons obtenir une interface comprenant :

    * Un **élément textuel** "Connectez-vous", assez gros.
    * Un **champ de saisie** pour un **nom d'utilisateur**.
    * Un **champ de saisie** pour un **mot de passe**.
    * Un **bouton** "Connexion".

    Modifiez le document `activity_main.xml` afin d'implémenter cette interface. Vous devrez probablement remplacer le `ConstraintLayout` par un autre (par exemple `LinerarLayout`). Pensez également à donner des `identifiants` aux éléments graphiques de votre interface. Vous êtes libre sur les choix de design, mais globalement, on aimerait globalement que le tout soit centré verticalement.

    Quelques petites **astuces** : 

    * Sur vos champs de saisie, vous pouvez utiliser l'attribut `hint` pour spécifier un texte en "arrière-plan" de la zone de saisie qui s'effacera automatiquement quand l'utilisateur commencera à écrire. Un `hint` est une indication que l'on donne à l'utilisateur sur la nature du champ (on a vu la même chose dans les formulaires HTML). Vous pouvez également ajouter l'attribut `imeOptions` avec pour valeur `actionDone` pour que l'utilisateur puisse fermer le clavier simplement une fois sa saisie terminée. 
    
    * Pour n'autoriser la saisie que sur une seule ligne (pas de saut de lignes autorisé) vous pouvez utiliser l'attribut `singleLine` réglé sur `true`.

    * Pour que la saisie du `mot de passe` s'affiche correctement, vous devez correctement régler l'attribut `inputType`!

    * Il faut penser à utiliser judicieusement les attributs `gravity` et `layout_gravity` pour aligner correctement les éléments. De même, pour la taille des éléments, relisez bien le passage du [cours](({{site.baseurl}}/classes/cours1.pdf)) concernant `wrap_content` et `match_parent`

3. Faites en sorte de récupérer les deux champs de saisies ainsi que le bouton de connexion comme attributs dans votre classe `MainActivity`. Ils devront être initialisés dans le `onCreate` en utilisant leurs `identifiants`.

4. Créez une méthode `connexion` prenant un paramètre de type `View` (comme précédemment, nous ne nous servirons pas de ce paramètre, c'est juste pour faciliter la mise en place de la programmation événementielle). Cette méthode doit **vérifier que le mot de passe saisi est valide** (choisissez le mot de passe "valide" votre choix, par exemple "android"). On ne se soucie pas du nom d'utilisateur pour le moment. Si le mot de passe n'est **pas valide**, l'application doit afficher une **boîte de dialogue** (alerte) informant l'utilisateur que le mot de passe est invalide. Nous traiterons plus loin le cas où le mot de passe est valide. Vous aurez besoin d'explorer les méthodes du composant `EditText` pour trouver celle qui vous donne le texte saisi (c'est un nom assez simple et logique).

5. Faites en sorte que lorsqu'on appuie sur le `bouton "Connexion"`, la méthode `connexion` soit déclenchée. Lancez votre application et vérifiez que tout marche comme attendu en saisissant un mot de passe invalide.

</div>

Maintenant, il ne nous reste plus qu'à coder l'interface correspondant à l'espace personnel de l'utilisateur et afficher cette interface si l'utilisateur se connecte avec les bons identifiants. Pour cela, nous allons utiliser un `intent` explicite que nous avons vu en cours.

<div class="exercise">

1. Créez une **nouvelle activité** nommée `EspacePersonnelActivity` et utilisant le modèle `Empty Activity` (en utilisant les outils d'**Android Studio**). Deux fichiers sont générés : une classe `java` et un fichier `XML` (dans `res/layout`).

2. Dans le document `XML` de cette nouvelle `activité`, faites en sorte de centrer un élément textuel **"Bienvenue : "** suivi d'un autre élément textuel `vide` (ne contenant pas de texte), les deux placés côte à côte, horizontalement. Pensez à donner un `identifiant` à votre élément textuel vide.

3. Cette **nouvelle activité** sera affichée quand l'utilisateur aura rentré le bon mot de passe dans la première activité. Son but est d'afficher **"Bienvenue : nom d'utilisateur"** une fois la "connexion" effectuée. On doit donc faire en sorte de relier les deux `activités` et de les faire communiquer (la seconde activité doit récupérer le nom d'utilisateur). Dans votre méthode `connexion`, lorsque l'utilisateur a rentré un **mot de passe valide**, faites en sorte de lancer l'activité `EspacePersonnelActivity`. Pour cela, il faudra utiliser un `intent` (explicite). Passez des `données supplémentaires` à votre `intent` en ajoutant une donnée nommée `nomUtilisateur` correspondant au **nom d'utilisateur** saisi (se récupère de la même manière que pour le mot de passe).

4. Dans votre classe `EspacePersonnelActivity`, faites en sorte de récupérer l'élément textuel vide définit dans l'interface comme attribut dans la classe. Dans la méthode `onCreate`, initialisez l'élément textuel en utilisant son `identifiant` puis récupérez le **nom d'utilisateur** en allant lire les données de votre `intent` et remplacez le texte de l'élément textuel vide avec ce **nom d'utilisateur**.

5. Lancez votre application et tentez d'accéder à "l'espace personnel" en saisissant un bon mot de passe. Vérifiez que le nom d'utilisateur que vous avez saisi s'affiche bien dans ce nouvel écran.

</div>

## Utilisation d'une API

Comme nous l'avons vu en cours, les APIs ont un rôle central dans le cadre de développement de services liés au web, car elles permettent de détacher le **backend** de son rôle traditionnel (et un peu obsolète) du "rendu" de l'IHM. Le but de l'API est seulement de traiter les requêtes, appliquer des vérifications et renvoyer les données sous un certain format (généralement, du `JSON`). Ainsi, le service web développé est compatible avec diverses technologies comme une application web réactive (Angular, React...) ou bien, dans notre cas, une **application mobile**.

En fait, dans beaucoup de cas, si une application mobile propose des services liés aux données (gestion d'utilisateurs, affichage d'horaires de trains, réseaux sociaux...) il y a généralement une **API** derrière. Par exemple, **Twitter** possède sa propre API (que vous pouvez utiliser!) qui est à la fois utilisée dans l'application mobile et sur le site web.

### Une application météo

Dans la suite des exercices, nous allons utiliser une **API Web** afin d'afficher la **météo** d'une ville saisie par l'utilisateur.

Tout d'abord, commençons par mettre en place la base de l'application.

<div class="exercise">

1. Créez un nouveau projet nommé `MyMeteo` en sélectionnant une `Empty Activity` comme activité de base.

2. Modifiez l'interface de l'activité principale, pour obtenir un rendu similaire à celui de la capture d'écran ci-dessous. C'est-à-dire, un ensemble centré contenant :

    * Un `élément textuel` "Rentrez un nom de ville"
    * Un `champ de saisie` (sur une seule ligne)
    * Un `bouton` "Voir météo" (la taille du texte a été légèrement augmenté)

    Globalement, vous pouvez suivre la stratégie suivante : un conteneur `LinearLayout` orienté `verticalement` avec une `gravity` fixée sur `center`. Pour chaque élément, il faut judicieusement fixer la largeur et la hauteur (`wrap_content` ou `match_parent` ?). Vous pouvez ajouter un peu de `margin vertical` entre les éléments et également un peu de `margin horizontal` sur le champ de saisie. Pensez à donner un **identifiant** au champ de saisie et au bouton.

    <p style="text-align:center">
    <img style="width:30%" src="{{site.baseurl}}/assets/TD1/meteo1.png">
    </p>

3. Dans votre classe `MainActivity`, faites en sorte de récupérer le champ de saisie et le bouton comme attributs de la classe. Ils devront être initialisés dans le `onCreate` en utilisant leurs **identifiants**.

4. En utilisant les outils d'`Android Studio`, créez une nouvelle activité (toujours `Empty Activity`) nommée `MeteoActivity`.

5. Modifiez l'interface de cette nouvelle activité pour avoir un rendu similaire à la capture d'écran ci-dessous. C'est-à-dire, 4 éléments textuels assez gros et un bouton alignés au centre et disposés **verticalement** (cela se fait très facilement avec un `LinearLayout` correctement réglé). Pensez à donner un identifiant à chaque élément. Du côté de la classe `MeteoActivity`, il faudra récupérer ces éléments comme nous l'avons fait précédemment avec les autres interfaces.

    <p style="text-align:center">
    <img style="width:30%" src="{{site.baseurl}}/assets/TD1/meteo2.png">
    </p>

6. Faites en sorte de déclencher l'ouverture de `MeteoActivty` depuis `MainActivity` via un `intent` lorsque l'utilisateur appui sur le bouton "Voir météo". Il faudra passer à l'`intent` la donnée du nom de la ville saisi par l'utilisateur. De son côté, `MeteoActivity` doit récupérer cette donnée en lisant dans son `intent` et remplacer le texte de l'élément textuel "Nom Ville" avec le nom de la ville récupérée dans l'`intent`.

7. Faites en sorte que l'appui sur le bouton "Retour" ouvre l'écran correspondant à `MainActivity`. Il faudra aussi utiliser un `intent`, mais sans passer de données supplémentaires (MainActivity n'attend pas de données particulières, contrairement à MeteoActivity).

8. Lancez votre application et vérifiez qu'on peut bien passer d'un écran à l'autre et que le nom de la ville saisie par l'utilisateur s'affiche bien sur le second écran.

</div>

Afin d'obtenir les informations sur la **météo**, nous allons utiliser l'`API` suivante : [openweathermap](https://openweathermap.org/api). 

Elle s'utilise de manière très simple avec des **requêtes HTTP**. Par exemple, le lien suivant permet d'obtenir les informations météo de **Montpellier** (en remplaçant `votre_cle` par celle obtenue par mail après isncription, au début du TD) : [http://api.openweathermap.org/data/2.5/weather?q=Montpellier&APPID=votre_cle&lang=fr&units=metric](http://api.openweathermap.org/data/2.5/weather?q=Montpellier&APPID=votre_cle&lang=fr&units=metric). Explorez les informations météo de différentes villes en utilisant cette `API`.

La clé permet de vous authentifier auprès de l'API. C'est une protection courante, afin de limiter le nombre d'appels à l'API ou bien pour proposer des verions payantes permettant un nombre illimité d'appels, plus d'ptions, etc. La clé obtenue avec la verison gratuite de cette API sera amplement suffisante pour nos besoins.

Maintenant, il nous faut une classe qui va permettre d'aller interroger l'`API` météo et de convertir l'information récupérée en un format exploitable dans notre application. Nous allons utiliser la librairie `android-async-http` qui permet de faire des requêtes **asynchrones** (à la manière de Javascript, par exemple). Nous allons combiner cela avec les objets de type `Consumer` de Java. 

Un objet `Consumer` est une interface qui représente une **fonction** qui peut être passée en paramètre à une autre fonction et appelée. Nous allons notamment nous en servir comme **callback** dans les requêtes asynchrones (une fois le résultat reçu, le **callback** sera appelé). Voici un exemple :

```java
public class A {

    public static void funA(int a, int b, Consumer<Integer> callback) {
        int somme = a + b;
        callback.accept(somme);
    }

}

public class B {

    public void funB() {
        A.funA(5, 9, this::traitement);
        //Ou bien : A.funA(5, 9, result -> this.traitement(result));
    }

    public void traitement(int result) {
        ...
    }

}
```

Dans le paramètre de type `Consumer`, on doit préciser le type de paramètre de la fonction (ici, un `Integer`). Ici, on fait donc référence à une fonction qui prend en paramètre un entier. Dans notre exemple, à la fin de `funA`, la fonction `traitement` de l'instance de la classe `B` sera appelée. On aurait pu préciser une autre méthode, tant que celle-ci prend un paramètre de type entier.

Lors de l'appel de la fonction qui prend en paramètre le `Consumer`, on peut faire référence à la fonction cible de deux manières :

* parametre -> appelFonction(parametre)
* nomFonction

Dans une classe, on peut faire référence à une méthode cible en utilisant le mot clé `this` :

* parametre -> this.appelFonction(parametre)
* this::nomFonction

Nous allons pouvoir mettre tout cela en lien avec la librairie `android-async-http` qui s'utilise ainsi :

```java
AsyncHttpClient client = new AsyncHttpClient();

//Effectue une requête GET sur l'URL (nous reviendrons sur le responseHandler après)
client.get(url, responseHandler);

//Effectue une requête POST sur l'URL. On peut préciser des paramètres (pour rappel, avec POST, nous n'utilsons plus le query string pour passer de l'information)
client.post(url, parametres, responseHandler);

//Mise à jour totale de la ressource
client.put(url, parametres, responseHandler);

//Mise à jour partielle de la ressource
client.patch(url, parametres, responseHandler);

//Supression de la ressource
client.delete(url, responseHandler);
```

Ainsi, on peut alors exécuter tout type de requête vers une `API REST`.

Le `responseHandler` correspond à un objet qui permet de traiter la réponse. Il en existe de plusieurs types, mais nous allons principalement utiliser un `JsonHttpResponseHandler` qui permet de traiter les réponses sous le format `JSON` (il en existe d'autres, pour différents formats). L'usage est de créer directement cet objet lors de l'appel de la méthode, de manière **anonyme**. Il est alors possible de redéfinir plusieurs méthodes, selon la réponse obtenue :

```java
client.get(url, new JsonHttpResponseHandler() {
    @Override
    public void onSuccess(int statusCode, Header[] headers, JSONObject response) {
        //Dans le cas où la réponse est un objet JSON simple
        ...
    }

    @Override
    public void onSuccess(int statusCode, Header[] headers, JSONArray response) {
        //Dans le cas où la réponse est un tableau d'objets JSON
        ...
    }

    @Override
    public void onFailure(int statusCode, Header[] headers, String responseString, Throwable throwable) {
        //Dans le cas où il y a une erreur lors de la requête...
        ...
    }
});
```

Vous pouvez redéfinir les méthodes que vous voulez, selon la logique de votre code. Par exemple, une requête visant une ressource précise va retourner un `JSONObject`. Il faut donc implémenter le `onSuccess` prenant en paramètre un `JSONObject`. Par contre, une requête `get` plus générale (par exemple, récupérer toutes les livres d'une bibliothèque) renverra un `JSONArray`.

Concernant les **paramètres** passés lors d'une requête `POST`, `PUT` ou `PATCH`, dans le cas d'une `API REST` il s'agit d'un objet `JSON` (appelé le `payload`). Dans ce TD, nous n'utiliserons pas ces méthodes (nous n'avons accès qu'à `GET` sur l'API Météo) mais nous y reviendrons lors du prochain TD.

<div class="exercise">

1. Ouvrez le **second** fichier `build.gradle` situé dans `Gradle Scripts`. Au niveau de la section `dependencies`, ajoutez la ligne suivante :

    ```groovy
    implementation 'com.loopj.android:android-async-http:1.4.9'
    ```

    Après cet ajout, cliquez sur le bouton `Sync Now` qui a dû apparaitre en haut à droite.

2. Créez un sous-package `api` dans `com.example.mymeteo` (à moins que vous ayez changé les noms de packages, dans ce cas, adaptez).

3. Créez une classe `MeteoAPIConnector` dans ce nouveau package.

4. Ajoutez un **attribut de classe** (donc `static`) de type `AsyncHttpClient` nommé `client`. Initialisez-le au niveau de la déclaration de l'attribut.

5. Ajoutez une **méthode de classe** (donc `static` là aussi) nommée `getMeteo` qui prend en paramètre :

    * Une chaîne de caractères `nomVille`.

    * Un objet `Consumer` configuré avec un objet `JSONObject` correspondant au `callback` qui sera appelé à l'issue de la requête vers l'API.

    Cette fonction doit :

    * Effectuer un appel (ici, un `get` simple) vers l'API de météo pour récupérer les informations météo de la ville passée en paramètre.

    * Traiter le retour de la requête (sous la forme d'un `JSONObject`) en exécutant le `callback` correspondant au `Consumer` passé en paramètre en lui passant le résultat (le `JSONObject`). Il faudra utiliser la bonne méthode `onSuccess`. Nous ne traiterons pas les erreurs pour le moment (donc pas de `onFailure`).

</div>

Très bien, nous avons maintenant de quoi faire appel à l'API et récupérer le résultat et le transmettre à la bonne fonction. Il ne nous reste donc plus qu'à traiter le résultat en affichant les données météo de la ville sélectionnée sur l'interface de l'application.

Un objet `JSONObject` s'utilise plus ou moins comme une `map`(dictionnaire). On peut préciser l'élément que l'on veut récupérer en donnant une **clé** (nom de l'attribut). On doit également utiliser une méthode correspondant au type de donnée que l'on veut récupérer (`getInt`, `getString`, `getDouble`, etc.). Par exemple, si on analyse les réponses de l'API, trois éléments nous intéressent : `temp`, `speed` et `description`, situés dans des sous-parties de l'objet `JSON`. Il y a deux nombres à virgules et un `String`. Ainsi, le code suivant permet, par exemple, de récupérer la température de `Montpellier` :

```java
public void updateMeteo(JSONObject map) {
    //Temperature de Montpellier
    //La temperature est dans un sous-objet "main"
    JSONObject main = map.getJSONObject("main");
    //La température est un nombre à virgules
    double temperature = main.getDouble("temp");
    ...
}
```

<div class="exercise">

1. Dans votre classe `MeteoActivity`, créez une méthode `updateMeteo` (comme dans l'exemple ci-dessus) dont le but est de remplacer le texte des éléments textuels de l'interface avec les données contenues dans le `JSONObject`. 

On utilise "temp" situé dans l'élément "main" pour obtenir la **Temperature**. On utilise "description" situé dans l'objet situé à la première case du tableau "weather" pour **Temps**. Enfin, on utilise "speed" dans l'élément "wind" pour l'élément **Vent**. Attention, avant d'exécuter les modifications, vérifiez bien que votre objet `map` n'est pas `null` (ou votre application pourrait éventuellement planter dans certains cas).

Aussi, concernant l'élément `weather` il s'agit d'un **tableau**. Vous devez donc récupérer le `JSONArray` correspondant à l'élément `weather`. Une fois ce tableau récupéré, vous pouvez appeler la méthode `getJSONObject(index)` dessus, ce qui permet d'obtenir un élément JSON situé à l'index indiqué.

Vous pouvez convertir un `double` en `String` avec `Double.toString(nombre)`.

2. Toujours dans `MeteoActivity`, dans le `onCreate`, en plus de modifier le nom de la ville sur l'interface, faites en sorte qu'une requête soit envoyée vers l'API en utilisant `MeteoAPIConnector` et en spécifiant `updateMeteo` comme méthode de **callback**. Il faudra remplacer le nom de la ville de la requête par le nom de la ville récupéré depuis l'`intent`.

3. Avant de pouvoir lancer votre application, comme celle-ci utilise `internet`, il faut lui en donner la permission. Rendez-vous dans `manifests/AndroidManifest.xml` et ajoutez la ligne suivante dans la zone définie par les balises `manifest` :

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

4. Lancez votre application, saisissez un nom de ville puis validez. L'écran correspondant à `MeteoActivity` devrait s'ouvrir et, après un court instant, tous les textes devraient être remplacés par les informations de la météo actuelle de la ville spécifiée.

</div>

### Amélioration de l'application

Nous allons réaliser divers ajouts afin d'améliorer notre application de météo.

Tout d'abord, on aimerait que les informations de l'écran `MeteoActivity` soient "cachées" le temps qu'on obtienne la réponse de l'API. Pour cela, on peut utiliser un élément `ProgressBar` (dans le XML) pour afficher un chargement pendant ce délai.

<div class="exercise">

1. Dans votre fichier `activity_meteo.xml`, ajoutez un composant `ProgressBar` dans le `LinearLayout` dont la hauteur et la largeur devra **correspondre à celle du parent** (trouvez la bonne valeur à utiliser). Ensuite, regroupez les 4 éléments textuels et le bouton dans un `LinearLayout`. 

2. Changez la visibilité du "sous" `LinearLayout` (contenant les éléments textuels et le bouton) à `gone`. Cela a pour effet de faire "disparaître" ces éléments. Donc, lorsque l'écran est chargé, il n'y a que la `ProgressBar` de visible.

3. Dans `MeteoActivity`, récupérez les deux nouveaux éléments créés (avec les identifiants) comme attributs dans la classe puis, dans votre méthode `updateMeteo`. Après la mise à jour des champs textuels, faites en sorte de changer la **visibilité** de la `ProgressBar` à `gone` et celle du "sous" `LinearLayout` à `visible`. Cela aura pour effet de cacher l'animation de chargement et de dévoiler les éléments textuels de notre interface une fois le chargement des informations terminé.

4. Lancez votre application et testez que le chargement se déroule comme souhaité lorsqu'on souhaite afficher la météo d'une ville.

</div>

On souhaite que les textes de l'application soient **internationalisés**. On dira que, par défaut, la langue par défaut de l'application est l'anglais, et qu'on veut aussi proposer une version française.

<div class="exercise">

1. Dans `res/values/strings.xml`, créez des nouvelles associations clés-valeurs pour les différents textes de l'application. À la place du français, vous traduirez ces textes en anglais.

2. Remplacez les textes dans `activity_main` et `activity_meteo` en faisant référence aux clés (`@string/cle`)

3. Cliquez sur le dossier `res/values` puis effectuez un clic-droit et sélectionnez `New` ⇾ `Value Resource File`. Nommez le fichier `strings.xml` et pour nom de dossier, spécifiez `values-fr`.

4. Ouvrez votre nouveau fichier et copiez-collez le contenu du fichier `string.xml` (anglais) en traduisant les textes en français.

5. Lancez l'application. Dans le téléphone émulé, changez la langue du système et vérifiez que le texte change bien selon la langue (français ou en anglais par défaut, pour toutes las autres langues).

</div>

On imagine le scénario suivant : on se balade sur internet, et on voit le nom d'une ville marqué sur un article, dans un site, sur une conversation... On aimerait rapidement pouvoir connaître la météo dans cette ville. On voudrait pouvoir aisément sélectionner le texte et l'envoyer à notre application pour traitement, sans avoir à le copier / coller. Pour cela, on peut utiliser un **intent implicite**!

<div class="exercise">

1. Pour commencer, nous allons déclarer que notre écran `MeteoActivity` est capable de gérer la réception d'un texte (le nom de la ville). Pour cela, il faut modifier la déclaration de `MeteoActivity` dans `manifests/AndroidManifest.xml` :

    ```xml
    <activity android:name=".MeteoActivity" android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.SEND" />
            <category android:name="android.intent.category.DEFAULT" />
            <data android:mimeType="text/*"/>
        </intent-filter>
    </activity>
    ```

    Grâce à ces lignes, on informe le système installant notre application que `MeteoActivity` peut gérer l'action `SEND` (partager) d'un élément textuel. Ainsi, lorsqu'on voudra réaliser cette action sur l'appareil, notre application sera listée dans la liste des choix possibles et `MeteoActivity` pourra être démarrée directement via un `intent` contenant le texte partagé.

2. Lorsque vous spécifiez (ou lisez) le **nom de la ville** dans votre `intent`, vous utilisez sûrement une chaîne de caractère (clé) que vous avez définie de manière littérale (par exemple "nomVille"). Nous allons changer cela, car, lors du traitement d'un `intent implicite`, la donnée envoyée est associée à un nom de clé bien précise. Dans notre cas, il s'agit de `Intent.EXTRA_TEXT`. Remplacez la clé que vous utilisez pour **écrire** le nom de la ville dans l'`intent` dans `MainActivity` par la clé `Intent.EXTRA_TEXT` et faites de même dans `MeteoActivity` quand vous **lisez** le nom de la ville dans l'`intent`.

3. Lancez votre application et recherchez la liste des grandes villes françaises sur Google. Réalisez un **appui long** sur le nom de ville de votre choix afin de le sélectionner puis appuyez sur **partager** puis **share**, (dans un système en anglais). Au moment de partager, si votre appareil vous propose de partager un `lien`, cliquez sur la petite icône de lien (semblable à un petit trombone) en haut à droite pour annuler ce comportement. Votre application `MyMeteo` devrait être listée dans les choix d'applications. Sélectionnez-la et vérifiez que les données météo de la ville sont bien affichées.

</div>

On aimerait maintenant donner une **icône personnalisée** à notre application.

<div class="exercise">

1. Tout d'abord, téléchargez l'image de votre choix, qui sera utilisée comme icône. Dans `Android Studio`, cliquez sur `New`, `Image Asset`. Dans le menu qui s'ouvre, sélectionnez `Launcher Icons` au niveau du champ `Icon type`. Donnez le nom de votre choix puis sélectionnez `Image` dans `Asset Type`. Au niveau de `Path` cliquez sur le dossier et allez chercher l'image que vous avez téléchargé, sur votre ordinateur. Enfin, cliquez sur `Next` puis `Finish`. Dans le sous-dossier `res/drawble`, vous devriez observer votre image sous différents formats dans un dossier portant le nom de votre ressource.

2. Pour déclarer votre image comme **icône de l'application**, rendez-vous dans `manifests/AndroidManifest.xml` et modifiez les deux lignes `android:icon` et `android:roundIcon` en spécifiant le nom de votre ressource, créée à l'étape précédente. Pour le deuxième (`roundIcon`) rajoutez `_round` à la fin du nom de votre ressource (lorsque vous avez importé votre image, elle a été convertie dans différents formats, dont un "en rond" afin de pouvoir l'afficher correctement dans la liste des applications de l'appareil). 

3. Lancez votre application, quittez-la puis vérifiez que celle-ci possède bien une icône, quand vous naviguez dans l'appareil.

</div>

Maintenant que notre application est complète, il ne reste plus qu'à générer le fichier `.apk` (son exécutable). C'est ce fichier qui pourra être distribué et aussi mis à disposition sur le `Playstore`, par exemple.

<div class="exercise">

1. À l'aide du cours, exportez votre application en `.apk`.

2. Une fois exporté, trouvez ce fichier dans votre système.

</div>

## Pour aller plus loin

Pour développer l'application météo, nous avons utilisé une `API` publique et gratuite. Il existe plein d'autres `API` que vous pouvez utiliser pour créer des applications!

Par exemple :

* [https://pokeapi.co/](https://pokeapi.co/) : Une `API` très complète sur les Pokémons! Avec elle, vous pourriez construire une application **Pokédex**, par exemple.

* [https://genderize.io/](https://genderize.io/) : donne des informations sur un prénom (nombre de personnes portant ce nom, etc.)

* [https://uselessfacts.jsph.pl/](https://uselessfacts.jsph.pl/) : donne des faits inutiles, mais vrais.

* [https://animechan.vercel.app/](https://animechan.vercel.app/) : citations d'animés / de personnages d'animés.

Vous pouvez trouver une liste d'API WEB gratuites ici : [https://github.com/public-apis/public-apis](https://github.com/public-apis/public-apis)

<div class="exercise">

1. Sélectionnez une `API`, étudiez son fonctionnement, testez-la.

2. En vous inspirant de votre travail sur l'application **My Meteo**, réalisez une nouvelle application exploitant l'API que vous avez choisi.

</div>

## Conclusion

Lors de ce TD, vous avez découvert les bases du développement d'une application mobile sous Android et l'utilisation de service web (les `APIs`). Dans le prochain TD, nous allons continuer sur cette lancée en développant notre propre `API` pour le réseau social **The Feed** (le retour!).