---
layout: post
title: "TD/TP 1 -- Contrôleurs et mise en page"
categories: jekyll update

mathjax: true
---


## 1.1 - Premiers pas avec JavaFX : 

Rappelez-vous que JavaFX est un framework Java pour le développement des interfaces graphiques (GUI, ou *Graphical User Interface*) en Java. Une GUI JavaFX est construite grâce à un **graphe de scène**, où les **nœuds** correspondent à un ensemble d'éléments graphiques organisés de manière hiérarchique. La scène (un objet de type [`Scene`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/Scene.html)) est associée à une fenêtre qui correspond à un objet de type [`Stage`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/stage/Stage.html).

Dans l'exemple précédent, nous avons créé une application JavaFX basique (`HelloApplication`) avec une fenêtre unique. En principe, une application peut avoir plusieurs fenêtres, mais une est obligatoire -- la fenêtre principale (**primary stage** en anglais). Celle-ci est fournie automatiquement par l'environnement d'exécution JavaFX. Pour illustrer cela, nous allons créer un nouveau package `com.example.partie1` et créer une nouvelle "application" basique à l'intérieur. 

Pour créer ce nouveau package, il suffit de sélectionner le répertoire `Java` dans l'arborescence du projet, de faire un clic droit, de sélectionner `New` > `Package`, et de saisir son nom (`com.example.partie1`).  

![](img/newPackage.png)

Si cela n'est pas fait automatiquement par IntelliJ, vous devez aussi intégrer ce nouveau package dans la description du module. Sans cela, le code lancera une erreur à l'exécution car il ne pourra pas faire le lien entre votre interface et JavaFX. Le code à ajouter dans `module-info.java` est le suivant:

```java
    exports com.example.partie1;
```

 Ensuite, si vous copiez le code suivant dans votre nouveau package (après l'avoir sélectionné dans l'arborescence), la classe Java, et le fichier associé, seront créés. L'exécution de ce code lance une fenêtre vide et cachée (non visible à l'utilisateur) :

```java
import javafx.application.Application;
import javafx.stage.Stage;

public class MaPremiereClasseJavaFX extends Application {

    @Override
    public void start(Stage primaryStage) {
        // le code pour enrichir votre fenêtre
    }
}
```

Notez que la méthode `main(String args[]` n'est pas nécessaire ici, car le point d'entrée d'une application JavaFX est la méthode `start(Stage primaryStage)`. En revanche, vous pouvez toujours ajouter une méthode `main(String args[]` pour  intégrer des paramètres à la ligne de commande. Par exemple, le code ci-dessous permet de changer la taille de la fenêtre en fonction des paramètres passés en ligne de commande.

```java
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.layout.StackPane;
import javafx.stage.Stage;

public class MaPremiereClasseJavaFX extends Application {
    
    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) {
        // Récupérer les arguments passés en ligne de commande
        Parameters params = getParameters();
        int width = 300;
        int height = 150;

        if (params.getRaw().size() > 0) {
            // Utiliser le premier argument passé en ligne de commande comme largeur de la fenêtre
            width = Integer.parseInt(params.getRaw().get(0));
        }

        if (params.getRaw().size() > 1) {
            // Utiliser le deuxième argument passé en ligne de commande comme hauteur de la fenêtre
            height = Integer.parseInt(params.getRaw().get(1));
        }
    
        // Créer une scène 
        StackPane root = new StackPane();
        Scene scene = new Scene(root, width, height);

        // Définir la scène principale de l'application
        primaryStage.setScene(scene);
        primaryStage.setTitle("Exemple d'utilisation des arguments en ligne de commande");
        primaryStage.show();
    }
}
```

Pour rappel, vous pouvez directement exécuter ce code en cliquant sur l’icône de lancement (triangle vert) à côté du nom de la méthode ``main``, puis en sélectionnant ``Run``. L'application sera alors exécutée sans paramètre en ligne de commande. L'exécution de ce code produit une fenêtre vide, mais visible, de taille 300x150 pixels. Cette fenêtre possède un titre et voici comment elle s'affiche sur Linux :

![](img/Premiere-page-javafx.png)


 Si vous voulez tester votre application avec des paramètres en ligne de commande, vous pouvez éditer la configuration d'exécution en cliquant sur l’icône de lancement et sélectionnant ``Modify Run Configuration``. 

