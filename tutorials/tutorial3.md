---
title: TD3 &ndash; Développement d'une application mobile
subtitle: Android, API, Requêtes, Layout, Programmation évenementielle
layout: tutorial
lang: fr
---

## Introduction

Dans ce dernier TD, vous allez coder l'application mobile de **The Feed** en la reliant directement à l'API codée lors du TD précédent. Nous allons exploiter toutes les fonctionnalités offertes par notre API afin de construire une application assez complète.

Avant toute chose, si vous travaillez sur les machines de l'IUT, vous devez suivre à nouveau les étapes d'installation et de configuration d'Android Studio pour ces machines. Tout cela est expliqué au [début du TD1]({{site.baseurl}}/tutorials/tutorial1.html).

Concernant l'API :

* Si vous prévoyez d'utiliser un émulateur de smartphone (sur Android Studio), vous pouvez aussi bien héberger votre API sur le serveur `webinfo` que sur un serveur local, sur votre machine.

* Si vous prévoyez d'utiliser directement votre smartphone, il faut que votre serveur soit disponible publiquement en ligne, donc sur le serveur `webinfo` et pas en local.

## Bases de l'application

Pour commencer, nous allons poser les bases de l'application en créant l'interface principale qui servira à afficher les différentes publications. Nous mettrons également en place un menu de navigation simple. Pour l'instant, pas de connexion avec l'**API**, il s'agit simplement de tester le rendu de notre application.

### Interface principale

Commençons par créer le projet et l'activité principale. Nous viendrons compléter cette interface au fur et à mesure du développement de l'application. Pour gérer l'affichage des publications, nous allons utiliser un layout appelé `ScrollView` qui permettra de faire défiler les différentes publications. On aura aussi une zone permettant d'accueillir des boutons, pour recharger les publications, en publier, etc...

Le but du premier exercice est d'arriver à ce rendu : 

<p style="text-align:center">
    <img style="width:30%" src="{{site.baseurl}}/assets/TD3/rendu1.png">
</p>

<div class="exercise">

1. Créez un nouveau projet nommé `TheFeedApp`. Pour l'activité de base, sélectionnez `Empty Activity`. Pour le langage, sélectionnez `java` et la version 26 (ou plus) de l'API.

2. Dans le fichier de layout `activity_main.xml`, construisez l'interface en suivant ces instructions :

    * Supprimez le `TextView` placé là par défaut.

    * Remplacez le `ViewGroup` principal `androidx.constraintlayout.widget.ConstraintLayout` par un simple `LinearLayout` avec une orientation verticale et une couleur de fond `#e6ecf0` (attribut `android:background`).

    * Ajoutez une `ProgressBar`. Cet élément doit remplir tout son conteneur parent (réglez donc correctement `layout_width` et `layout_height`). Une fois cela fait, réglez sa visibilité à `gone` (nous nous en servirons lors des chargements). Vous donnerez aussi un **identifiant** à cet élément.

    * Ajoutez un `LinearLayout` à l'orientation verticale. Il doit aussi remplir tout le conteneur parent. Nous nous référons à ce `ViewGroup` comme étant la **zone de contenu**. Donnez-lui aussi un identifiant.

    * Dans la **zone de contenu**, ajoutez un autre `LinearLayout` à l'orientation horizontale. Il doit remplir toute la largeur du conteneur parent, mais pour la hauteur, il doit seulement prendre la place nécessaire en fonction de son contenu. Spécifiez également des marges de `10dp` (attribut `android:layout_margin`). Enfin, les éléments contenus dans ce `layout` doivent être **centrés**. Nous nous référons à ce `ViewGroup` comme étant la **zone des boutons**.

    * Dans la **zone des boutons**, ajoutez **deux boutons**. Le premier doit afficher le texte **Recharger** et le second **Publier** (nous nous occuperons de l'internationalisation plus tard). La largeur et la hauteur de ces deux boutons doivent correspondre à leur contenu. Donnez-leur aussi des **marges horizontales** (`android:layout_marginHorizontal`) de `10dp`. Donnez des identifiants à ces boutons.

    * Dans la **zone de contenu**, ajoutez un `ScrollView`. Il doit remplir toute la largeur de son conteneur parent et pour la hauteur, elle doit s'adapter selon le contenu. 
    
    * Enfin, dans le `ScrollView` que vous venez de créer, ajoutez un `LinearLayout` avec une orientation `verticale` qui remplit toute la largeur de con conteneur et dont la hauteur s'adapte selon le contenu. Nous nous référons à ce `ViewGroup` comme étant la **zone des publications**. C'est ici que nous ajouterons dynamiquement les différentes publications chargées depuis l'**API**.

3. Dans le dossier `res/drawable`, importez [cette image]({{site.baseurl}}/assets/TD3/anonyme.jpg) qui servira à représenter un utilisateur qui n'a pas d'image de profil.

4. Pour chaque **publication** ajoutée, nous utiliserons un layout **template** que nous remplirons avec des données et que nous ajouterons dans la zone des publications. Pour avoir une idée du rendu de cette interface et vérifier que tout fonctionne, ajoutez (temporairement) plusieurs fois le code suivant (représentant une publication) dans le `LinearLayout` de la **zone des publications**:

    ```xml
    <androidx.cardview.widget.CardView
        android:layout_marginVertical="10dp"
        android:layout_marginHorizontal="35dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <LinearLayout
            android:orientation="horizontal"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content">
            <ImageView
                android:src="@drawable/anonyme"
                android:padding="10dp"
                android:layout_width="60dp"
                android:layout_height="60dp"/>
            <LinearLayout
                android:orientation="vertical"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content">
                <TextView
                    android:text="Anonyme - 01 Janvier 2023"
                    android:paddingHorizontal="10dp"
                    android:paddingTop="5dp"
                    android:textSize="15sp"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"/>
                <TextView
                    android:text="Hello world!"
                    android:paddingHorizontal="10dp"
                    android:paddingTop="5dp"
                    android:textStyle="bold"
                    android:textSize="20sp"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"/>
            </LinearLayout>
        </LinearLayout>
    </androidx.cardview.widget.CardView>
    ```
5. Lancez votre application et vérifiez que tout fonctionne. Si vous avez ajouté assez de publications, vous devriez pouvoir **scroller**. Si vous avez des erreurs de compatibilité en lançant le projet, ouvre le **deuxième** fichier `build.gradle` situé dans `Gradle Scripts` et changez la ligne :

    ```groovy
    implementation 'androidx.appcompat:appcompat:1.6.1'
    ```

    En :

    ```groovy
    implementation 'androidx.appcompat:appcompat:1.5.1'
    ```

    Et appuyez sur le bouton `Sync now` dans le bandeau, juste en dessous de l'onglet. Relancez votre application.

6. Supprimez les publications que vous avez ajoutées temporairement.
</div>

Quelques commentaires sur le **template** des publications :

* Le ViewGroup `CardView` est un layout permettant d'organiser les éléments dans une **carte**, c'est-à-dire une boîte avec des coins, une ombre...parfait pour nos publications !

* Dans cette carte, on retrouve un **LinearLayout** horizontal contenant deux éléments :

    * L'image de profil de l'utilisateur

    * Un autre `LinearLayout` vertical contenant deux `TextView`. Le premier donne les informations de la publication (nom de l'utilisateur et date) et le second concerne le contenu de la publication.

### L'entité publication

Lorsque nous récupérerons nos publications depuis l'API, elles seront au format `JSON`. Il serait alors intéressant d'avoir du côté de l'application mobile un objet "miroir" de l'objet distant récupéré, afin de stocker ses informations et pour facilement le manipuler et l'ajouter à notre zone de publications. Nous verrons le processus de sérialisation et de désérialisation plus tard, pour l'instant, nous allons simplement créer l'objet. Nous n'inclurons pas tout de suite les **auteurs**.

<div class="exercice">

1. Créez un package nommé `entity` et à l'intérieur, créez une classe `Publication`.

2. Créez des attributs `id` (int), `message` (String) et `datePublication` (Date) ainsi que les **getters** et **setters** associés (Android Studio peut vous les générer automatiquement). 

3. Ajoutez aussi un constructeur vide. Pour créer un objet, nous en ferons une instance "vide" puis nous utiliserons les `setters` (car nous ferons pareil lors de la **sérialisation**, plus tard).

</div>

### Présentation des publications

Maintenant que nous avons une classe `Publication`, nous allons pouvoir l'utiliser dans la génération de notre interface. Mais tout d'abord, nous allons voir comment créer un **template** et l'utiliser dynamiquement.

Dans le dossier `layout`, il est possible de créer des fichiers `xml` de `template` dont le but n'est pas d'être affiché tel quel comme les layouts mais plutôt d'être injecté et utilisé dans un autre layout (souvent de manière dynamique). Une **Activity** peut générer une vue à parti d'un template, lui injecter de l'information puis l'ajouter dans son layout. Les éléments d'un template peuvent posséder des **identifiants** et il est alors facile d'y accèder avec la méthode `findViewById` utilisée directement sur le template.

Par exemple, imaginons le layout `activity_example.xml` suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:id="@+id/content"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    </LinearLayout>

</LinearLayout>
```

Et un template `template_example.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/carte"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">
    <TextView
        android:id="@+id/texte"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>
</androidx.cardview.widget.CardView>
```

Il est alors possible d'utiliser ce template (plusieurs fois) dans `ExampleActivity.java` et l'injecter dans le layout principal :

```java
public class ExampleActivity extends AppCompatActivity {

    private LinearLayout content;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_example);
        this.content = findViewById(R.id.content);

        //Ajout du template trois fois
        this.ajouterTemplate();
        this.ajouterTemplate();
        this.ajouterTemplate();
    }

    public void ajouterTemplate() {
        //Quand on récupère une vue, il faut caster
        //L'élément qu'on récupère doit être du type du ViewGroup principal du template
        // On doit préciser un ViewGroup "parent" auquel le template sera relié. Ici, il s'agit de this.content.
        CardView template = (CardView) getLayoutInflater().inflate(R.layout.template_example, this.content, false);
        //On va chercher le TextView contenu à l'intérieur du template! On utilise donc findViewById sur la vairable "template" et non pas "this".
        TextView texteExample = (TextView) template.findViewById(R.id.texte);
        texExample.setText("Coucou!");
        //Ajout du template modifié dans le LinearLayout possédant l'id "content" dans le layout pricnipal de l'activité.
        this.content.addView(template);
    }
```

Dans notre cas, le template que nous allons créer et utiliser pour les publications est celui présenté plus tôt dans le TD :

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView
xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_marginVertical="10dp"
android:layout_marginHorizontal="35dp"
android:layout_width="match_parent"
android:layout_height="wrap_content">
<LinearLayout
    android:orientation="horizontal"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">
    <ImageView
        android:src="@drawable/anonyme"
        android:padding="10dp"
        android:layout_width="60dp"
        android:layout_height="60dp"/>
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
        <TextView
            android:paddingHorizontal="10dp"
            android:paddingTop="5dp"
            android:textSize="15sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
        <TextView
            android:paddingHorizontal="10dp"
            android:paddingTop="5dp"
            android:textStyle="bold"
            android:textSize="20sp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </LinearLayout>
</LinearLayout>
</androidx.cardview.widget.CardView>
```

<div class="exercise">

1. Dans le dossier `res/layout`, créez un fichier `template_publication.xml` et importez le code du template ci-dessus.

2. Placez divers **id** sur les éléments qu'il est intéressant de récupérer : l'image de profil, les informations de la publication (le premier `TextView`) et son contenu (le deuxième `TextView`).

3. Dans votre classe `MainActivity.java`, faites en sorte de récupérer dans des attributs tous les éléments sur lesquels vous avez placé des `id` dans le layout `activity_main.xml`.

4. Créez une méthode `ajouterPublication` prenant un objet `publication` de type `Publication` en paramètre. Le but de cette méthode est de :

    * Charger la `CardView` du template `template_publication.xml`.

    * Changer le texte du `TextView` portant les **informations** de la publication par : **Anonyme - Date de la publication** en utilisant la date de l'objet `publication`. Pour afficher la date de manière textuelle au bon format, vous pouvez utiliser le code suivant :

    ```java
    SimpleDateFormat formater = new SimpleDateFormat("dd MMMM yyyy", Locale.getDefault());
    String dateText = formater.format(monObjetDate);
    ```
    * Changer le texte du `TextView` portant le **contenu** de la publication avec le message de l'objet `publication`.

    * Ajouter la `CarView` du template ainsi modifiée au `LinearLayout` de la **zone des publications**

    Cette méthode ne retourne rien.

5. Créez une méthode `chargerPublications` qui ne prend aucun paramètre et ne retourne rien. Cette méthode doit :

    * Vider le contenu de la **zone de publications**. Pour cela, on peut appeler la méthode `removeAllViewsInLayout` sur le `LinearLayout` concernée.

    * Instancier plusieurs objets de type `Publication`, remplir les paramètres (message, contenu, date...) avec les setters et appeler la méthode `ajouterPublication` pour chaque publication. Pour la date une simple instanciation de l'objet suffit : `new Date()`.
    
    Pour le moment, il s'agit d'une méthode bouchon. Plus tard, nous la remplacerons avec un appel à l'API.

6. Réécrivez la méthode `onResume` (si vous commencez à écrire le nom, Android Studio vous permettra de générer le code de base de la méthode) et ajoutez un appel à `chargerPublications`. La méthode `onResume` est appelée à chaque retour à l'interface (réouverture de l'application, retour en arrière, fermeture d'un autre écran...) ce qui permettra de mettre à jour la liste des publications sans que l'utilisateur ait à recharger.

7. Lancez votre application. Normalement, les publications que vous avez instanciées s'affichent bien !

8. Nous allons maintenant un peu améliorer le rendu des avatars (anonyme) en affichant une image arrondie, grâce à une librairie. Pour cela, rendez-vous dans le deuxième fichier `build.gradle` dans `Gradle Scripts` et ajoutez la dépendance suivante :

    ```groovy
    dependencies {
        ...
        implementation 'de.hdodenhof:circleimageview:3.1.0'
    }
    ```

    Appuyez sur le bouton de synchronisation.

9. Dans `template_publicatio.xml`, remplacez l'élément `ImageView` par `de.hdodenhof.circleimageview.CircleImageView`. Il s'agit d'une extension de `ImageView` affichant les images en arrondies.

10. Relancez votre application et constatez le changement.

</div>

### Menu

Afin de naviguer au travers des différentes interfaces d'une application, plusieurs systèmes sont envisageables. Dans notre cas, nous allons mettre en place un **menu** simple qui sera disponible dans toutes les activités de l'application.

Pour définir les différents éléments d'un menu, il suffit simplement de créer un fichier `xml` dans le dossier `res/menu`. Par exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/id1" android:title="Bouton 1" />
    <item android:id="@+id/id2" android:title="Bouton 2" />
    <item android:id="@+id/id3" android:title="Bouton 3" />
</menu>
```

Chaque `item` doit posséder un identifiant pour qu'on puisse facilement identifier quel bouton est sélectionné par la suite.

Ensuite, pour affecter un menu à une interface, il suffit de réécrire la méthode `onCreateOptionsMenu(Menu menu)` au niveau de la classe de l'activité, ainsi :

```java
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.menu_feed_connected, menu);
        return true;
    }
