---
layout: post
title: "TD/TP 2 -- Gestion des évènements"
categories: jekyll update

mathjax: true
---


Dans cette partie, nous allons nous initier à la gestion des évènements en JavaFX. Afin d'organiser un peu nos fichiers sources, nous allons créer un nouveau package `com.example.partie2` dans notre projet "exemple". Comme dans la partie précédente, n'oubliez pas de vérifier dans `module-info.java` que la référence à ce nouveau package est bien exportée.

## 2.1 - Traitement d'un premier évènement simple 

Pour commencer simplement, nous allons construire une petite application graphique qui correspond au *"Bonjour à tous"* traditionnel (sans utiliser FXML). Son interface suivra la maquette (mockup) suivante :

![](img/bonjourFenetreMockup.png)


Nous allons créer une nouvelle classe `BonjourFenetre` dans notre projet à partir du code fournit ci-dessous. Pour cela, il suffit  simplement de sélectionner le package `com.example.partie2` et de copier/coller le code.

```java
import javafx.application.Application;
import javafx.stage.Stage;

public class BonjourFenetre extends Application {

    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) throws Exception {
        primaryStage.setTitle("Hello application");
        primaryStage.show();
    }
}
```

Nous modifions maintenant la taille de la fenêtre en fixant la largeur et la hauteur à 400 grâce au code suivant :

```java
        primaryStage.setWidth(400);
        primaryStage.setHeight(400);
```

Dans la méthode `start(Stage primaryStage)`, nous créons  ensuite un conteneur [`VBox`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/layout/VBox.html). Nous centrons ses éléments en utilisant sa méthode `setAlignment(Pos p)`. Nous ajoutons aussi un [`Label`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/Label.html) dont le texte actuel est *"Bonjour à tous !"*. Pour afficher ces éléments, il faut créer une [scène](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/Scene.html), y intégrer notre conteneur `VBox` et l'ajouter à la fenêtre principale (objet de la classe `Stage`). Le code de la méthode `start` devient :

```java
    @Override
    public void start(Stage primaryStage) throws Exception {

        // Création d'un conteneur VBox avec ses éléments centrés
        VBox vbox = new VBox();
        vbox.setAlignment( Pos.CENTER );

        // Création et ajout du label au conteneur
        Label helloLabel = new Label("Bonjour à tous !"); 
        vbox.getChildren().add( helloLabel );

        // Création de la scene
        Scene scene = new Scene( vbox );

        // Ajout de la scene à la fenêtre 
        primaryStage.setScene( scene );

        primaryStage.setTitle("Hello application");
        primaryStage.setWidth(400);
        primaryStage.setHeight(400);
        primaryStage.show();
    }  
```

![](img/bonjourFenetre.png)