![](img/Premiere-page-javafx_modifyConfig0.png)

Vous pourrez alors définir des paramètres en ligne de commande à utiliser avec cette configuration, comme par exemple ``500 500`` (500 pixels de large et de haut), dans le champs de saisi ``Program arguments``. 

![](img/Premiere-page-javafx_modifyConfig1.png)

Cette fois-ci, vous utiliserez l’icône de lancement (triangle vert) dans le bandeau supérieur pour lancer cette nouvelle configuration d'exécution et constaterez que la fenêtre a une taille de 500x500 pixels comme défini par les paramètres en ligne de commande.

![](img/Premiere-page-javafx_modifyConfig2.png)


Dans le code ci-dessus, la méthode statique `launch(String[] args)` de la classe `javafx.application.Application` va déclencher la méthode dynamique `start(Stage primaryStage)`. Notez que JavaFX est conçu de façon à ce que la méthode `launch(String[] args)` détecte correctement la méthode `start(Stage primaryStage)` à exécuter en fonction de la classe héritant de `Application` où `launch(String[] args)` a été lancée. Dans l'exemple ci-dessus cette classe est `MaPremiereClasseJavaFX`.


Si vous êtes sur un autre système d'exploitation (Windows, Mac OS, etc.) le design de la fenêtre sera différent, l'environnement JavaFX faisant le travail nécessaire d'adaptation. Dans tous les cas, cette fenêtre contiendra une barre de titre et un emplacement pour afficher la scène.

## 1.2 - Conception d'une première interface graphique basique [(JavaFX Tutorial)](https://fxdocs.github.io/docs/html5/#_layout) :

Dans cette partie, nous allons créer différents contrôleurs et les mettre en page dans une fenêtre graphique. Nous allons donc construire différents graphes de scène JavaFX. 