```

Ensuite, pour exécuter du code selon le bouton sélectionné, on récrit la méthode `onOptionsItemSelected(@NonNull MenuItem item)` en vérifiant l'identifiant du bouton sélectionné :

```java
    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        if (item.getItemId() == R.id.id1) {
            //Bouton 1 sélectionné
        }
        else if (item.getItemId() == R.id.id2) {
            //Bouton 2 sélectionné
        }
        else if (item.getItemId() == R.id.id3) {
            //Bouton 3 sélectionné
        }
        return super.onOptionsItemSelected(item);
    }
```

Dans notre cas, nous allons définir deux menus : un menu pour les visiteurs (non connecté) et un menu pour les utilisateurs connectés. Plus tard, nous implémenterons la logique liée à la sélection de chaque bouton. Au début, nous n'afficherons que le menu pour les visiteurs puis nous verrons comment changer de menu quand l'utilisateur est connecté.

<div class="exercise">

1. Faites un clic droit sur le dossier `res` puis sélectionnez `New` -> `Android Ressource Directory`. Nommez le dossier `menu` et sélectionnez aussi `menu` pour **Ressource type**.

2. Dans `res/menu`, créez un menu `menu_visiteur.xml` comprenant trois boutons : **Feed**, **Connexion** et **Inscription**. N'oubliez pas les identifiants.

3. Dans `res/menu`, créez un menu `menu_utilisateur.xml` comprenant trois boutons : **Feed**, **Ma page** et **Déconnexion**. N'oubliez pas les identifiants. Le bouton **Feed** doit (et peut) posséder le même identifiant que dans `menu_visiteur.xml`.

4. Afin que toutes les activités aient accès au même menu, nous allons définir le comportement lié au menu dans une **classe abstraite** dont toutes nos activités hériteront. Créez donc une classe **abstraite** `AbstractFeedActivity` étendant la classe `AppCompatActivity`.

5. Dans cette nouvelle classe, réécrivez la méthode `onOptionsItemSelected` afin de créer un `if` par bouton disponible dans les deux menus que vous venez de créer (donc en tout, **5 boutons à gérer**). Pour l'instant, nous allons laisser le corps de chaque `if` vide, nous les remplirons au fur et à mesure.

6. Toujours dans cette classe, réécrivez la méthode `onCreateOptionsMenu` en sélectionnant le menu des visiteurs.

7. Faites en sorte que votre classe `MainActivity` étende la classe `AbstractFeedActivity` au lieu de `AppCompatActivity`. Ainsi, elle hérite du comportement lié aux menus. Dans le futur, toutes nos classes `Activity` hériteront de `AbstractFeedActivity`.

8. Lancez votre application. Vous devriez observer trois petits points en haut à droite. Il s'agit du menu. Vous pouvez appuyer dessus pour afficher les options.
</div>

## Utilisation de l'API

Maintenant que nous avons posé les bases de l'application, il est temps d'utiliser notre `API` pour charger des données concrètes !

### Sérialisation et désérialisation

Avant toute chose, quand nous allons recevoir des données depuis l'API, cela sera sous le format `JSONObject` (ou bien `JSONArray` pour les collections). Il faut donc définir des méthodes pour transformer un objet `JSON` en `Publication` et inversement. Ces processus sont appelés `desérialisation` et `sérialisation`.

Pour rappel, pour **lire** des données un `JSONObject`, on procède ainsi :

```java
try {
    int num = json.getInt("num");
    String str = json.getString("str");
    JSONArray array = json.getJSONArray("subArray");
    JSonObject subJson = json.getJSONArray("subJson");
    ...
} catch (JSONException  e) {
    e.printStackTrace();
}
```

Pour chaque type de données, il y a une méthode `get` associée. On utilise comme paramètre la **clé** qui correspond au nom de l'attribut à extraire dans le document json. Il ne faut pas oublier de gérer l'exception.

Pour lire dans une liste d'objets json (`JSONArray`) on procède avec une boucle :

```java
int i = 0;
try {
    while (i < array.length()) {
        JSONObject json = array.getJSONObject(i);
        ...
        i++;
    }
} catch (JSONException e) {
    e.printStackTrace();
}
```

Enfin, pour **construire** un `JSONObject`, on utilise la méthode `put` :

```java
JSONObject json = new JSONObject();
try {
    json.put("attr1", objet1);
    json.put("attr2", objet2);
    ...
 } catch (JSONException e) {
    e.printStackTrace();
}
```

Tout cela va nous permettre de compléter notre classe `Publication` afin de lui rajouter les outils pour réaliser sa **sérialisation** et sa **désérialisation**.

<div class="exercise">

1. Dans la classe `Publication`, ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Permet de transormer l'objet en payload JSON.
     */
    public JSONObject serialize() {
        JSONObject json = new JSONObject();
        try {
            //A compléter
        } catch (JSONException e) {
            e.printStackTrace();
        }
        return json;
    }
    ```

    Attention, tous les attributs ne sont pas à sérialiser ! Souvenez-vous, dans le cadre d'une publication, la seule chose envoyée par le client est le **message**.

2. Ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Affecte les attributs de la publication à partir des données contenues dans "json"
     */
    public void unserialize(JSONObject json) {
        try {
            //A compléter
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
    ```

    Comme indiquer, cette méthode va lire les données contenue dans l'objet `json` pour les affecter aux différents attributs de notre `Publication`. Le nom des attributs à récupérer correspond aux noms dans le document json qui vous est retourné quand vous créez une publication (vous pouvez vérifier sur `Postman`). Pour le moment, on souhaite seulement récupérer : l'id du message, sa date et son contenu.

    Attention, dans le **json**, la date est une chaîne de caractères ! (**String**). Il faut la convertir ainsi :

    ```java
    SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
    Date date = formatter.parse(dateEnString);
    ```

    Il faudra alors rajouter la gestion d'une exception `ParseException` au niveau du bloc `try/catch`.

3. Ajoutez ce nouveau **constructeur** (déjà complet):

    ```java
    public Publication(JSONObject jsonObject) {
        this.unserialize(jsonObject);
    }
    ```

    Le but est de créer simplement une nouvelle publication à partir d'un `JSONObject` en désérialisant son contenu.

4. Enfin, ajoutez et complétez la **méthode de classe** suivante :

    ```java
    /**
     * Construit et retourne une liste de publications à partir du JSONArray passé en paramètre
     * Chaque entrée du JSONArray contient un objet json qui peut être désérialisé en publication
     * Soyez malin et utilisez votre nouveau constructeur!
     */
    public static List<Publication> unserializePublications(JSONArray array) {
        List<Publication> publications = new ArrayList<>();
        try {
            //A compléter
        } catch (JSONException e) {
            e.printStackTrace();
        }
        return publications;
    }
    ```

    Cette méthode de classe (**static**) servira notamment lors de l'appel à l'API quand nous récupèrerons l'ensemble des publications sous la forme d'un `JSONArray`. Il sera alors possible de convertir tout cela en une liste d'objets `Publication` exploitables par notre interface.

</div>

### Appels à l'API

Comme nous l'avions fait dans le **TD1**, nous allons mettre en place une classe spéciale dont le rôle sera de faire des appels à notre `API`, grâce à la librairie **Android Async HTTP**. Si vous ne vous rappelez plus bien du fonctionnement de cette librairie, n'hésitez pas à aller consulter la partie concernée dans le **TD1** (au niveau de l'application météo).

Pour simplifier, au lieu d'avoir un attribut `AsyncHttpClient` client (pour effectuer les requêtes) nous allons plutôt en créer un à chaque requête (ce qui sera pratique quand nous devrons gérer l'authentification). Ce n'est pas optimal, mais vous pourrez améliorer cet aspect de l'application plus tard.

Pour les publications, le squelette de base de notre classe sera donc le suivant :

```java
public class PublicationAPI {

    private final static String baseUrl = //TO-DO;

    public static void loadAllPublications(Consumer<List<Publication>> callback) {
        AsyncHttpClient client = new AsyncHttpClient();
        //TO-DO
    }

}
```

Ici, `baseUrl` correspond à l'URL pointant sur la ressource "publications" dans votre `API`. (par exemple [https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/publications](https://webinfo.iutmontp.univ-montp2.fr/~votre_login/chemin_dossier_projet/public/api/publications)).

Attention, si votre serveur (où est hébergé votre API) est en local, il ne faut pas utiliser l'adresse **http://localhost/chemin** (ou http://127.0.0.1/chemin) qui pointe vers autre chose, sur votre émulateur, mais plutôt l'adresse `http://10.0.2.2/chemin` qui fera le pont vers le serveur local de votre machine. Si ovus utilisez un autre port que 80 (port HTTP de base), il faudra le préciser : `http://10.0.2.2:port/chemin`

Cette information sera utilisée dans les requêtes, afin d'atteindre la route de souhaitée sur l'`API`.

Le `callback` est un **Consumer** de liste de publications. C'est-à-dire que quand on fera un appel à la méthode `loadAllPublications`, il faudra fournir une fonction prenant en paramètre une liste de publications qui sera appelée une fois les publications récupérées depuis l'API et converties en liste d'objets `Publication` par notre application.

<div class="exercise">

1. Importez la librairie en l'ajoutant comme dépendances dans le fichier `build.gradle`:

    ```groovy
    dependencies {
        ...
        implementation 'com.loopj.android:android-async-http:1.4.9'
    }
    ```

    N'oubliez pas de synchroniser.

2. Il faut autoriser l'application à utiliser **internet**. Pour cela, rendez-vous dans `manifests/AndroidManifest.xml` et ajoutez la ligne suivante dans la zone définie par les balises `manifest` :

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    ```

3. Créez un package `api` et créez la classe `PublicationAPI` en important le squelette de classe ci-dessus.

4. Affectez `baseUrl` selon votre situation et l'adresse de votre `API`.

5. Complétez la méthode `loadAllPublications` afin que celle-ci envoie une requête pour récupérer toutes les publications, traite la réponse en convertissant le tableau **JSON** des réponses en liste de **publications** puis appelle le **callback** avec ce résultat. Quelques petits commentaires :

    * Encore une fois, si tout cela est flou pour vous, toutes les réponses sont dans le TD1 dans la partie correspondante dans l'exercice lié à l'application Météo et la connexion à l'API.

    * Dans le `JsonHttpResponseHandler`, il faut implémenter la méthode suivante :

    ```java
    @Override
    public void onSuccess(int statusCode, Header[] headers, JSONObject response) {
        ...
    }
    ```

    En effet, la réponse renvoyée n'est pas directement un `JSONArray` mais un `JSONObject` concernant diverses informations dont un `JSONArray` contenant les publications.

    * Par conséquent, trouvez le nom de l'attribut permettant d'extraire le `JSonArray` correspondant (en analysant la réponse obtenue avec `Postman`).

    * Pour convertir le `JSONArray` en **publications**, servez-vous d'une méthode que nous avons définie plus tôt !

</div>

### Chargement des publications

Maintenant que nous pouvons récupérer nos publications depuis l'API, il ne nous reste plus qu'à les charger dans notre interface principale !

<div class="exercise">

1. Dans votre classe `MainActivity`, créez et complétez la méthode suivante :

    ```java
    /**
     * Ajoute toutes les publications de la liste dans l'interface.
     */
    protected void finChargementPublications(List<Publication> publications) {
        //TO-DO
    }
    ```

    Cette méthode sera le `callback` que nous fourniront lors de l'appel à l'API. Elle sera donc appelée une fois que toutes les publications seront chargées. Le but est de parcourir la liste et d'utiliser la méthode que vous aviez défini plutôt pour ajouter une publication dans l'interface à partir du template, etc...

2. Modifiez la méthode `chargerPublications` en supprimant l'ajout des "fausses" publications. À la place, faites un appel vers l'API et précisez la méthode `finChargementPublications` comme `callback`. Déplacez aussi l'appel vidant les anciennes publications (`removeAllViewsInLayout`) au début de `finChargementPublications`.

3. Assurez-vous que votre `API` est accessible et fonctionnelle. Assurez-vous aussi d'avoir quelques publications enregistrées sur votre API.

4. Lancez votre application mobile. Après quelques secondes, vous devriez voir vos (vrais) publications apparaitre sur l'interface !

5. Afin d'améliorer ce rendu, créez deux méthodes :

    * Une méthode `debutChargement` qui cache la zone de (visibilité `View.GONE`) de la **zone de contenu** et rend visible (visibilité `View.VISIBLE`) la `ProgressBar`.

    * Une méthode `finChargement` qui réalise fait inverse.

    Servez-vous ensuite de la méthode `debutChargement` dans `chargerPublications` et `finChargement` orsque les publications ont fini d'être ajoutées dans `finChargementPublications`.

6. Faites en sorte que l'appui sur le bouton **recharger** relance le chargement des publications. Pour rappel, lors de l'ajout d'un **event listener**, on peut procéder ainsi pour éviter d'ajouter un paramètre `View` à la méthode appelée (si on ne compte pas utiliser cet objet `View`) :

    ```java
    button.setOnClickListener(view -> fonction());
    ```

7. Relancez votre application et vérifiez que vos nouveaux ajouts fonctionnent.
</div>

### L'entité utilisateur

De la même manière que nous avons créé une entité `Publication` au niveau de notre application mobile, nous allons créer une entité `Utilisateur`. 

Vous allez ajouter les méthodes pour sérialiser/désérialiser l'utilisateur, mais pas pour désérialiser une liste d'utilisateurs. En effet, nous ne serons pas amenés (du moins pas pour le moment) à lire la liste des utilisateurs, mais c'est une fonctionnalité que vous pourrez rajouter dans le futur !

Les attributs à stocker dans cette classe sont : l'id, le login, l'adresse email et le **mot de passe en clair** (plainPassword). En effet, quand nous créons un utilisateur, nous devons préciser son mot de passe dans le payload ! Néanmoins, cette partie ne sera jamais désérialisée, car absente des documents **JSON** retournés par le serveur (de toute façon, la base de données ne stocke pas le mot de passe en clair).

<div class="exercise">

1. Dans le package `src`, créez une classe `Utilisateur`. Définissez ses attributs, ses getters et ses setters.

2. Ajoutez une méthode `serialize` dont le but est de construire et renvoyer l'objet JSON correspondant au `payload` pour créer un utilisateur (donc, login, mot de passe en clair, adresse email...).

3. Ajoutez une méthode `unserialize` qui permet d'affecter les attributs de l'instance à partir d'un objet **JSON** passé en paramètre. Pour connaître les noms des attributs à récupérer, n'hésitez pas à vous servir de `Postman`.

4. Ajoutez un constructeur vide et un constructeur prenant en paramètre un `JSONObject` qui désérialise son contenu dans l'instance (comme pour les publications).

</div>

Il est important de noter qu'en l'état, votre méthode `serialize` ne fonctionne que pour `POST` et pas vraiment pour `PATCH`, car il n'est pas possible de choisir quels éléments sont sérialisés. C'est quelque chose que vous pourrez améliorer à la fin du TD, si vous souhaitez étendre les fonctionnalités de l'application.

### Ajout des données des auteurs

Maintenant que nous avons un objet `Utilisateur`, nous allons pouvoir ajouter les données des auteurs dans les publications.

<div class="exercise">

1. Dans la classe `Publication`, ajoutez un attribut `auteur` de type `Utilisateur` ainsi que le getter et le setter associés.

2. Toujours dans la classe `Publication`, modifiez le code la méthode `unserialize` pour affecter l'auteur. Normalement vous devriez être en mesure de faire cela très facilement, en une ligne de code.

3. Au niveau de la classe `MainActivity`, modifiez le code de l'ajout d'une publication pour afficher le `login` de l'utilisateur à la place de `Anonyme` dans le `TextView` contenant les informations de la publication.

4. Lancez votre application et vérifiez que le login de l'auteur apparait effectivement sur chaque publication.

</div>

Maintenant, nous aimerions afficher l'image de profil de chaque utilisateur, à côté de ses publications. Néanmoins, comme vous l'avez remarqué, nous ne stockons par d'image de profil du côté de l'API. Cela aurait pu se faire, mais nous ne l'avons pas fait par manque de temps, car c'est un peu complexe.

À la place, nous allons utiliser un service appelé [Gravatar](https://fr.gravatar.com/). Ce service permet (en vous inscrivant) d'associer votre adresse mail à une photo de profil. Ainsi, à partir de votre adresse, les différents sites web et applications peuvent charger votre image de profil depuis ce service sans avoir besoin de stocker cette image de leur côté et sans demander à l'utilisateur d'uploader cette image. Il suffit de faire une simple requête à **Gravatar**. Certains sites comme **Bitbucket** utilisent ce mécanisme.

Du côté **Android** il est très facile d'aller charger une image depuis **Gravatar** et de la charger. Ainsi, grâce à l'adresse mail de nos utilisateurs, nous pourront charger leur image de profil. Si l'utilisateur n'est pas inscrit sur **Gravatar**, nous chargerons simplement l'image "anonyme" que nous utilisions jusqu'ici.

Pour obtenir l'image de profil **Gravatar** d'un utilisateur, on prend son adresse mail, on la **hache** avec l'algorithme **MD5** et on fait appel à l'URL : [https://www.gravatar.com/avatar/hash](https://www.gravatar.com/avatar/hash)

Par exemple, si on prend l'email d'exemple suivant : `gravatar.exemple.iut.mtp@gmail.com`.

On la hache avec `MD5` (par exemple avec [ce site](https://www.md5hashgenerator.com/)) : `ed2e85b77eea9cd92a5348c421ff812b`.

Et on obtient l'adresse de l'image de profil via ce lien : [https://www.gravatar.com/avatar/ed2e85b77eea9cd92a5348c421ff812b](https://www.gravatar.com/avatar/ed2e85b77eea9cd92a5348c421ff812b)

Ensuite, sur `Android`, on peut utiliser deux librairies :

* `commons-codec` pour encoder facilement une chaîne de caractères avec **MD5**.

* `picasso` pour charger une image depuis un lien et l'injecter dans une `ImageView` (ou plutôt notre `CircleImageView`).

Voici un exemple d'utilisation :

```java
//Conversion de l'adresse email en MD5
String hash = new String(Hex.encodeHex(DigestUtils.md5(adresseEmail)));
String gravatarUrl = "http://www.gravatar.com/avatar/" + hash + "?s=204&d=404";
Picasso picasso = new Picasso.Builder(this).build();
picasso.load(gravatarUrl).placeholder(R.drawable.imageDefaut).into(imageView);
```

Le **query string** permet d'indiquer à Gravatar ce qu'il faut retourner comme code en cas de succès (`204`) ou si l'utilisateur n'existe pas (`404`). Cela permettra à `picasso` de charger une image par défaut (pointée par `R.drawable.imageDefaut`) si l'utilisateur n'existe pas.

<div class="exercise">

1. Importez les deux librairies nécessaires dans les dépendances du `build.gradle` :

    ```groovy
    implementation 'com.squareup.picasso:picasso:2.8'
    implementation 'commons-codec:commons-codec:1.15'
    ```

2. Faites les modifications nécessaires dans la méthode `ajouterPublication` de `MainActivity` afin de charger l'image de profil **Gravatar** de l'utilisateur dans le `CircleImageView` de la publication, à partir de son adresse mail. L'image par défaut à préciser est l'image **anonyme** que nous utilisons depuis le début.

3. Sur votre `API` (avec `Postman`) créez un utilisateur avec une adresse mail qui est liée à un compte et une image de profil **Gravatar**. Vous pouvez soit directement vous inscrire sur **Gravatar** et utiliser votre propre image soit utiliser l'adresse `gravatar.exemple.iut.mtp@gmail.com`. Toujours via l'API (ou directement dans votre base), ajoutez quelques publications à cet utilisateur.

4. Lancez votre application et vérifiez que l'image de profil s'affiche bien sur chaque publication de l'utilisateur ! Les autres utilisateurs (qui n'ont pas de profil **Gravatar**) doivent avoir une image **anonyme**, comme précédemment.

</div>

## Authentification

Maintenant que nous savons charger et afficher des publications depuis notre `API`, nous aimerions implémenter les autres fonctionnalités sur l'application mobile. Mais la plupart de ces fonctionnalités demandent d'être authentifié et donc, que l'application récupère et stocke un `JWT` et l'envoi à chaque requête nécessitant une authentification.

Nous allons donc mettre en place un système d'authentification.

### Stockage du JWT

Pour nous aider à gérer et lire dans le `JWT`, nous allons utiliser une librairie dédiée. Ensuite, il faudra **stocker** physiquement ce jeton dans les données de l'application afin qu'il puisse y ré-accéder en cas de fermeture et de réouverture de l'application.

Afin de stocker des données, il est possible d'utiliser la classe `SharedPreferences`. Elle fournit toutes les méthodes nécessaires pour accéder à une catégorie nommée et y stocker des données en mode clé-valeur, à la manière d'une **map**.

Pour pouvoir manipuler cette classe, nous avons besoin d'un objet de type **context**. En Android, un **context** fait référence à une classe qui permet d'accéder aux ressources de l'application (et divers autres services). Par exemple, toutes nos activités sont des **contextes** valides. Néanmoins, il serait fastidieux de passer l'instance d'une activité chaque fois qu'on a besoin d'utiliser un **context**. À la place, nous pouvons définir une classe principale (dans le package principal, avec les autres activités) pour notre application qui permet d'accéder au **context** global de l'application de manière **statique** :

```java
import android.app.Application;
import android.content.Context;

public class MyApp extends Application {

    private static MyApp application;

    public void onCreate() {
        super.onCreate();
        application = this;
    }

    public static Context getAppContext() {
        return application.getApplicationContext();
    }

}
```

Il faut ensuite modifier le fichier `manifests/AndroidManifest.xml` en ajoutant le paramètre `android:name` paramétré avec le nom de notre classe, dans l'élément `<application>` :

```xml
<application
    android:name=".MyApp"
    ...
```

Par la suite, on peut alors facilement accéder au **context** ainsi :

```java
Context context = MyApp.getAppContext();
```

Concernant la classe `JWT` que nous allons utiliser, pour l'instant, deux éléments nous intéressent :

```java
/*
Constructeur
On lui passe le string représentant le jwt codé. 
Le constructeur va le décoder et on pourra accèder à ses informations grâces aux méthodes.
*/
JWT jwt = new JWT(stringJWT);

//Renvoie le token codé. C'est celui-ci qu'on utilisera lors des requêtes sécurisées.
String tokenCode = jwt.toString();
```

Concernant `SharedPrefence`, on l'utilise ainsi pour écrire des données :

```java
//Ecriture

//Si la catgeorie n'existe pas, elle sera créée.
//Context.MODE_PRIVATE permet de protéger les données, pour qu'elle ne soient lisibles que par l'application
SharedPreferences prefs = MyApp.getAppContext().getSharedPreferences("categorie", Context.MODE_PRIVATE);

//On récupère l'éditeur
SharedPreferences.Editor editor = prefs.edit();

//Foncitonne comme une map
editor.putString("cle", str);

//On applique les changements
editor.apply();

//Si on souhaite supprimer une donnée :
editor.remove("cle");
editor.apply();
```

Et pour en lire :

```java
//Lecture

SharedPreferences prefs = MyApp.getAppContext().getSharedPreferences("categorie", Context.MODE_PRIVATE);
if(prefs.contains("cle")) {
    //Le deuxième paramètre est une valeur par défaut si la clé n'est pas présente.
    String str = prefs.getString("cle", null);
    ...
}
```

Votre objectif est de créer une classe permettant de gérer le token d'authentification. Elle possédera le squelette suivant :

```java
public class TokenManager {

    private static JWT token;

    public static void storeToken(JWT jwt) {
        TokenManager.token = jwt;
        //TO-DO
    }

    public static JWT getToken() {
        if(TokenManager.token == null) {
           //TO-DO
        }

        return TokenManager.token;
    }

    public static void unsetToken() {
        TokenManager.token = null;
        //TO-DO
    }

}
```

Comme vous l'aurez constaté, nous possédons un attribut de classe `token`. En fait, pour éviter de re-charger tout le temps le token depuis les données de l'application, nous utilisons cet attribut intermédiaire. Si sa valeur est nulle, on ira lire le token dans les données.

<div class="exercise">

1. Importez la librairie permettant de gérer les `JWT` dans les dépendances du `build.gradle` :

    ```groovy
    implementation 'com.auth0.android:jwtdecode:2.0.2'
    ```

2. En vous inspirant du code de `MyApp`, créez une classe `TheFeedApp` qui permet d'accéder au `context` de l'application de manière statique. Faites aussi l'ajout nécessaire dans `AndroidManifest.xml`.

3. Créez un package `utils` et à l'intérieur, créez la classe `TokenManager` en important le squelette de code donné ci-dessus.

4. Complétez la méthode `storeToken`. Cette méthode stocke déjà le token dans notre attribut de classe. Votr objectif est donc d'aller également stocker le **string** représentant le token codé dans les données de l'application en utilisant `SharedPreferences`. Vous pouvez utiliser le nom de catégorie de votre choix (il faudra bien entendu utiliser le même lors de la lecture.)

5. Complétez la méthode `getToken`. Si le token (attribut de classe) vaut **null**, il faut donc aller chercher le **string** correspondant dans les données de l'application, construire un `JWT` avec et l'affecter à `token`.

6. Complétez la méthode `unsetToken`. Son but est de supprimer le token, à la fois dans l'attribut de classe (c'est déjà fait) mais aussi das les données de l'application.
</div>

Nous avons maintenant de quoi stocker un token. Pour chaque requête nécessitant d'être authentifié, on utilisera cette classe ajouter le `JWT` aux **headers**. Et on pourra aussi le stocker après une connexion réussie. Dans l'absolu, il serait mieux que cette classe doit un **singleton**. Vous pourrez améliorer cet aspect à la fin du TD, s'il vous reste du temps.

### Classe utilitaire pour les données de l'utilisateur

Le `JWT` émis par notre `API` possède des données intéressantes :

* Sa date d'expiration

* L'identifiant de l'utilisateur

* Son login

Nous pouvons donc nous servir de tout cela pour construire une classe utile pour lire ces informations, pour savoir si l'utilisateur a besoin de se reconnecter, etc... Par exemple, on pourra facilement vérifier si l'utilisateur connecté est propriétaire d'une publication et donc s'il peut le supprimer (avant d'envoyer une requête "pour rien").

Au niveau de l'objet `JWT`, il est possible d'utiliser les méthodes suivantes :

```java
//Renvoie la date d'expiration
Date expiration = jwt.getExpiresAt();

//Il est donc possible de savoir si un token n'a pas expiré en comparant à la date d'aujourd'hui :
Date now = new Date();
boolean estValide = now.before(expiration);

//Pour récupérer d'autres informations, on récupère un "Claim" :
Claim claim = jwt.getClaim("cle");
//On doit convertir ce claim dans le type souhaite :
int valeur = claim.asInt(); //Si c'est un entier
String str = claim.str(); //Si c'est une chaîne de caractères
//Ect...
```

Dans l'exercice suivant, vous penserez bien à utiliser la classe `TokenManager` que nous avons construit juste avant.

<div class="exercise">

1. Dans le package `utils`, créez une classe `UtilisateurUtils`.

2. Ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Renvoie "true" si l'utilisateur est connecté, c'est-à-dire qu'il possède un 
     * token et que celui-ci est toujours valide (n'a pas expiré).
     */
    public static boolean estConnecte() {
        
    }
    ```

3. Ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Retourne l'identifiant de l'utilisateur, contenu dans le token.
     * Si le token n'existe pas ou n'est pas valide, on renvoie -1.
     */
    public static int getUtilisateurId() {
        
    }
    ```

    N'hésitez pas à décoder un de vos `JWT` pour voir comment est appelé cet attribut dans le `payload` du token. 

4. Ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Retourne le login de l'utilisateur, contenu dans le token.
     * Si le token n'existe pas ou n'est pas valide, on renvoie null.
     */
    public static String getUtilisateurLogin() {
        
    }
    ```

    Attention, dans le `payload` du token, l'attribut stockant le login est probablement appelé `username`.
</div>

### Connexion et déconnexion

Maintenant, nous avons tout ce qu'il faut pour **connecter** et **déconnecter** l'utilisateur. Tout d'abord, il faut créer la classe permettant de faire un appel vers la route d'authentification sur l'API.

Il s'agit de la première requête `POST` que vous allez coder dans l'application. Contrairement aux requêtes, `GET`, il faut fournir un `payload` JSON et aussi utiliser le `context` de l'application (accessible via votre classe `TheFeed`). Il faut ensuite convertir le `payload` en `StringEntity` qu'on passera à la requête.

```java
JSONObject payload = new JSONObject();
try {
    payload.put("donnee", donee)
    ...
    StringEntity payloadEntity = new StringEntity(payload.toString());
    client.post(TheFeedApp.getAppContext(), url, payloadEntity, "application/json", new JsonHttpResponseHandler() {
        ...
    });
} catch(JSONExcpetion | UnsupportedEncodingException e) {
    e.printStackTrace();
}
```

<div class="exercise">

1. Dans le package `api`, créez une classe `AuthentificationAPI`. Comme pour `PublicationAPI`, ajoutez un **attribut statique** nommé `baseURL` pointant vers l'URL d'authentification (finissant par `/api/auth`).

2. Ajoutez et complétez la méthode suivante :

    ```java
    public static void connexion(String login, String password, Consumer<JWT> callback) {
        JSONObject payload = new JSONObject();
        try {
            //TO-DO
            //Contruction du payload d'authentification
            //Envoi de la requête POST vers la route d'authentification et appel du callback
        } catch(JSONException | UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        //TO-DO
    }
    ```

    * Le **Consumer** précisé est une fonction qui attend un `JWT` en paramètre. Lors du retour de l'API, il faudra donc construire le `JWT` à partir de la chaîne contenu dans le `JSONObject` et exécuter le **callback** avec. Il faudra là-aussi placer un `try/catch`, car vous manipulez un `JSONObject`.

    * L'API renvoie un simple `JSONObject`. Implémentez donc la bonne méthode `onSuccess` dans l'handler de votre requête.

</div>

Il faut ensuite construire l'interface permettant de se connecter. Il s'agit donc d'une nouvelle **activity**!

<div class="exercise">

1. Créez une nouvelle activité `ConnexionActivity`. Pour rappel, il faut utiliser les outils d'Android Studio pour cela et ne pas créer les fichiers vous-même : Clic droit sur le dossier du projet projet puis -> `New` -> `Activity` -> `Empty Activity`.

2. Concernant le layout `activity_connexion.xml`, à vous de le remplir ! Il nous faut de quoi rentrer un login, un mot de passe, avoir un bouton de connexion et pouvoir afficher un chargement pendant la connexion (avec une `ProgressBar`) comme nous le faisons pour les publications. Vous pouvez reprendre en partie le layout de connexion que vous aviez construit lors du **TD1**.

3. Du côté de la classe `ConnexionActivity`, faites-la hériter de `AbstractFeedActivity` (afin qu'elle puisse accéder au **menu**) puis récupérez les éléments importants (login, mot de passe, bouton, progress bar...). 

    Enfin, ajoutez et complétez les deux méthodes suivantes :

    ```java
    /**
     * Récupère le login et le mot de passe et 
     * fait un appel à l'API pour s'authentifier
     */
    protected void connexion() {

    }

    /**
     * Callback une fois que l'utilisateur s'est connecté avec succès
     * Sauvegarde le token puis termine l'activity
     */
    protected void connexionValidee(JWT token) {

    }
    ```

    * Terminer une activity signifie qu'on la détruit et que donc, l'écran correspondant est fermé (on retourne sur le précédent). Pour cela on utilise la méthode `finish`.

    * Comme nous l'avions fait pour les **publications**, il faut que `connexion` cache l'interface et affiche la `ProgressBar`. Il n'y a pas besoin de faire l'opération inverse dans `connexionValidee`, car de toute façon, l'interface sera fermée après la connexion.

4. Faites en sorte que l'appui sur le bouton `connexion` exécute la méthode **connexion**. 

5. Dans `AbstractFeedActivity`, modifiez la méthode `onCreateOptionsMenu` afin de charger le menu des utilisateurs connectés si l'utilisateur est effectivement connecté et le menu des visiteurs (cleui que nous utilisions jusqu'ici) si ce n'est pas le cas.

6. Toujours dans `AbstractFeedActivity`, ajoutez et complétez la méthode suivante :

    ```java
    //Affiche l'itnerface de connexion en utilisant un Intent.
    protected void ouvrirInterfaceConnexion() {

    }
    ```

7. Modifiez la méthode `onOptionsItemSelected` pour que quand le bouton "Connexion" est sélectionné, la méthode `ouvrirInterfaceConnexion` soit lancé.

8. Enfin, afin que le menu se mette à jour, nous allons faire en sorte qu'il soit rechargé chaque fois que l'écran actuel de l'application est rechargé. Pour recharger le menu, on utilise la méthode `invalidateOptionsMenu`. Récrivez donc la méthode `onResume` (que nous avons déjà rencontré avant) en faisant appel à cette méthode.

9. Lancez votre application. Dans le menu, sélectionnez "Connexion" puis connectez-vous avec les identifiants d'un utilisateur de votre `API`. Une fois de retour sur l'interface principale des publications, le menu devrait avoir changé ! Si vous avez rentré de mauvais identifiants par erreur, vous serez bloqué et devrez relancer l'application (nous allons améliorer cet aspect juste après).

10. Tentez de fermer et de rouvrir l'application, le menu devrait rester inchangé (toujours le menu réservé aux utilisateurs connectés).

</div>

Maintenant que notre système de connexion fonctionne, nous allons ajouter le fait de pouvoir se déconnecter ainsi que diverses améliorations.

Pour se déconnecter, il suffit que le client supprime son `JWT` en local. En effet, pas besoin d'interaction avec l'API pour cela, car son état **stateless** fait qu'elle ne conserve pas le jeton de son côté.

<div class="exercise">

1. Dans `AbstractFeedActivity`, ajoutez et complétez la méthode suivante :

    ```java
    protected void deconnexion() {

    }
    ```

    Cette méthode doit **afficher une boîte de dialogue** qui demande à l'utilisateur s'il est sûr de se connecter et lui laisser le choix de confirmer ou d'annuler :

    * En cas de confirmation, vous devez supprimer le `JWT` conservé dans l'application puis régénérer le menu.

    * En cas d'annulation, on ferme la boîte de dialogue.

    Pour rappel, on peut construire une boîte de dialogue ainsi :

    ```java
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.setTitle("titre");
    builder.setMessage("Message");

    //On fait cela pour pouvoir utiliser les méthodes de la classe dans "onClick"
    MaClasseActivity context = this;
    builder.setPositiveButton("Nom bouton confirmation", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialogInterface, int i) {
            //Code éxécuté en cas de validation
        }
    });
    builder.setNegativeButton("Nom bouton annulation", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialogInterface, int i) {
            //Code éxécuté en cas d'annulation

            //Permet de fermer la boîte de dialogie
            dialogInterface.cancel();
        }
    });

    //Affiche la boîte de dialogue
    builder.show();
    ```

2. Faites les modifications nécessaires dans `onOptionsItemSelected` pour lancer la méthode précédente quand l'utilisateur sélectionne le bouton de déconnexion.

3. Lancez l'application et vérifiez que votre nouvelle fonctionnalité marche (basculement sur le menu visiteur après déconnexion).

4. Après fermeture de l'application, si vous la rouvrez, le menu "visiteur" doit encore être celui chargé.

</div>

En cas d'échec de la connexion (si les identifiants ne sont pas bons), notre application doit être capable de détecter l'erreur et prévenir l'utilisateur. Lors du **TD1**, les `Consumer` vous ont été présenté, mais nous avions aussi parlé des `Runnable` qui ont un rôle similaire (représente une fonction, mais qui ne prend aucuns paramètres et ne renvoi rien).

Dans le `JsonHttpResponseHandler`, il est possible de détecter l'échec de l'aboutissement d'une requête et de réagir en fonction, en implémentant la méthode :

```java
@Override
public void onFailure(int statusCode, Header[] headers, Throwable throwable, JSONObject errorResponse) {
    ...       
}
```

L'idée serait donc d'appeler un `Runnable` (passé en paramètre) qui permettrait d'exécuter du code sur l'interface en cas d'erreur.

Pour déclencher l'exécution d'un `Runnable`, on utilise la méthode **run** :

```java

//Fonction / méthode sans paramètres ni retour
public void exemple() {

}

public void exemple2(Runnable fun) {
    ...
    fun.run();
}

public void exemple3() {
    this.exemple2(this::exemple);
}

```

<div class="exercise">

1. Dans `AuthentificationAPI`, ajoutez un paramètre `failure` de type `Runnable`. Dans l'handler, quand l'API renvoie une erreur, déclenchez votre `Runnable`.

2. Dans `ConnexionActivity`, ajoutez une méthode `erreurIdentifiants` qui **arrête lanimation du chargement** (cache la `ProgressBar` et ré-affiche l'interface) puis affiche une **boîte de dialogue** simple (sans bouton de confirmation ou d'annulation, juste avec un message) informant l'utilisateur que ses identifiants sont invalides.

3. Modifiez le code de `connexion` afin préciser la méthode `erreurIdentifiants` comme `Runnable` lors de l'appel à l'API.

4. Faites en sorte qu'à chaque rafraichissement de `MainActivity`, on affiche ou on cache le bouton **Publier** selon le fait que l'utilisateur soit connecté ou non. En effet, ce bouton ne doit être accessible qu'aux utilisateurs connectés. Vous pouvez par exemple coder une nouvelle méthode et l'appeler dans le `onResume`.

5. Créez une méthode `afterDeconnexion` dans `AbstractFeedActivity` avec une visibilité `protected` et déplacez le code exécuté lors de la confirmation de la déconnexion (dans la boîte de dialogue) dans cette méthode. Il doit s'agir simplement de l'appel permettant de régénérer le menu. Dans le code de votre boîte de dialogue, faites donc un appel à `afterDeconnexion`.

6. Dans `MainActivity`, servez-vous du mécanisme de réécriture de méthode pour faire en sorte de cacher le bouton **Publier** après la déconnexion.

7. Lancez votre application, déconnectez-vous (si ce n'est pas déjà fait) puis tentez de vous reconnecter en saisissant de mauvais identifiants. Vérifiez que tout fonctionne comme attendu. Quand vous vous déconnectez, le bouton "Publier" doit disparaitre.

</div>

## Diverses fonctionnalités

Nous allons maintenant nous attaquer à l'implémentation des fonctionnalités qui demandent d'être authentifié. En effet, jusqu'ici, notre `JWT` ne servait pas beaucoup !

Pour pouvoir inclure le `JWT` dans nos requêtes qui ont en besoin, il faut l'ajouter au **header** du client ainsi :

```java
AsyncHttpClient client = new AsyncHttpClient();
client.addHeader("Authorization", "Bearer " + token);
```

Plutôt que de répéter ces étapes à chaque fois (car il faut aller chercher le token dans le manager, etc...) nous allons plutôt créer une classe fille de `AsyncHttpClient` qui enregistrera automatiquement notre token (dans le code de constructeur). Ainsi, on pourra simplement faire :

```java
AsyncHttpClient client = new AuthenticatedAsyncHttpClient();
client.post(...)
```

<div class="exercise">

1. Dans le package `api`, créez une classe `AuthenticatedAsyncHttpClient` qui étend `AsyncHttpClient`.

2. Créez un constructeur (sans paramètres) qui doit :

    * Appeler le constructeur parent (bien entendu)

    * Aller chercher le `JWT` stocké dans l'application.

    * S'il n'est pas **null**, ajoute (à lui-même) un header `Authorization`, comme nous l'avons vu juste avant.

</div>

### Publier

Pour poster une nouvelle publication, nous allons ouvrir une boîte de dialogue et nous demanderons à l'utilisateur de taper son message. Une fois celui-ci validé, nous ferons alors appel à l'API qui nous retournera la publication complète (avec son id) et nous n'aurons plus qu'à l'ajouter sur l'interface, dans la liste des publications.

<div class="exercise">

1. Dans la classe `PublicationAPI`, ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Créé une publication en faisant appel à l'API
     */
    public static void publier(Publication publication, Consumer<Publication> callback) {
        try {
            //Initialisation du client
            StringEntity payloadEntity = new StringEntity(/* ... */);
            //Envoie de la requête et gestion du résultat...
            
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
    }    
    ```

    * C'est une requête qui nécessite de posséder un `JWT`. Il faudra donc nous servir de la classe que nous avons défini dans l'exercice précédent et non pas un client classique.

    * Comme c'est une méthode `POST`, il faudra encoder le `payload` de la publication. La méthode de sérialisation va donc enfin vous servir !

    * L'objet récupéré à l'issu de l'appel (publication créée) devra être converti en `Publication` et passé au Consumer.

    * Pour l'envoi de la requête (pour les paramètres à spécifier), vous pouvez vous inspirer de ce que vous avez fait pour la méthode `connexion` dans `AuthentificationAPI`.

2. Au niveau de `MainActivity`, il va falloir légèrement modifier la méthode `ajouterPublication`. En effet, quand on ajoute les publications reçues lors du chargement de l'interface, on les ajoute de haut en bas. Par contre, pour une publication qu'on vient de créer, il faut l'ajouter tout en haut. Pour cela, il suffit de préciser un paramètre **index** dans la méthode `addView` pour spécifier où on ajoute notre template. Dans notre cas, on l'ajoute en tout en haut, donc en position 0 : `addView(template, 0)`. Si on ne précise pas d'index (comme c'est le cas actuellement), elle est ajoutée à la fin. 
    
    Ajoutez donc un paramètre **booléen** qui permet de savoir si on ajoute à la fin ou au début et adaptez votre méthode en conséquence. Par conséquent, il faudra aussi légèrement adapter votre méthode `finChargementPublications`.

3. Toujours dans `MainActivity`, ajoutez et complétez les deux méthodes suivantes :

    ```java
    /**
     * Ajoute une nouvelle publication en haut de la liste des publications
     */
    protected void ajouterPublicationEnHaut(Publication publication) {

    }

    /**
     * Construit une publciation puis appelle l'API pour l'enregsitrer
     */
    protected void creerPublication(String message) {

    }
    ```

    * Normalement, vous devriez être en mesure d'identifier la méthode `callback` à spécifier lors de l'appel à l'API.

    * Dans `creerPublication`, il faudra instancier une publication puis affecter son message avec son setter.

4. Encore dans `MainActivity`, nous souhaitons disposer d'une méthode qui affiche une boîte de dialogue permettant de rentrer un message. Pour cela, on doit inclure un `EditText` dans la boîte de dialogue :

    ```java
    AlertDialog.Builder builder = new AlertDialog.Builder(this);
    builder.setTitle("Titre");

    //Creation du champ de saisie
    final EditText input = new EditText(this);
    //On spécifie le type de données rentrées (texte normal, pas de mot de passe...)
    input.setInputType(InputType.TYPE_CLASS_TEXT);

    //On affecte cet EditText comme vue principale de la boîte de dialogue
    builder.setView(input);
    
    //Même chose que précédemment (pour pouvoir accèder au méthodes de l'activity)
    MonAcitivty context = this;
    builder.setPositiveButton("Bouton confirmer", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialogInterface, int i) {
            //Recupération du texte saisi
            String texteSaisi = input.getText().toString();
            ...
        }
    });
    builder.setNegativeButton("Bouton annuler", new DialogInterface.OnClickListener() {
        @Override
        public void onClick(DialogInterface dialogInterface, int i) {
            //Fermeture
            dialogInterface.cancel();
        }
    });
    builder.show();
    ```

    Ajoutez donc une méthode `demanderMessage` qui permet d'ouvrir une boîte de dialogue demandant à l'utilisateur de saisir le contenu de sa publication. Une fois confirmé, on doit **créer le message**.

5. Faites en sorte que l'appui sur le bouton **publier** déclenche la méthode que nous avons créé à la question précédente.

6. Lancez votre application. Connectez-vous si ce n'est pas déjà fait puis vérifiez que tout fonctionne en tentant de publier.

</div>

### Suppression d'une publication

Pour supprimer une publication, nous allons utiliser un événement particulier qui est déclenché qu'on reste appuyé longtemps sur un élément. Quand un utilisateur souhaitera supprimer une de ses publications, il devra appuyer dessus (pendant un certain laps de temps), ce qui affichera une boîte de dialogue lui demandant de confirmer. L'application enverra alors la demande de suppression à l'API et enfin, on supprimera la publication de la liste, sur l'interface.

Lors de l'appel à l'**API**, nous utiliserons un `Runnable` et non pas un `Consumer`. En effet, vous l'avez sans doutes constaté lors de la création de l'API lors du **TD2**, mais un appel (valide) à `DELETE` ne renvoie rien. Par conséquent, nous n'utiliserons pas un `JSONHttpHandler` mais plutôt un `TextHttpResponseHandler` dans lequel il faudra obligatoirement implémenter deux méthodes (mais nous ne nous servirons que de `onSuccess`).

Autrement, la méthode **delete** s'utilise comme un **get** (pour les paramètres). Donc pas besoin de context, de payload, etc...


```java
client.delete(url, new TextHttpResponseHandler() {

    @Override
    public void onSuccess(int statusCode, Header[] headers, String responseString) {
        //La ressource a bien été supprimée...
    }

    @Override
    public void onFailure(int statusCode, Header[] headers, String responseString, Throwable throwable) {
        //Pour l'instant, on ne fait rien ici (mais idéalement, il faudrait)
    }
});
```

Du côté de l'interface, l'événement à traiter est `OnLongClickListener`. Ici, contrairement à la plupart des méthodes que nous avons codées, il y a tout intérêt à **récupéré la vue** fournie lors du déclenchement de l'événement. Cette vue est la cible de l'événement, dans notre cas, la `CardView` de la publication. Nous en aurons besoin pour la supprimer de la liste des publications.

```java
view.setOnLongClickListener((view) -> this.methode(view));

public void methode(View view) {
    
}

//Il est bien sûr possible d'ajouter des paramètres, si nécessaire :

view.setOnLongClickListener((view) -> this.methode(view, objet));

public void methode(View view, Object objet) {
    
}
```

Dans le `LinearLayout` stockant les publications, il est très simple de supprimer une vue en appelant la méthode `removeView`.

```java
layout.removeView(view);
}
```

<div class="exercise">

1. Dans `PublicationAPI`, ajoutez et complétez la méthode suivante :

    ```java
    public static void supprimerPublication(Publication publication, Runnable callback) {

    }
    ```

    * Attention, il s'agit d'une requête `DELETE` où l'utilisateur doit être authentifié.

    * On cible une ressource en particulier. Donc, il faut compléter l'URL de base avec l'identifiant e la publication.

    * Aidez-vous de la documentation et des explications données sur la méthode **delete**, un peu plus haut.

2. Dans `MainActivity`, ajoutez et complétez les méthodes suivantes :

    ```java
    /**
     * Retire la vue passée en paramètre de la zone (liste) des publications
     */
    protected void retirerPublication(View viewPublication) {

    }

    /**
     * Fait un appel à l'API pour supprimer la publication
     */
    protected void supprimerPublication(Publication publication, View viewPublication) {

    }
    ```

    Nous ne l'avons pas encore vue, mais nous pouvons construire un `Runnable`qui déclenche une méthode avec des paramètres :

    ```java
    Runnable runnable = () -> this.methode(val1, val2, ...);

    //Ou bien directement comme paramètre d'une méthode
    MonAPI.appel(var, () -> this.methode(val1, val2, ...));
    ```

    Cela va vous être utile, car comme vous vous en doutez, dans `supprimerPublication`, le `callback` va être la méthode `retirerPublication` à laquelle on passe la vue à retirer une fois que l'API a confirmée la suppression de la ressource. Nous n'aurions pas pu mettre un `Consumer`, car ici la vue de la publication existe déjà n'est pas une donnée générée, récupérée et appliquée par une autre méthode dans un autre contexte.

3. Toujours dans `MainActivity`, créez et complétez la méthode suivante :

    ```java
    /**
     * Affiche une boîte de dialogue demandant la confirmation de la supression de la publication
     * Une fois confirmé, la méthode faisant l'appel à l'API est déclenchée pour supprimer la
     * publication.
     *
     * La méthode renvoie falsesi l'utilisateur n'a pas le droit de supprimer la publication.
     * Sinon, true est renvoyé après avoir affiché la boîte de dialogue.
     */
    protected boolean demanderConfirmationSupression(Publication publication, View viewPublication) {
        
    }
    ```

    * **Attention**, dans cette méthode, avant d'afficher la boîte de dialogue, il faut vérifier que l'utilisateur est l'auteur de la publication. S'il n'est pas connecté ou n'est pas l'auteur de la publication, on ne fait rien et on renvoie **false**. Il est très aisé de vérifier cela grâce à la classe `UtilisateurUtils` et les données de l'objet `Publication`.

    * Pour le code de la boîte de dialogue, il doit y avoir un bouton de confirmation et d'annulation. Vous pouvez donc reprendre en partie ce que nous avions fait pour la boîte de dialogue pour se déconnecter.

    * Nous sommes obligés de faire en sorte que cette méthode renvoie un **booléen** car nous allons la relier aux événements `OnLongClickListener` qui nécessitent d'être traités avec une méthode qui renvoie un booléen.

4. Dans la méthode `ajouterPublication`, faites en sorte qu'un appui long sur la `CardView` de la publication déclenche la méthode `demanderConfirmationSupression`. Il faudra donc traiter l'événement `OnLongClickListener` et passer à la fois une **publication** (celle en train d'être crée) ainsi que la vue (la **CardView** de la publication ou bien simplement la vue sur laquelle est déclenchée l'événement).

5. Lancez votre application. Vérifiez qu'en étant déconnecté, vous ne pouvez rien supprimer (la boîte de dialogue ne doit pas s'afficher). Vérifiez qu'en étant connecté, vous pouvez supprimer vos publications (en faisant un appui long dessus), mais pas celles d'autres utilisateurs (la boîte de dialogue ne doit pas s'afficher dans ce cas aussi).

</div>

### Inscription

Pour l'inscription, celle-ci se fera assez simplement. Le layout sera très similaire à celui de connexion, il faut seulement rajouter un champ pour l'adresse email.

<div class="exercise">

1. Créez une nouvelle **activity** (toujours avec les outils d'Android) nommée `InscriptionActivity` (héritant de `AbstractFeedActivity`). Pour le layout, nous avons besoin : d'une progresse bar pour le chargement, d'un champ pour le login, pour le mot de passe, pour l'adresse mail. Vous veillerez à bien utiliser l'attribut `android:inputType` sur les champs du mot de passe et de l'adresse email afin d'adapter le clavier (et le rendu) selon la nature du contenu (des étoiles pour le mot de passe, un clavier adapté pour l'adresse email...)

2. Dans le package `api` créez une classe `UtilisateurAPI`. Ajoutez une méthode de classe permettant de créer un nouvel utilisateur sur l'API. Vous pouvez vous inspirer de ce que vous avez fait dans les deux autres classes qui utilisent l'API. A priori, pas besoin de `Consumer`, car il n'y a pas d'intérêt (en tout cas en l'état) de récupéré un utilisateur créé. Vous pouvez donc utiliser un simple `Runnable`. L'inscription d'un utilisateur n'est pas une fonctionnalité qui nécessite d'être authentifié. 

    Comme nous l'avions fait pour la **connexion**, il serait bien de gérer le cas d'échec de la requête avec un deuxième **callback** (pour indiquer à l'utilisateur qu'une erreur est survenue pendant l'inscription).

    Pensez bien à utiliser la méthode de **sérialisation** que nous avions créé dans la lasse `Utilisateur`.

3. Dans la classe `InscriptionActivity`, inspirez-vous de ce que vous avez déjà fait dans `LoginActivity`, mais cette fois-ci, pour réaliser l'inscription d'un utilisateur. À la fin de l'inscrption, on veut seulement fermer l'interface d'inscription. Un message d'erreur devra être affiché (dans une boîte de dialogue) en cas d'échec de l'inscription.

4. Réalisez les modifications nécessaires dans `AbstractFeedActivity` afin de pouvoir ouvrir l'interface d'inscription à partir du menu.

5. Lancez l'application et testez d'inscrire un nouvel utilisateur (puis de vous connecter). N'oubliez pas aussi de tester de rentrer des données invalides pour vérifier qu'un message est bien affiché à l'utilisateur dans ce cas.

</div>

### Pages personnelles

Nous souhaitons maintenant réaliser la fonctionnalité "page personnelle" dont le but est d'afficher la liste des publications d'un utilisateur (en cliquant sur son profil à partir d'une publication). On pourra aussi afficher la page personnelle de l'utilisateur connecté.

<div class="exercise">

1. Dans `UtilisateurAPI`, ajoutez et complétez la méthode suivante :

    ```java
    /**
     * Charge toutes les publications d'un utilisateur dont l'identifiant est passé en paramètre
     */
    public static void loadAllPublications(int idUtilisateur, Consumer<List<Publication>> callback) {

    }
    ```

2. Créez une nouvelle activity `PagePersonelleActivity`. Pour le layout `activity_page_personnelle.xml`, nous souhaitons reprendre quasiment le même que celui de `activity_main.xml` sans les boutons "publier" et "recharger".

3. Concernant la classe `PagePersonelleActivity` faites la hériter de `AbstractFeedActivity`. Vous pouvez reprendre en grande partie le code de `MainActivity`, à l'exception du code liés aux fonctionnalités des deux boutons publier / recharger. 

    Néanmoins, il y a une différence : les publications à charger sont celles d'un utilisateur précis L'identifiant de cet utilisateur est donc passé à travers l'**intent** ayant servi à démarrer cette activity. Il faut donc le récupérer et charger les publications en utilisant la méthode que vous avez développé dans `UtilisateurAPI`.

    ```java

        private int idUtilisateur;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            this.idUtilisateur = this.getIntent().getIntExtra("cle", valeurparDefaut);
        }
    ```

4. Dans votre classe `MainActivity`, faites en sorte que l'appui sur l'image de profil de l'auteur d'une publication ouvre sa page personnelle. Il faudra donc créer un **intent** en ajoutant l'identifiant de l'utilisateur concerné.

5. Dans `AbstractFeedActivity`, faites en sorte que l'appui sur le bouton **Ma page** ouvre la page de l'utilisateur connecté. Vous devriez pouvoir facilement trouver son identifiant grâce à l'une des classes que vous avez développées précédemment.

6. Démarrez votre application et vérifiez que tout marche bien, en affichant votre page personnelle, ou bien en affichant celle d'un autre utilisateur en appuyant sur son image de profil, sur une publication.

</div>

### Derniers ajouts

Pour terminer, nous allons faire quelques derniers petits ajouts et améliorations.

<div class="exercise">

1. Faites en sorte qu'un appui sur le bouton du menu **Feed** ouvre l'interface principale `MainActivity`. Il s'agit en quelque sorte d'un bouton pour retourner à l'accueil.

2. Il y a beaucoup de code en commun entre `PagePersonnelleActivity` et `MainActivity`. Refactorez votre code intelligemment en utilisant l'héritage pour éliminer les redondances et centraliser le code commun. Vous pouvez notamment mettre en palce une classe abstraite, avec certaines méthodes abstraites, des attributs en ocmmun...

3. Trouvez un moyen pour que quand on soit dans une interface donnée, par exemple, l'interface principale, le bouton `Feed` ne rouvre pas la même interface encore une fois. Pareil pour l'interface de connexion, d'inscription et les pages personnelles. Indice : pensez à l'héritage...!

4. En modifiant `AbstractFeedActivity`, faites en sorte que quand on rouvre l'application ou qu'on recharge simplement l'interface, l'interface de connexion s'ouvre si l'utilisateur était connecté et ne l'est plus à cause d'un token qui a expiré. Il faudra probablement ajouter une méthode dans `UtilisateurUtils` (pour voir si l'utilisateur doit se reconnecter, c'est-à-dire qu'il possède déjà un token, mais expiré). Il faudra aussi penser à **supprimer le token** avant d'ouvrir l'interface, sinon, il y aura une boucle de récursivité infinie...! (l'utilisateur a expiré, donc il est redirigé sur l'interface de connexion, il est toujours expiré, donc il est redirigé sur l'interface de connexion, etc...). Vous pouvez éventuellement tester en baisant le paramètre `ttl` dans votre API (à quelques secondes).

5. Dans les pages personnelles des utilisateurs, nous aimerions que le `login` de l'utilisateur soit chargé. Cela implique donc une seconde requête à l'API pour charger le profil de l'utilisateur :

    * Modifiez votre layout `activity_page_personnelle.xml` pour ajouter une zone pour afficher le login de l'utilisateur.

    * Ajoutez une nouvelle méthode à `UtilisateurAPI` pour charger un utilisateur à partir de son identifiant.

    * Modifiez le code de la classe `PagePersonnelleActivity` pour charger l'utilisateur et afficher son nom (en plus du chargement de ses publications).

</div>

## Pour aller plus loin

Même si l'application que nous avons construite est assez complète, il y a plein de points que vous pouvez améliorer :

* Traiter les échecs de requêtes dans tous les appels à l'API.

* Ajouter la modification du profil de l'utilisateur (adresse email / mot de passe) en exploitant la méthode `PATCH`. Il faudra probablement ajouter une nouvelle méthode de sérialisation (ou modifier l'ancienne) pour gérer cela, dans `Utilisateur`.

* Ajouter l'internationalisation et donc ne plus avoir de chaînes de caractères codées en dur.

* Transformer certaines classes en singleton, au lieu d'avoir des classes utilisées en mode statique uniquement.

* Ajouter plus de message de feedback, lors de l'exécution des différentes fonctionnalités.

* Améliorer l'ergonomie, les menus.

* Réactiver la pagination du côté de l'API et mettre en place un système qui charge les publications page par page quand on scrolle.

## Conclusion

Après avoir appris les bases du développement d'applications mobiles sous **Android** lors du **TD1**, vous avez appris à concevoir votre propre **API Rest** lors du **TD2** et enfin, vous avez construit une application exploitant quasi-pleinement cette API lors du **TD3**. Vous possédez maintenant une base solide pour attaque le [projet]({{site.baseurl}}/tutorials/projet.html) !