Nous ajoutons maintenant un [`TextField`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/TextField) qui permettra de saisir un nom. Ce champs aura une largeur maximale (`setMaxWidth`) à 180 pixels, et une police (*font*) Courier 12 (`setFont( Font.font("Courier", FontWeight.NORMAL, 12) )`). Puis, nous ajoutez un [`Button`](https://openjfx.io/javadoc/18/javafx.controls/javafx/scene/control/Button.html) construit avec le texte *"Dire bonjour"*.


```java
        // Ajout d'un champ de saisi de texte de taille 180 pixels
        TextField nameField = new TextField("Veuillez saisir un nom");
        nameField.setMaxWidth(180.0d);
        nameField.setFont( Font.font("Courier", FontWeight.NORMAL, 12) );
        vbox.getChildren().add( nameField );

        // Ajout d'un bouton avec du texte
        Button button = new Button("Dire bonjour");
        vbox.getChildren().add( button );
```

![](img/bonjourFenetre2.png)


A noter que nous pouvons aussi remplacer le texte associé au bouton par  [cette image](img/exo3/silver_button.png). Pour cela, nous allons utiliser les classes [`Image`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/scene/image/Image.html) et [`ImageView`](https://openjfx.io/javadoc/18/javafx.graphics/javafx/scene/image/ImageView.html). Nous allons tout d'abord utiliser un des constructeurs de la classe `Image` pour charger un fichier image stocké sur le disque.  L'objet  `Image` ainsi créé est ensuite intégré au bouton grâce à sa méthode `setGraphic(Node n)`. Avant d'exécuter le code, il faut copier l'image en question dans le répertoire `partie2` dans le répertoire `resources/com/example/`. Pour que cette image soit trouvée parmi les ressources du package, il faut également utiliser la méthode [`getResource()`](https://docs.oracle.com/javase/8/docs/technotes/guides/lang/resources.html) pour avoir le chemin vers le fichier. 

```java
        // Ajout d'un bouton avec du texte
        Button button = new Button();
        vbox.getChildren().add( button );

        // Chargement de l'image
        Image image = new Image( BonjourFenetre.class.getResource("silver_button.png").toString() );

        // Création d'un composant avec l'image peinte à l'intérieur  
        ImageView iv = new ImageView();
        iv.setImage(image);

        // Intégration de l'image dans le bouton
        button.setGraphic( iv );
```

Nous allons maintenant ajouter un [gestionnaire d'événement](https://openjfx.io/javadoc/18/javafx.base/javafx/event/EventHandler.html) sur ce bouton afin d'intercepter l'action de clic et lui associer une réponse spécifique.

Dans un premier temps, nous l'ajoutons sous la forme d'une expression lambda `actionEvent -> { ... }`.  L'action de clic sur ce bouton aura pour effet de transformer le texte du `Label` en *"Bonjour à toi, César"*, si le texte *César* a été saisi dans le `TextField`. Pour cela, nous allons utiliser la méthode [`addEventHandler`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/scene/Node.html#addEventHandler(javafx.event.EventType,javafx.event.EventHandler)) de `Button` et lui associer la lambda expression sur l'évènement [`MOUSE_CLICKED`](https://openjfx.io/javadoc/20/javafx.graphics/javafx/scene/input/MouseEvent.html#MOUSE_CLICKED). Au final, nous intégrons donc le code suivant dans notre méthode `start()`.

```java
        // Changement du texte après un clic sur le bouton
        button.addEventHandler(MouseEvent.MOUSE_CLICKED, actionEvent -> { 
            helloLabel.setText( "Bonjour à toi, "+nameField.getText() );
        });
```

Lorsque vous exécutez l'application, vous constatez que l'évènement est bien intercepté et traité.

![](img/BonjourFenetreClic.png)


Le code précédent représente une première approche pour intégrer la gestion du clic sur le bouton dans notre fenêtre, mais ce n'est pas la seule.

Une autre option est de stocker l'expression lambda précédente en attribut de type `EventHandler<Event>` dans la classe `BonjourFenetre`.  Il faut supprimer le code ci-dessus, déclarer les composants comme attributs de la classe, tout comme les gestionnaires d'évènements. Attention à ce niveau à bien utiliser ces attributs dans le code de la méthode `start` et non à redéclarer des variables locales avec des noms identiques (utiliser `this` pour cela). Au final, on obtient le code suivant :

```java
public class BonjourFenetre extends Application {

    // Label affichant le message de bienvenue
    private Label helloLabel;

    // Champ de saisi du nom de l'utilisateur
    private TextField nameField;

    // Bouton déclenchant la mise à jour du texte
    private Button button;

    // Gestionnaire d'évènements appelé lors du clic sur le bouton
    EventHandler<MouseEvent> buttonClickHandler = actionEvent -> { 
            helloLabel.setText( "Bonjour à toi, "+nameField.getText() );
        };


    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) throws Exception {

         // Création d'un conteneur VBox avec ses éléments centrés
        VBox vbox = new VBox();
        vbox.setAlignment( Pos.CENTER );

        // Création et ajout du label au conteneur
        this.helloLabel = new Label("Bonjour à tous !"); 
        vbox.getChildren().add( helloLabel );      

        ...

        // Changement du texte après un clic sur le bouton
        button.addEventHandler(MouseEvent.MOUSE_CLICKED, buttonClickHandler);

        ...
    }
}
```

Si vous exécutez ce code, vous verrez que, même si son organisation est différente, le résultat reste identique.

Une autre option pour mieux dissocier composants et gestionnaires d'évènements est de laisser les composants en attributs mais de déclarer les gestionnaires d'évènements comme des méthodes de la classe.

```java
public class BonjourFenetre extends Application {

    // Label affichant le message de bienvenue
    private Label helloLabel;

    // Champ de saisi du nom de l'utilisateur
    private TextField nameField;

    // Bouton déclenchant la mise à jour du texte
    private Button button;


    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) throws Exception {

         // Création d'un conteneur VBox avec ses éléments centrés
        VBox vbox = new VBox();
        vbox.setAlignment( Pos.CENTER );

        // Création et ajout du label au conteneur
        this.helloLabel = new Label("Bonjour à tous !"); 
        vbox.getChildren().add( helloLabel );      

        ...

        // Changement du texte après un clic sur le bouton
        button.addEventHandler(MouseEvent.MOUSE_CLICKED, actionEvent -> handleButonClick(actionEvent) );

        ...
    }

    // Actions effectuées lors du clic sur le bouton
    private void handleButonClick(Event event) {
        helloLabel.setText( "Bonjour à toi, "+nameField.getText() );
    }
}
```

Encore une fois, le résultat à l'exécution reste identique mais l'organisation du code a été sensiblement améliorée.

La gestion des évènements associés à un composant peut être la même dans plusieurs fenêtres d'une même application. Les approches précédentes impliqueraient une duplication du code, ce qui est à éviter si ces fenêtres évoluent systématiquement de la même façon. Une autre option serait donc de factoriser ce code en l'externalisant sous la forme d'une classe. On aurait ainsi dans notre projet une nouvelle classe `ButtonClickHandler` héritant de  `EventHandler<ActionEvent>` dont le code serait le suivant :

```java
public class ButtonClickHandler implements EventHandler<Event> {

    private Label helloLabel;
    private TextField nameField;

    public ButtonClickHandler(Label helloLabel, TextField nameField) {
        this.helloLabel = helloLabel;
        this.nameField = nameField;
    }

    @Override
    public void handle(Event event) {
        // Utiliser le Label récupéré
        helloLabel.setText( "Bonjour à toi, "+nameField.getText() );
    }
}
```

Cette classe serait importée dans notre classe `BonjourFenetre` et utilisée pour instancier le gestionnaire d'évènements du bouton de la manière suivante:

```java
        // Changement du texte après un clic sur le bouton
        button.addEventHandler(MouseEvent.MOUSE_CLICKED,  new ButtonClickHandler(helloLabel, nameField) );
```

A noter qu'il est aussi possible de déclarer `ButtonClickHandler` en classe interne de `BonjourFenetre` si tous les composants partageant ce comportement sont uniquement dans cette classe.

Pour finir, nous allons faire en sorte que le message soit aussi changé lorsque l'utilisateur tape sur la touche _Entrée_ du clavier lors de la saisie du `TextField`. Il suffit pour cela d'utiliser la méthode de convenance [`setOnAction(...)`](https://openjfx.io/javadoc/20/javafx.controls/javafx/scene/control/TextField.html#setOnAction(javafx.event.EventHandler)) sur le champ de saisi.


```java
        // Ajout d'un champ de saisi de texte de taille 180 pixels
        TextField nameField = new TextField("Veuillez saisir un nom");
        nameField.setMaxWidth(180.0d);
        nameField.setFont( Font.font("Courier", FontWeight.NORMAL, 12) );
        
        nameField.setOnAction( actionEvent -> handleButonClick(actionEvent) );
        
        vbox.getChildren().add( nameField );
```


   Vous allez maintenant mettre en pratique la gestion des évènements JavaFX au travers de différents exercices.





## Exercices

Les exercices suivants sont issus du [support de TP](https://gitlabinfo.iutmontp.univ-montp2.fr/ihm/tp1) de [Sophie Nabitz](mailto:sophie.nabitz@univ-avignon.fr),
      [Cyrille Nadal](mailto:cyrille.nadal@umontpellier.fr),
      [Nathalie Palleja](mailto:nathalie.palleja@umontpellier.fr),
      [Xavier Palleja](mailto:xavier.palleja@umontpellier.fr),
      [Petru Valicov](mailto:petru.valicov@umontpellier.fr) (IUT de Montpellier-Sète, département informatique).

Ils sont à faire à partir du même dépôt issu de la Github Classroom utilisée précédemment.
   
### Exercice 4 - Conteneurs BorderPane et HBox

Vous devez écrire une application dont la fenêtre initiale est la suivante :

![](img/Exo4First.png)

Un clic sur un des boutons (ici 3 clics sur le vert) donnera le résultat suivant :

![](img/Exo4Second.png)

Vous utiliserez pour cela un conteneur `BorderPane` (taille 400 sur 200), dont l'élément du haut est un `Label` centré, celui du milieu un `Pane` et celui du bas une `HBox` de `Button`. Le changement de couleur d'un panneau se fait en utilisant la méthode `setStyle(String s)`.

### Exercice 5 - Jeu de type PacMan

**Objectif :** afficher un pacman, un fantôme, les faire se déplacer au clavier, détecter les éventuelles collisions.

Dans un jeu PacMan les personnages (classe `Personnage`) ont tous un corps sous forme de cercle et une direction de déplacement.

- Un objet de type `Pacman` sera un `Personnage` qui comporte _une bouche_. Celle-ci est orientée vers la droite, gauche, bas, haut en fonction de sa direction.

- Un objet de type `Fantome` sera un `Personnage` qui comporte _un bas de corps_, _un œil gauche_, _une rétine gauche_, _un œil droit_ et _une rétine droite_, les rétines seront orientées en fonction de sa direction.

- Les objets de type `Fantome` et `Pacman` pourront se déplacer dans les 4 directions du plan de jeu avec des touches différentes du clavier, ils ne peuvent _pas sortir du plan de jeu_.

- Il sera possible de **détecter la collision** entre deux personnages (ou éléments du jeu) se touchant.

- La classe `JeuMain` est chargée de lancer le jeu dans une fenêtre 640*480, chaque personnage est pour l’instant stocké dans un carré de 20 pixels de côté.

**Diagramme de classes du code qui vous est fourni :**

<img src="img/exo5/pacmandiagclasses.png" width="700"/>


Le [_code fourni_](img/exo5/exercice5.zip) permet d'afficher le plan de jeu, un pacman qui se dirige vers la **droite** ou vers la **gauche**, un fantôme **qui ne bouge pas** pour l’instant.



**État initial :**

![](img/exo5/jeuinitial.jpg)

**Le pacman s’est déplacé à droite**

![](img/exo5/pacman2.jpg)

**Puis repart vers la gauche**

![](img/exo5/pacman3.jpg)

1 - **Complétez** la classe `Pacman` afin que soient pris en compte les déplacements bas et haut. Les touches de déplacement seront les touches du clavier UP, DOWN, LEFT et RIGHT

![](img/exo5/pacmanbas.jpg) ![](img/exo5/pacmanhaut.jpg)

2 - **Complétez** la classe `Fantome` afin que soient pris en compte les déplacements haut, bas, gauche et droite. Les touches de déplacement seront Z, S, Q et D. Les yeux du fantôme suivront la direction comme cela :

![](img/exo5/fantomes.jpg)

3 - Pour l’instant, la collision affiche un message dans la console. Trouvez un moyen de **stopper le jeu** lorsqu'une collision se produit.

4 - **Ajoutez des obstacles infranchissables** (murs, etc), faites en sorte de placer le pacman et le fantôme aux extrémités du jeu.<br>
**Algo à mettre en place**

- Créez un ou des **obstacles** (nouvelle classe `Obstacle` héritant de `Rectangle`) et stockez-les dans `JeuMain` (Arraylist static...)
- Lors du déplacement d'un `Personnage` (haut, bas, droite ou gauche), _sauvegardez_ sa position (`x` et `y`), faites le _déplacement voulu_, _détectez_ une collision avec un obstacle (détection de collision avec un des obstacles), si collision _détectée_ **repositionnez** le personnage avec les coordonnées sauvegardées.


![](img/exo5/pacmanObstacle.jpg)

5 - **Imaginez** une suite, un mode de jeu rapide par exemple le jeu se lance, le gagnant sera soit le pacman s’il atteint le fantôme en moins de 10 secondes, soit le fantôme s’il réussit à échapper au pacman au bout des 10 secondes…


### Exercice 6 - Jeu du pendu

Dans cet exercice, vous allez réaliser une interface graphique du jeu du pendu, jeu qui consiste à découvrir un mot caché en proposant des lettres successivement, jusqu'à l'avoir trouvé, mais dans la limite d'un certain nombre de vies (le joueur est alors "pendu"...).
<br/>Pour mettre en place le jeu, on vous fournit une classe [`Dico`](img/exo6/Dico.java), qui correspond à un petit dictionnaire contenant donc une liste de mots. On peut l'utiliser grâce aux deux méthodes publiques suivantes :
- `String getMot()` qui retourne un mot, extrait de la liste de mots de façon aléatoire à chaque appel,
- `ArrayList<Integer> getPositions(char lettre, String mot)` qui retourne la liste des positions d'un caractère dans une chaîne de caractères. Si ce caractère ne se trouve pas dans la chaîne de caractères, le résultat est une liste vide.

Vous allez construire cette interface de façon incrémentale, en suivant par exemple la démarche proposée un peu plus bas.
Vous pouvez vous inspirer d'une IHM qui ressemblerait à cela :

![](img/Exo6Debut.jpg)

Lorsque l'utilisateur a commencé à proposer des lettres, l'interface deviendra :

![](img/Exo6EnCours.jpg)

Remarquez que les caractères proposés par le joueur qui ne se trouvent pas dans le mot à découvrir ne sont plus actifs. Par contre, ceux en faisant partie peuvent rester actifs.

#### Démarche :
Commencez par afficher :
- un `TextField` permettant à l'utilisateur de proposer une lettre,
- un `Label` qui rappelle le nombre de vies,
- et une représentation cachée du mot à découvrir.
  <br/>A ce niveau, le jeu devrait être effectif.

Remplacez maintenant le `TextField` par un ensemble de touches correspondant à des lettres. Lorsque le nombre de vies est 0 ou que le joueur a trouvé le mot, les touches ne devraient plus avoir d'effet (sans pour autant être devenues inactives).
<br/>Affichez ensuite les images qui correspondent au nombre de vies, en utilisant les fichiers  dans [cette archive](img/exo6/exercice6.zip).
<br/>Enfin, ajoutez le bouton qui permet de recommencer une partie.