Nous allons supposer que nous essayons de reproduire la maquette (ou ["mock-up"](https://fr.wikipedia.org/wiki/Mock-up)) suivante:

![JavaFX Tutorial example mockup](img/vboxandhboxapp_mockup.png)

Cette interface a une ligne de contrôles en haut de sa fenêtre. Plus précisément, nous avons  un bouton "Refresh" à gauche puis un lien hypertexte "Sign Out" à droite. En dessous, on trouve un tableau de valeurs avec deux colonnes "Last Name" et "First Name". Ce tableau occupe toute la hauteur et la largeur de la fenêtre, à l'exception d'un bandeau en bas de la fenêtre. Ce bandeau est composé d'un trait de séparation et d'un bouton "Close" en dessous à droite. 

L'une des premières difficultés consiste à identifier les conteneurs adéquates pour faire cette mise en page dans la `Scene` (i.e. l'espace associé à la fenêtre, de type `Stage`). Pour cela, on peut se référer à la [liste des classes disponibles dans le package `javafx.scene.layout`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/layout/package-summary.html) ou à d'autres sources maintenues par la communauté (p.ex. "How to use JavaFX Layouts" de E. Eden-Rump). La figure ci-dessous résume les principales mises en page (i.e. "layouts") :

![Layout types by E. Eden-Rump](img/LayoutTypes-768x304.png)

Nous allons plus particulièrement utiliser les classes `VBox` et `HBox`, car elles permettent d'aligner et dimensionner les éléments contenus. Cela permettra d'étirer le tableau horizontalement et de contrôler l'alignement des autres contrôles. La figure suivante présente comme l'interface graphique peut-être décomposée avec ces composants :

![JavaFX Tutorial mockup with VBox and HBox ](img/vboxandhboxapp_brokendown.png)

Dans un premier temps, nous ajoutons les différents conteneurs et intégrons à l'intérieur les contrôleurs en suivant le modèle précédent. Le code ci-dessous correspond à cela :

```Java
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Hyperlink;
import javafx.scene.control.Separator;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class VBoxAndHBoxApp extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception {

        // Création du conteneur principal
        VBox vbox = new VBox();

        // Création du conteneur correspondant à la ligne de contrôle haut dessus du tableau
        HBox topControls = new HBox();
        Button btnRefresh = new Button("Refresh");

        HBox topRightControls = new HBox();
        Hyperlink signOutLink = new Hyperlink("Sign Out");
        topRightControls.getChildren().add( signOutLink );

        topControls.getChildren().addAll( btnRefresh, topRightControls );

        // Création du tableau avec les deux colonnes
        TableView<Object> tblCustomers = new TableView<>();
        TableColumn<Object, String> lastNameCol = new TableColumn<>("Last Name");
        TableColumn<Object, String> firstNameCol = new TableColumn<>("First Name");
        tblCustomers.getColumns().addAll( lastNameCol, firstNameCol );

        // Création de la ligne de séparation 
        Separator sep = new Separator();

        // Création du bandeau en bas de la fenêtre
        HBox bottomControls = new HBox();
        Button btnClose = new Button("Close");
        bottomControls.getChildren().add( btnClose );

        // Ajout des contrôleurs au conteneur principal
        vbox.getChildren().addAll(
                topControls,
                tblCustomers,
                sep,
                bottomControls
        );

        // Ajout du conteneur à la scene
        Scene scene = new Scene(vbox );

        // Ajout de la scene à la fenêtre et changement de ses paramètres (dimensions et titre)
        primaryStage.setScene( scene );
        primaryStage.setWidth( 800 );
        primaryStage.setHeight( 600 );
        primaryStage.setTitle("VBox and HBox App");

        // Affichage de la fenêtre
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

Ce code génère la fenêtre suivante :

![](img/vboxandhboxapp1.png)

Elle ne correspond pas encore totalement a notre maquette. Les marges et l'alignement des composants ne correspondent pas. 

Nous commençons par définir l'alignement des contrôleurs du bandeau supérieur. Pour cela, nous utilisons la méthode `setAlignment` de `HBox` pour aligner le contenu (i.e.  le bouton `Refresh` et l'hyperlien `Sign Out`) comme souhaité. On modifie le code précédent de la manière suivante :

```Java

        // Création du conteneur correspondant à la ligne de contrôle haut dessus du tableau
        HBox topControls = new HBox();
        topControls.setAlignment( Pos.BOTTOM_LEFT );
        Button btnRefresh = new Button("Refresh");

        HBox topRightControls = new HBox();
        topRightControls.setAlignment( Pos.BOTTOM_RIGHT );
        Hyperlink signOutLink = new Hyperlink("Sign Out");
        topRightControls.getChildren().add( signOutLink );

        topControls.getChildren().addAll( btnRefresh, topRightControls );
```

Lorsque l'on exécute le code, on obtient toujours la même mise en page pour le bandeau supérieur, malgré l'utilisation de `setAlignment`. En réalité, l'hyperlien est bien à droite mais le conteneur `topRightControls` ne s'étire pas jusqu'à l'extrémité droite de la fenêtre. Cette espace est déjà pris par le conteneur `topControls`. Nous devons donc changer cela en  spécifiant que `topRightControls` doit prendre autant d'espace que possible dans le conteneur parent `topControls`. L'instruction `HBox.setHgrow(topRightControls, Priority.ALWAYS );` permet d'obtenir ce résultat.   


```Java

        // Création du conteneur correspondant à la ligne de contrôle haut dessus du tableau
        HBox topControls = new HBox();
        topControls.setAlignment( Pos.BOTTOM_LEFT );
        Button btnRefresh = new Button("Refresh");

        HBox topRightControls = new HBox();
        HBox.setHgrow(topRightControls, Priority.ALWAYS );
        topRightControls.setAlignment( Pos.BOTTOM_RIGHT );
        Hyperlink signOutLink = new Hyperlink("Sign Out");
        topRightControls.getChildren().add( signOutLink );

        topControls.getChildren().addAll( btnRefresh, topRightControls );
```

Cette modification permet d'obtenir la fenêtre suivante :

![](img/vboxandhboxapp2.png)

Pour les mêmes raisons, le tableau central n'est pas étendu jusqu'en bas de la fenêtre. Nous ajoutons donc l'instruction `VBox.setVgrow( tblCustomers, Priority.ALWAYS );` à la suite de la création du tableau.

```Java
        // Création du tableau avec les deux colonnes
        TableView<Object> tblCustomers = new TableView<>();
        TableColumn<Object, String> lastNameCol = new TableColumn<>("Last Name");
        TableColumn<Object, String> firstNameCol = new TableColumn<>("First Name");
        tblCustomers.getColumns().addAll( lastNameCol, firstNameCol );
        VBox.setVgrow( tblCustomers, Priority.ALWAYS );

```
![](img/vboxandhboxapp3.png)

Il reste encore le tableau central à modifier pour que les colonnes soient de même taille et prennent toute la largeur du conteneur. Pour cela, nous pouvons utiliser l'instruction `tblCustomers.setColumnResizePolicy(TableView.CONSTRAINED_RESIZE_POLICY);`. Ainsi, la largeur disponible est répartit de manière équitable entre toutes les colonnes tout en garantissant que la largeur totale de la table ne dépasse pas la largeur du conteneur parent. 

```Java 
        // Création du tableau avec les deux colonnes
        TableView<Object> tblCustomers = new TableView<>();
        tblCustomers.setColumnResizePolicy(TableView.CONSTRAINED_RESIZE_POLICY);
        TableColumn<Object, String> lastNameCol = new TableColumn<>("Last Name");
        TableColumn<Object, String> firstNameCol = new TableColumn<>("First Name");
        tblCustomers.getColumns().addAll( lastNameCol, firstNameCol );
        VBox.setVgrow( tblCustomers, Priority.ALWAYS );
```

Nous ajoutons aussi l'instruction `bottomControls.setAlignment(Pos.BOTTOM_RIGHT );` pour aligner  à droite le bouton `Close`.

```Java
        // Création du bandeau en bas de la fenêtre
        HBox bottomControls = new HBox();
        bottomControls.setAlignment(Pos.BOTTOM_RIGHT );
        Button btnClose = new Button("Close");
        bottomControls.getChildren().add( btnClose );
```

![](img/vboxandhboxapp4.png)

Il est également possible de contrôler la marge intérieure des conteneurs pour espacer légèrement les éléments entre eux. La méthode `setMargin(Node child, Insets value)` de [`Vbox`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/scene/layout/VBox.html#setMargin(javafx.scene.Node,javafx.geometry.Insets)) et de [`HBox`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/scene/layout/HBox.html#setMargin(javafx.scene.Node,javafx.geometry.Insets)) permet de contrôler cela (disponibles dans d'autres conteneurs du même type). Le premier paramètre est le conteneur sur laquelle la marge sera appliquée (p.ex. `topControls` ou `tblCustomers`). La deuxième paramètre correspond à la marge appliquée sous la forme d'un objet [`Insets`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/geometry/Insets.html). Cet objet représente la marge à l'intérieure d'une zone rectangulaire (*inside offset* ou *padding*), i.e. la marge en haut, celle à droite, celle en bas et celle à gauche. Il est possible de mettre la même marge partout en créant  par exemple l'objet `new Insets(10.0d)` pour avoir une marge de 10 pixels (en double nécessairement) autour des éléments dans le conteneur. Il est également possible de définir des marges différentes en faisant par exemple `new Insets(0.0d, 10.0d, 10.0d, 10.0d)`, i.e. 0 pixel en haut, 10 pixels à droite, 10 pixel en bas, et 10 pixels à gauche. On pourrait par exemple ajouter les marges suivantes à notre application :

```Java
        VBox.setMargin( topControls, new Insets(10.0d) );
        VBox.setMargin( tblCustomers, new Insets(0.0d, 10.0d, 10.0d, 10.0d) );
        VBox.setMargin( bottomControls, new Insets(10.0d) );
```
![](img/vboxandhboxapp5.png)


Pour l'instant, nous n'intégrons pas les données dans notre tableau. Nous le ferons dans les TD suivants lorsque l'on s’entraînera à intégrer des évènements et à faire de liaison de données (*binding*) dans l'interface.


Vous allez maintenant mettre en pratique le processus de conception d'IHM illustré dans cet exemple par l'intermédiaire de plusieurs exercices.  Pensez à consulter le cours pour vous aider dans chaque question. A la fin de chaque question/exercice, vous ferez exécuter votre nouvelle version de l'application. Pensez aussi à committer (et pousser) régulièrement sur votre dépôt GitHub.


## Exercices 

Les exercices suivants sont issus du [support de TP](https://gitlabinfo.iutmontp.univ-montp2.fr/ihm/tp1) de [Sophie Nabitz](mailto:sophie.nabitz@univ-avignon.fr),
      [Cyrille Nadal](mailto:cyrille.nadal@umontpellier.fr),
      [Nathalie Palleja](mailto:nathalie.palleja@umontpellier.fr),
      [Xavier Palleja](mailto:xavier.palleja@umontpellier.fr),
      [Petru Valicov](mailto:petru.valicov@umontpellier.fr) (IUT de Montpellier-Sète, département informatique).

Ces exercices seront réalisés dans le dépôt Github Classroom du cours. Pour cela, vous devez aller sur Ametice dans la section `Liens classroom pour les TP`, et sélectionner le lien `TP1` correspondant à votre groupe. Une fois l'*assignment* (i.e. l'exercice) accepté, un nouveau dépôt sera ajouté à votre compte GitHub. Ensuite, vous devez ouvrir IntelliJ et créer un projet à partir de ce dépôt en copiant son URL (bouton `Getfrom VCS` dans la fenêtre accueil d'IntelliJ). Au moment du clonage du projet, on vous demandera d'autoriser IntelliJ à accéder au dépôt GitHub. Si vous avez une erreur à ce niveau, il vous faudra passer par la création d'un jeton d'accès (`Personal access token`) sur Github et recopier ce jeton (un code) à l'endroit demandé dans IntelliJ.

### Exercice 1 - Trouver les bons conteneurs

 Écrivez une application JavaFX (dans le même package ou dans un autre projet) qui affiche une fenêtre similaire à celle-ci :

![](img/exo1/FenetrePrincipale.png)

Cette application devra être développée directement à partir des classes du paquetage (*package*) `fr.amu.iut.exercice1` disponible dans le dépôt Github Classroom du cours, i.e. dans le répertoire **src/main/java/fr/amu.iut.exercice1** cloné précédemment.

**Quelques indications :**
* Réfléchissez au type du conteneur que vous allez utiliser pour la racine de votre graphe de scène. Pensez à consulter les sous-classes de la classe [`Pane`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/layout/Pane.html).
* La barre de menus ([`MenuBar`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/MenuBar.html)) est composé de 3 menus : _File_, _Edit_ et _Help_. Les menus _File_ et _Edit_ contiennent chacun les sous-items suivants :
    * _File_ -> _New_, _Open_, _Save_ et _Close_
    * _Edit_ -> _Cut_, _Copy_ et _Paste_.

  Le menu _Help_ ne contient aucun autre sous-item.
* Pour obtenir des barres séparant les différentes parties de la scène, vous pouvez utiliser des [`Separator`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/Separator.html) ou des [`SeparatorMenuItem`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/SeparatorMenuItem.html).
* Le formulaire du milieu est un [`GridPane`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/layout/GridPane.html) composé de 3 lignes et 2 colonnes.
* Aucune réaction aux événements n'est attendue pour cet exercice.

### Exercice 2 - Conteneur GridPane

Reproduisez la fenêtre suivante en utilisant un conteneur `GridPane`:

![](img/Exo2.png)

Cette fenêtre est déplaçable, mais pas redimensionnable. Pour cela, vous pouvez exécuter `initStyle(StageStyle.UTILITY)` sur l'objet `Stage`. Sur certains systèmes d'exploitation ce style n'est pas supporté et la fenêtre peut ne pas s'afficher... Dans ce cas, vous pouvez exécuter `setResizable(false)` sur l'objet `Stage`.

Les 9 éléments sont des `Label`, dont vous aurez défini les "graphiques" en utilisant les 3 fichiers fournis dans [cette archive](img/exo2/exercice2.zip). Pour cela, déclarez un objet de la classe [`ImageView`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/image/ImageView.html) et utilisez la méthode `setGraphic(Node n)` sur le label. Notez que cette méthode reçoit un objet de type `Node` de JavaFX en paramètre et [`ImageView`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/image/ImageView.html) est une de ses nombreuses sous-classes. Pour créer un objet `ImageView`, il faut lui passer en paramètre du constructeur la chaîne de caractères correspondante au chemin relatif vers la ressource, comme par exemple :

```java
ImageView image = new ImageView("exercice2/Croix.png");
label.setGraphic(image);
```

L'image à afficher sera choisie aléatoirement : pour cela, vous pouvez faire générer un nombre entre 0 et 2, à partir d'un objet `Random`:
```java
Random random = new Random();
int nombre = random.nextInt(3);
```

