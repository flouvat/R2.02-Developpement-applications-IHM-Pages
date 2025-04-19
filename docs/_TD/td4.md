---
layout: post
title: "TD/TP 4 -- Propriétés et Binding"
categories: jekyll update

mathjax: true
---


Le but de ce TP est de vous familiariser avec la notion de [**Propriété**](https://wiki.openjdk.java.net/display/OpenJFX/JavaFX+Property+Architecture) en JavaFX. Pour un objet donné, les propriétés définissent son état accessible en lecture/écriture, et qui de plus, peut être _observé_ lors de son changement.

La notion sœur de propriété et la notion de **binding**. Il s'agit d'une valeur **X** qui est _liée_ à un certain nombre de valeurs observables **x<sub>1</sub>, x<sub>2</sub>, x<sub>3</sub>...** : si un changement d'une des valeurs observables a lieu, alors **X** est automatiquement recalculé. Toutes les classes de propriétés de JavaFX permettent la création des bindings et l'ajout des écouteurs de changement (**listeners**). Pensez à consulter le cours avant de poursuivre.


## Exemple 1 - Calculer automatiquement la somme de deux nombres

L'objectif de cet exemple est d'illustrer le principe du *binding* pour automatiser une opération (une addition) sur deux champs de saisie, sans avoir gérer des évènements.

![](img/exemple1-binding.png)

Nous allons commencer par créer un nouveau package `fr.amu.iut.exemple1` dans le projet (répertoire `java`) et copier le code suivant:

```java
package fr.amu.iut.exemple1;

import javafx.fxml.FXMLLoader;
import javafx.scene.layout.Pane;
import javafx.scene.layout.VBox;
import java.io.IOException;

public class CalculatorView extends Pane {

    public CalculatorView() throws IOException {

        // chargement des composants de la fenêtre
        FXMLLoader loader = new FXMLLoader(getClass().getResource("CalculatorView.fxml"));
        VBox root = loader.load();

        // Ajout des composants dans la fenêtre
        this.getChildren().add(root);
    }
}
```

Cette classe `CalculatorView` représente une fenêtre de l'application, construite à partir d'un fichier *FXML*. Jusqu'à présent, ce code était dans la méthode ``start`` de la classe représentant l'application. Dans cet exemple, le code a été organisé différemment. La fenêtre (encore appelée *"view"* ou vue) a été isolée dans une classe à part pour en faciliter la modification. Il s'agit d'une simple réorganisation du code visant à avoir une meilleure architecture, et ainsi pouvoir facilement ajouter et modifier des fenêtres (i.e. des *"views"* ou vues). A noter que cette classe hérite d'un conteneur graphique (ici un `Pane`) afin de pouvoir être ajoutée ensuite dans notre `Scene`. Le code suivant montre le code de la classe application associée.


```java
package fr.amu.iut.exemple1;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class CalculatorApp extends Application {

    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) throws Exception {
        CalculatorView view = new CalculatorView();

        Scene scene = new Scene(view);

        primaryStage.setTitle("Calculator App");
        primaryStage.setScene( scene );
        primaryStage.setWidth( 250 );
        primaryStage.setHeight( 160 );
        primaryStage.show();
    }
}
```



Nous allons maintenant créer le fichier *FXML* associé à cette fenêtre. Pour cela, nous allons créer un répertoire `fr/amu/iut/exemple1` dans le répertoire `resources`, y créer un fichier `CalculatorView.fxml` et copier le code suivant.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.control.*?>
<?import javafx.scene.layout.*?>

<VBox xmlns="http://javafx.com/javafx"
      xmlns:fx="http://javafx.com/fxml"
      fx:controller="fr.amu.iut.exemple1.CalculatorViewController"
      prefHeight="400.0" prefWidth="600.0"
      spacing="10" >

    <Label text="Addition" />
    <TextField fx:id="num1Field" promptText="Nombre 1" />
    <TextField fx:id="num2Field" promptText="Nombre 2" />
    <Label fx:id="resultLabel" />
</VBox>
```

Comme le montre ce code, notre fenêtre est simplement composé de champs de saisie et de deux étiquettes, empilés dans une `VBox`. On remarque aussi que ce fichier est lié à un contrôleur Java `CalculatorViewController` chargé de gérer les actions sur la fenêtre. 

```java
package fr.amu.iut.exemple1;

import javafx.beans.binding.Bindings;
import javafx.beans.binding.DoubleBinding;
import javafx.beans.property.DoubleProperty;
import javafx.beans.property.SimpleDoubleProperty;
import javafx.beans.property.StringProperty;
import javafx.fxml.FXML;
import javafx.scene.control.Label;
import javafx.scene.control.TextField;
import javafx.util.StringConverter;
import javafx.util.converter.NumberStringConverter;

public class CalculatorViewController {
    @FXML
    private Label resultLabel;
    @FXML
    private TextField num2Field;
    @FXML
    private TextField num1Field;

    public void initialize() {
        // récupération des propriétés associées aux champs de saisie
        StringProperty num1Str = num1Field.textProperty();
        StringProperty num2Str = num2Field.textProperty();

        // conversion str -> double et liaison avec les textes saisis
        StringConverter<Number> converter = new NumberStringConverter();
        DoubleProperty num1 = new SimpleDoubleProperty();
        DoubleProperty num2 = new SimpleDoubleProperty();
        Bindings.bindBidirectional(num1Str,num1,converter);
        Bindings.bindBidirectional(num2Str,num2,converter);

        // Calcul de la somme et liaison avec le label
        DoubleBinding sum = num1.add(num2);
        resultLabel.textProperty().bind(Bindings.format("Résultat : %.2f", sum));
    }
}
```
Cette classe sera créée, et ses attributs initialisés, lors de l'exécution par la méthode `load()` de `FXMLLoader`. Elle a pour rôle de lire les valeurs saisies par l'utilisateur (du texte donc), de les convertir en double et d'en calculer la somme. 

Si  nous avions utilisé la même approche que dans les exercices précédents, il aurait fallu pour faire cela ajouter un gestionnaire d'événements sur chacun des champs de saisie. Ce gestionnaire aurait été déclenché à chaque modification d'un des champs de saisie par l'utilisateur. Il aurait récupéré les valeurs des deux champs de saisie, les aurait convertis en double, en aurait fait la somme, et pour finir aurait écrit le résultat dans l'étiquette de la fenêtre. Dans cet exemple, nous n'allons pas procéder de cette façon et exploiter les *"binding"* (liaison de données) pour produire un code beaucoup plus simple se passant de gestionnaire d'événements. 

Comme le montre le code ci-dessus, ces bindings sont définis dans la méthode `intialize()`, qui est automatiquement exécutée une fois le contrôleur Java `CalculatorViewController` créé par `FXMLLoader`. Cette méthode commence par récupérer les propriétés associées aux champs de saisie (car seules des propriétés peuvent être liées par du binding). Ces `StringProperty` sont ensuite reliées à des `DoubleProperty`, et sont au passage converties en double grâce à la classe `StringConverter`. Ces `DoubleProperty` sont ensuite associées à une addition dont le résultat est relié à l'étiquette chargée d'afficher le résultat (objet `resultLabel`). Une fois ces liens définis, les valeurs seront automatiquement converties, additionnées et le résultat affiché à chaque modification du texte des champs de saisie. Il n'est pas nécessaire d'avoir de gestionnaires événements pour faire cela.




## Exercices


### Exercice 11 - Premières propriétés et bindings simples

On reprend l'exercice 2 qui change la couleur d'un panneau et affiche combien de fois un bouton a été cliqué. Mais cette fois-ci nous allons complexifier un peu le comportement des éléments de la fenêtre. Désormais il faudrait que les clics changent également le texte et la couleur d'un label situé tout en bas de la fenêtre.

![](img/Exo11-a.png) ![](img/Exo11-b.png) ![](img/Exo11-c.png)

1. Associez à chacun des trois boutons, 3 événements avec la méthode de convenance `setOnAction(event -> ...)`.

2. Ajoutez à la classe un attribut `nbFois` de classe `IntegerProperty` et instanciez-le dans un constructeur en utilisant la classe concrète `SimpleIntegerProperty`. Cette **propriété** devra changer dynamiquement en fonction du bouton cliqué et du nombre de clics. Changez le code de vos gestionnaires d'événement de façon à utiliser la propriété `nbFois` lors de l'affectation du texte du label `texteDuHaut`.

3. Ajoutez maintenant un attribut `message` de classe `StringProperty`, instanciez-le dans le constructeur en utilisant `SimpleStringProperty`. Dans les gestionnaires d'événement, ce message sera affecté au texte du `Button`.

4. Transformez l'affectation du texte du label `texteDuHaut` en un **binding** sur la propriété `Text` du label et déplacez ce nouveau code à l'extérieur du gestionnaire d'événement. Vous utiliserez la méthode statique `concat(...)` de la classe [`Bindings`](https://openjfx.io/javadoc/18/javafx.base/javafx/beans/binding/Bindings.html) (pour concaténer un nombre variable de chaînes de caractères), et la méthode `asString()` (pour lier avec une `String` correspondant à une expression numérique). Pour l'instant, ne vous préoccupez pas de l'état initial du `Label`.

5. De même, déclarez un attribut `couleurPanneau` de classe `StringProperty`. Vous l'instancierez comme ceci : `couleurPanneau = new SimpleStringProperty("#000000");`.
   Mettez à jour l'objet `couleurPanneau` dans le gestionnaire d'événement en utilisant (uniquement) la valeur de la couleur correspondante au bouton choisi, et enfin, ajoutez un binding sur la propriété `Style` du panneau.

6. Extrayez les deux instructions de binding dans une méthode privée `createBindings()`. Dans cette méthode, déclarez et instanciez une variable `pasEncoreDeClic` de type `BooleanProperty`. Liez cette variable de façon à ce qu'elle change lorsque `nbFois` n'est plus égal à 0. Pour cela, retrouvez la version appropriée de la méthode `equal()` de `Bindings`.<br/>Transformez ensuite le binding sur le label `texteDuHaut` afin de gérer sa valeur initiale en utilisant `Bindings.when`.

7. **Sans toucher au code** des gestionnaires d'événement de vos boutons, faites en sorte que le label `texteDuBas` affiche le texte en fonction de `message` et de `couleurPanneau`. Par exemple, si le bouton *Rouge* a été cliqué, le texte de `texteDuBas` devrait être colorié en rouge et afficher "*Le Rouge est une jolie couleur !*".


### Exercice 12 - Écouteur de changement

On continue de travailler sur la palette, mais à partir d'une organisation du code un peu différente. Nous allons tout d'abord créer des boutons personnalisés (c.f. `CustomButton.java`) et stocker à l'intérieur de ces derniers un compteur de clics et une couleur. Ensuite, nous allons mettre en place un gestionnaire d'évènements génériques (i.e. indépendant du bouton cliqué). Ce gestionnaire incrémentera le compteur interne au bouton après chaque clique dessus. Puis, nous attacherons un _listener_ sur le compteur afin de mettre à jours l'interface graphique lorsque ce dernier est incrémenté. Dans cet exercice, nous n'utiliserons pas directement la méthode `bind` mais passerons par un gestionnaire d'évènements et un _listener_ pour lier les données (dans `Palette.java`).

 
1. Ajoutez dans la classe `CustomButton` les 3 méthodes usuelles pour la propriété `nbClics` (i.e. les _getters_ et _setters_). IntelliJ vous permet de générer automatiquement ce code en utilisant la fonctionnalité `Generate...` (`Alt+Insérer` sur l'attribut > `Create getters and setters for nbClics`). Ajoutez aussi la méthode `getCouleur()`.

2. Complétez le code du gestionnaire d'événements `gestionnaireEvennement` associé aux boutons afin d'incrémenter le compteur interne au bouton après chaque clic  (dans la méthode `start` de la classe `Palette`). 

3. Implémentez dans la méthode `start` le _listener_  `nbClicsListener` de façon à actualiser le label `texteDuHaut` et le style du panneau après chaque changement sur les compteur (comme dans l'exercice précédent). Ce _listener_ sera associé aux compteurs `nbClics` des 3 boutons customisés grâce à la méthode `addListener`.

    Ce _listener_ `nbClicsListener`  est de type [`ChangeListener<Number>`](https://openjfx.io/javadoc/18/javafx.base/javafx/beans/value/ChangeListener.html). Il s'agit d'une interface fonctionnelle de JavaFX.  Sa seule fonction abstraite est `changed(ObservableValue<? extends T> observable, T oldValue, T newValue)`. Le paramètre `observable` correspond à la source à laquelle l'écouteur sera associée (i.e. l'objet `IntegerProperty nbClics`), les deux autres paramètres étant la valeur actuelle de la source et la nouvelle valeur à utiliser.

    Pour récupérer le bouton cliqué dans le _listener_, vous utiliserez l'attribut `sourceOfEvent` défini dans la classe `Palette`. Cet attribut permet de récupérer un pointeur vers le dernier bouton cliqué, ce qui nous permet de récupérer ses informations (texte et couleur) et de les utiliser pour changer l'affichage de la fenêtre.


4. Modifiez l'implémentation de la méthode `changed(...)` de `nbClicsListener` afin de modifier également le label `texteDuBas` (avec le même effet que dans l'exercice précédent).



### Exercice 13 - Liste observable

Dans le fichier `MainPersonnes.java`, on va travailler avec une liste de personnes `lesPersonnes` qui peut évoluer, par ajout, suppression et modification d'éléments. Observez la déclaration et l'instanciation de cette liste. Il s'agit d'un objet `Observable`, ce qui signifie qu'on pourra y attacher des écouteurs, ou la lier à d'autres propriétés.

1. Dans cette question, vous allez compléter la fonction principale `main(String args[])` de la classe `MainPersonnes`, de façon à définir un écouteur de changement (un "_listener_") sur la liste `lesPersonnes`. Ce "_listener_" sera affecté à la variable `unChangementListener` de type `ListChangeListener`. Pour l'instant, le code se contentera d'afficher  après chaque ajout le nom de la personne ajoutée (dans le terminal). Invoquez la méthode `question1()` pour tester votre code. Pour cela, vous pouvez vous aider de l'exemple fournit dans la documentation de [ListChangeListener.Change](https://openjfx.io/javadoc/18/javafx.base/javafx/collections/ListChangeListener.Change.html). 

    Comme évoqué ci-dessus, la classe de ce _listener_ est  [ListChangeListener](https://openjfx.io/javadoc/18/javafx.base/javafx/collections/ListChangeListener). Elle peut être instanciée via une lambda expression ou une classe anonyme (comme pour les gestionnaires d'évènements). Il s'agit d'une interface fonctionnelle dont la méthode à implémenter n'a qu'un argument. Cet argument, de type [ListChangeListener.Change](https://openjfx.io/javadoc/18/javafx.base/javafx/collections/ListChangeListener.Change.html), représente un rapport des changements faits sur la liste observée. En fait, afin d'optimiser les performances, le _listener_ n'est pas déclenché immédiatement après chaque changement, mais après une série de modifications. 
    
    Pour pouvoir parcourir ces changements, il faut exécuter la méthode `next()` sur cet argument, et ensuite, suivant le type de changement, appliquer les changements voulus (le code de réaction). **Attention**, les traitements effectués dans ce _listener_ ne doivent pas modifier la liste qu'il "écoute". 
    
    A noter que dans cette première question, vous devrez uniquement récupérer le premier changement, vérifier qu'il s'agit d'un ajout, récupérer l'objet ajouté et afficher le nom de la personne. Pour savoir quel type de changement a eu lieu, vous pouvez utiliser des méthodes booléennes comme `wasAdded()`, `wasRemoved()`, `wasUpdated()` etc. Pour récupérer la liste des objets ajoutés, vous pourrez utiliser la méthode [`getAddedSubList()`](https://openjfx.io/javadoc/18/javafx.base/javafx/collections/ListChangeListener.Change.html#getAddedSubList()). 

    

   
2. On continue avec la suppression d'une personne de la liste. Pour cela, vous ajouterez au code du _listener_ précédent un test afin de vérifier si la modification faite est une suppression (grâce à la méthode `wasRemoved()`). Ensuite, vous utiliserez la méthode `getRemoved()` pour récupérer le premier élément supprimé, et vous afficherez le nom de la personne supprimée dans le terminal. Testez en remplaçant le précédent appel par celui de la méthode `question2()`.

3. Écrivez maintenant un code qui devrait réagir à une modification de l'âge en écrivant un texte du type "_Pierre a maintenant ... ans_", et testez-le en appelant maintenant `question3()`. Vous devriez constater que l'écouteur ne réagit pas. En effet, en l'état, il ne permet d'écouter les changements apporté aux attributs internes à l'objet.

4. Transformez maintenant l'instanciation de la liste `lesPersonnes` par `FXCollections.observableArrayList(personne -> new Observable[] {personne.ageProperty()});`. Cette instruction permet d'exprimer que l'on souhaite écouter les changements sur la propriété `age` de la classe `Personne`. Pour que ce code fonctionne, il faut aussi modifier le code de la classe `Personne` afin de transformer l'attribut `age` en propriété écoutable (i.e. `IntegerProperty`). Pour tester votre code, vous pouvez à nouveau utiliser la méthode `question3()`.

5. Créez un second écouteur (par exemple `plusieursChangementsListener`) et affectez-le à la liste `lesPersonnes` à la place du précédent. Cet écouteur gérera plusieurs changements à la fois. Contrairement, au premier, il devra donc parcourir tous les changements enregistrés par JavaFX. Les traitements effectués sur chaque changement seront quant à eux identiques au premier _listener_. Testez avec la méthode `question5()`. Vous pouvez constater que le listener est déclenché pour une série de changements et non après chaque changement (par exemple en ajoutant un affichage en fin du code du listener).

### Exercice 14 - Low-level binding
Dans cet exercice, à chaque ajout/suppression/changement de personne dans la liste des personnes, on souhaite pouvoir recalculer automatiquement les informations de la liste en fonction de l'**intégralité** de son contenu. Les bindings simples (haut-niveau) ne sont donc pas suffisants.

Le bindings bas-niveau (low-level bindings) apporte plus de flexibilité modulo quelques lignes de code supplémentaires. Pour utiliser des bindings de bas niveau, il y a trois étapes à respecter :

* Créer un objet qui correspondra à votre binding (par exemple de sous-type de `DoubleBinding` si la liaison doit se faire sur un nombre réel).
* Faire un appel à la fonction `bind(Observable... dépendances)` de la superclasse en lui passant en paramètre les dépendances à lier. Toutes les classes de binding ont une implémentation de la méthode `bind(Observable... dépendances)`.
* Redéfinir la méthode `computeValue()` en écrivant le code qui calculera (et retournera) la valeur courante du binding.

Voici un exemple de création de binding bas niveau pour le calcul de l'aire d'un rectangle :

```java
    DoubleProperty hauteur = new SimpleDoubleProperty(7.0);
    DoubleProperty largeur = new SimpleDoubleProperty(5.0);
    
    DoubleProperty aire = new SimpleDoubleProperty(); // valeur qui sera calculée à la volée
    
    DoubleBinding aireBinding = new DoubleBinding() {
        // constructeur de la classe interne anonyme
        {
           this.bind(hauteur, largeur); // appel du constructeur de la classe mère (DoubleBinding)
        }

        @Override
        protected double computeValue() {
            return hauteur.get() * largeur.get();
        }
    };
    aire.bind(aireBinding); // Liaison de la propriété aire au binding
    //  tous les changements de la hauteur et de la largeur vont être pris en compte :
        
    System.out.println(aire.get()); // affiche 35
    largeur.setValue(10);
    System.out.println(aire.get()); // affiche 70
```


1. On souhaite maintenant faire calculer automatiquement l'âge moyen des personnes dans la liste `lesPersonnes`. Consultez la nouvelle version des classes qui vous sont fournies. Instanciez le binding `calculAgeMoyen`, dont vous vous servirez pour que l'attribut `ageMoyen` soit actualisé au fur et à mesure des modifications de la liste `lesPersonnes`. Vous compléterez la classe `Personne` avec les méthodes dont vous pourriez avoir besoin, ainsi que le code de la fonction `main(String[] args)`, avant de tester avec la méthode `question1()`.

2. Écrivez un second binding `calculNbParisiens`, qui permettra de connaitre, grâce à l'attribut `nbParisiens`, le nombre de personnes nées à Paris. Testez ensuite avec la méthode `question2()`.

### Exercice 15 - Bindings

On reprend l'exercice du précédent TP qui affiche une interface de login.

Complétez la méthode `createBindings()` afin que :
- le champ du mot de passe ne soit pas éditable si le nom de l'utilisateur fait moins de 6 caractères,
- le bouton `cancel` ne soit pas cliquable si les deux champs sont vides,
- le bouton `ok` ne soit pas cliquable tant que le mot de passe n'a pas au moins 8 caractères, et ne contient pas au moins une majuscule et un chiffre.

**Remarque** : n'oubliez pas d'initialiser correctement votre contrôleur...

### Exercice 16 - Bindings bidirectionnels

Dans cet exercice, on cherche à visualiser la correspondance entre deux températures, l'une exprimée en degrés Celsius et l'autre en degrés Fahrenheit.
Pour cela, on utilisera 2 composants graphiques `Slider`, le premier (donnant la température en Celsius) qui pourra varier entre 0°C et 100°C, et le second qui pourra varier entre 0°F et 212°F, comme sur l'image ci-dessous :

![](img/Exo16.png)

Pour plus d'information sur la relation entre les deux unités de mesure de température et les règles de conversion : https://fr.wikipedia.org/wiki/Degr%C3%A9_Fahrenheit

1. Créez un binding bidirectionnel qui permettra que toute variation d'un des 2 curseurs provoque automatiquement le changement correspondant dans le second.

2. Ajoutez maintenant les températures exprimées sous forme de texte dans les `TextField` et faites en sorte qu'elles soient liées de manière bidirectionnelle avec les sliders correspondants. Vous pourrez avoir besoin d'utiliser la méthode statique [`bindBidirectional`](https://openjfx.io/javadoc/18/javafx.base/javafx/beans/binding/Bindings.html#bindBidirectional(javafx.beans.property.Property,javafx.beans.property.Property,javafx.util.StringConverter)) de la classe utilitaire [`Bindings`](https://openjfx.io/javadoc/18/javafx.base/javafx/beans/binding/Bindings.html).<br/>

