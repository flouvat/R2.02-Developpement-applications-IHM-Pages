---
layout: post
title: "TD/TP 3 -- Introduction à FXML"
categories: jekyll update

mathjax: true
---

Comme vous l'avez pu constater dans les exercices précédents, l'intégralité du code d'organisation des composants graphiques et les traitements correspondants étaient définis dans le même bloc de code Java. Par exemple, pour le jeu Pacman de l'exercice 5, la classe `JeuMain` gérait à la fois les composants principaux du graphe de scène du jeu, mais aussi la gestion des mouvements des personnages. Cette approche (dite _procédurale_) marche tant que le code de l'IHM n'est pas trop complexe. Mais le code deviendra ingérable dès que votre IHM contiendra des nombreux composants graphiques (potentiellement imbriqués), avec beaucoup de code de traitement associé.

Par ailleurs, rappelons qu'un des avantages d'utiliser JavaFX est la possibilité de décrire les interfaces graphiques de manière déclarative dans un langage dérivé du XML -- le FXML. Le graphe de scène ayant une structure arborescente, la description de celui-ci en XML est assez intuitive.

Ainsi, utiliser le FXML permet de séparer la logique de construction de l'interface utilisateur (_UI_), du code métier de l'application (_business logic_). Cette séparation devient très vite utile (voire nécessaire) lorsque la fenêtre a beaucoup de composants graphiques (potentiellement imbriqués), auxquels sont attachés des écouteurs. De plus, décrire les interfaces devient plus simple avec FXML comparé à l'approche procédurale.

Dans cette partie, nous allons voir comment construire des interfaces graphiques en utilisant le FXML. Afin de continuer à organiser nos fichiers sources, nous allons créer un nouveau package `com.example.partie3` dans notre projet "exemple". Comme dans les parties précédentes, n'oubliez pas de vérifier dans `module-info.java` que la référence à ce nouveau package est bien exportée. Nos applications dans cette partie utilisant FXML, il faut donc également ajouter une instruction indiquant que le package est "ouvert" à FXML.

```java
    opens com.example.partie3 to javafx.fxml;
    exports com.example.partie3; 
```



## 3.1 Création d'un formulaire de connexion avec FXML [2](https://www.callicoder.com/javafx-fxml-form-gui-tutorial/)

Pour commencer, nous allons construire une simple interface de connexion en s'appuyant sur FXML. Son interface ressemblera à la capture d'écran suivante :

![](img/javafx-fxml-form-gui-tutorial.png)

Nous allons créer une nouvelle classe `RegistrationFormApplication` dans notre projet à partir du code fournit ci-dessous. Pour cela, il suffit simplement de sélectionner le package `com.example.partie3` et de copier/coller le code.

```java
import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class RegistrationFormApplication extends Application {

    @Override
    public void start(Stage primaryStage) throws Exception{
        Parent root = FXMLLoader.load(getClass().getResource("registration_form.fxml"));
        primaryStage.setTitle("Registration Form FXML Application");
        primaryStage.setScene(new Scene(root, 800, 500));
        primaryStage.show();
    }


    public static void main(String[] args) {
        launch(args);
    }
}
```

L'objet `root` représente le composant racine de votre interface utilisateur, les autres composants sont imbriqués à l'intérieur. Il est initialié grâce à la méthode [`load`](https://openjfx.io/javadoc/20/javafx.fxml/javafx/fxml/FXMLLoader.html#load()). Cette méthode cherche à lire le fichier FXML `registration_form.fxml` stocké dans le répertoire `ressources` du projet. Le chemin vers ce fichier est donné par l'appel à la méthode `getResource`. Si le fichier existe, la méthode `load` va s'appuyer sur l'interface décrite  à l'intérieur en XML  pour créer toute l'arborescence des conteneurs et des composants. Une fois l'objet `root` créé et initialisé, il est passé en paramètre d'une `Scene`, qui est à son tour passée en paramètre du `Stage` (comme pour n'importe quelle application JavaFX).

A noter que la méthode `load` a une valeur de retour générique. Vous pourriez donc charger un type plus spécifique que `Parent`, ce qui permettrait d'accéder à des méthodes spécifiques au composant. Cependant, cette solution rend le code plus "fragile". Si on modifie le type du composant racine dans le fichier FXML, mais que l'on oublie de changer la classe associée à l'objet retourné par `load` dans le code, l'application lancera une erreur au moment de l'exécution (mais pas au moment de la compilation). En effet, il y aurait une incompatibilité entre le type déclaré dans votre FXML et celui déclaré dans votre votre code Java.

Pour l'instant, le fichier FXML n'existe pas. L'application lance donc une erreur si on essaie de l'exécuter. Nous allons donc créer le répertoire `ressources/com/example/partie3` dans le projet (à partir d'IntelliJ), créer le fichier `registration_form.fxml` et y copier le code suivant.

![](img/fxml_files.png)

```xml
<?import javafx.scene.layout.GridPane?>
<?import javafx.geometry.Insets?>
<?import javafx.scene.layout.ColumnConstraints?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.text.Font?>
<?import javafx.scene.control.TextField?>
<?import javafx.scene.control.PasswordField?>
<?import javafx.scene.control.Button?>

<GridPane xmlns:fx="http://javafx.com/fxml" alignment="center"
          hgap="10" vgap="10">
    <padding><Insets top="40" right="40" bottom="40" left="40"/></padding>
    <columnConstraints>
        <ColumnConstraints minWidth="100" prefWidth="100"
                           maxWidth="Infinity" halignment="RIGHT">
        </ColumnConstraints>
        <ColumnConstraints minWidth="200" prefWidth="200"
                           maxWidth="Infinity" hgrow="ALWAYS">
        </ColumnConstraints>
    </columnConstraints>
</GridPane>
```
Dans le document FXML ci-dessus, nous créons une mise en page `GridPane` alignée au centre et présentant un écart horizontal et vertical de 10. Nous spécifions également une marge intérieure de 40px de chaque côté. La balise `columnConstraints` permet d'ajouter une contrainte à tous les composants de l'interface utilisateur placés dans cette colonne. Plus précisemment, tous les composants placés dans la première colonne seront alignés à droite et auront une largeur minimale de 100px et une largeur préférée de 100px. De même, tous les composants placés dans la deuxième colonne auront une largeur minimale de 200px et une largeur préférée de 200px. Ils s'agrandiront aussi horizontalement et rempliront l'espace supplémentaire si l'écran est redimensionné (propriété `hgrow="ALWAYS"`). Les controleurs (champs de saisie, étiquettes et bouton) n'étant pas encore créés, on obtient  la fenêtre ci-dessous lorsqu'on exécute le code.

![](img/fxml_1.png)

Le `GridPane` est vide pour le moment. Nous allons donc ajouter les étiquettes, les champs de texte et le champs de mot de passe pour créer le formulaire d'inscription à l'intérieur de la balise `GridPane` dans le fichier FXML.

```xml
    <!-- Add Header Label -->
    <Label text="Registration Form (FXML)" GridPane.columnIndex="0" 
           GridPane.rowIndex="0" GridPane.columnSpan="2" 
           GridPane.rowSpan="1" GridPane.halignment="CENTER" >
        <font>
            <Font name="Arial" size="24" ></Font>
        </font>
        <GridPane.margin>
            <Insets top="20" right="0" bottom="20" left="0"></Insets>
        </GridPane.margin>
    </Label>

    <!-- Add Name Label -->
    <Label text="Full Name : " GridPane.columnIndex="0" 
           GridPane.rowIndex="1" >
    </Label>
    <!-- Add Name Text Field -->
    <TextField fx:id="nameField" prefHeight="40" 
               GridPane.columnIndex="1" GridPane.rowIndex="1"/>

    <!-- Add Email Label -->
    <Label text="Email ID : " GridPane.columnIndex="0" 
           GridPane.rowIndex="2" >
    </Label>
    <!-- Add Email Text Field -->
    <TextField fx:id="emailField" prefHeight="40" 
               GridPane.columnIndex="1" GridPane.rowIndex="2"/>

    <!-- Add Password Label -->
    <Label text="Password : " GridPane.columnIndex="0" 
           GridPane.rowIndex="3" >
    </Label>
    <!-- Add Password Field -->
    <PasswordField fx:id="passwordField" prefHeight="40" 
                   GridPane.columnIndex="1" GridPane.rowIndex="3"/>

    <!-- Add Submit Button -->
    <Button fx:id="submitButton" text="Submit"
            prefWidth="100" prefHeight="40" defaultButton="true"
            GridPane.columnIndex="0" GridPane.rowIndex="4"
            GridPane.columnSpan="2" GridPane.rowSpan="1"
            GridPane.halignment="CENTER">
        <GridPane.margin>
            <Insets top="20" right="0" bottom="20" left="0"></Insets>
        </GridPane.margin>
    </Button>
```

Le code FXML ci-dessus est assez explicite. `GridPane.columnIndex` et `GridPane.rowIndex` sont les propriétés permettant de placer les contôleurs dans une cellule particulière de la `GridPane`. `GriPane.columnSpan` et `GridPane.rowSpan` permettent d'indiquer que le contrôleur s'étendra sur plusieurs colonnes/lignes. A l'execution, on obtient l'interface souhaitée, mais elle n'intègre pas encore la gestion des évènements.

![](img/fxml_2.png)

Nous allons maintenant créer une classe Java permettant de contrôler (i.e. gérer) les évènements lancés sur l'interface. Ce type de classe est aussi appelé "contrôleur" car il permet de contrôler les actions de l'utilisateur et d'y associer la logique métier. Cette notion est à différencier de la notion de "contrôleur" d'interface utilisateur qui correspond à des composants de l'interface graphique (et sont des classes et objets JavaFX).  Pour cela, nous allons copier le code suivant dans le répertoire `partie3` de notre projet.

```java
import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.PasswordField;
import javafx.scene.control.TextField;
import javafx.stage.Window;

public class RegistrationFormController {
    @FXML
    private TextField nameField;

    @FXML
    private TextField emailField;

    @FXML
    private PasswordField passwordField;

    @FXML
    private Button submitButton;

    @FXML
    protected void handleSubmitButtonAction(ActionEvent event) {
        Window owner = submitButton.getScene().getWindow();
        if(nameField.getText().isEmpty()) {
            System.out.println("Form Error!, Please enter your name");
            return;
        }
        if(emailField.getText().isEmpty()) {
            System.out.println("Form Error!, Please enter your email id");
            return;
        }
        if(passwordField.getText().isEmpty()) {
            System.out.println("Form Error!, Please enter a password");
            return;
        }

        System.out.println("Registration Successful! \n Welcome " + nameField.getText());
    }
}
```

Cette classe va stocker des références vers les objets représentant les étiquettes, champs et bouton créés lors du chargement du fichier FXML par `FXMLLoader`.  Pour identifier quel élément FXML doit être injecté dans quel attribut membre, le chargeur FXML fait correspondre la propriété `fx:idattribut` de l'élément FXML avec le nom de l'attribut membre annoté avec `@FXML`. Par exemple, l'élément avec la propriété `fx:id="passwordField"` sera injecté dans l'attribut `passwordField`.

La méthode `handleSubmitButtonAction()` contient le code pour gérer la soumission du formulaire. Elle est annotée avec `@FXML` afin qu'elle puisse être utilisée dans le document FXML. Toutefois, en l'état, le code FXML n'est pas encore relié à cette méthode. Nous allons changer cela en intégrant la proprité  `onAction="#handleSubmitButtonAction"` dans la balise `Button` du fichier FXML. 

L'application est presque opérationnelle. Il ne manque plus qu'à spécifier le nom de la classe contrôleur (Java) dans le fichier FXML. Ainsi, `FXMLLoader` instanciera cette classe au lancement de l'application et initialisera ses différents grâce aux annotations et propriétés définies précédemment. Pour cela, il suffit d'ajouter la propriété `fx:controller="com.example.partie3.RegistrationFormController"` dans la balise du `GridPane` du fichier FXML. 

L'application est maintenant opérationnelle. Lors du clic sur le bouton, l'interface déclenche la méthode `handleSubmitButtonAction` et affiche des messages dans le terminal.

![](img/fxml_3.png)

Il est possible d'améliorer encore cette application en remplaçant l'affichage dans le terminal par l'ouverte d'une fenêtre d'alerte ("un pop-up"). Nous allons créer maintenant un nouvelle classe `AlertHelper` dans notre package pour représenter ces fenêtres d'alerte. 

```java
import javafx.scene.control.Alert;
import javafx.stage.Window;

public class AlertHelper {

    public static void showAlert(Alert.AlertType alertType, Window owner, String title, String message) {
        Alert alert = new Alert(alertType);
        alert.setTitle(title);
        alert.setHeaderText(null);
        alert.setContentText(message);
        alert.initOwner(owner);
        alert.show();
    }
}
```

Nous modifions ensuite la méthode `handleSubmitButtonAction` afin d'exploiter cette classe à la place de la sortie stantard. 

```java
    @FXML
    protected void handleSubmitButtonAction(ActionEvent event) {
        Window owner = submitButton.getScene().getWindow();
        if(nameField.getText().isEmpty()) {
            AlertHelper.showAlert(Alert.AlertType.ERROR, owner, "Form Error!", 
                    "Please enter your name");
            return;
        }
        if(emailField.getText().isEmpty()) {
            AlertHelper.showAlert(Alert.AlertType.ERROR, owner, "Form Error!", 
                    "Please enter your email id");
            return;
        }
        if(passwordField.getText().isEmpty()) {
            AlertHelper.showAlert(Alert.AlertType.ERROR, owner, "Form Error!", 
                    "Please enter a password");
            return;
        }

        AlertHelper.showAlert(Alert.AlertType.CONFIRMATION, owner, "Registration Successful!", 
                "Welcome " + nameField.getText());
    }
```

On obtient au final le résultat suivant.

![](img/fxml_4.png)



## Exercices

### Exercice 7 - Utilisation de FXML


Dans le répertoire contenant le code source de l'exercice 7, vous trouvez en partie le code de `CounterMain`, qui définit la fenêtre principale, et dont la structure est chargée à partir du fichier ressource *"CounterView.fxml"* du répertoire *"resources/exercice7"*.

Le contenu de `CounterView.fxml` définit la racine de la scène comme un conteneur `BorderPane`, dont l'élément au centre est un `VBox`. Ce dernier contient un `Label` et un conteneur `HBox`, lui-même contenant deux boutons. Le nom de chacun de ces éléments est donné par l'attribut `fx:id`.

Complétez la classe `CounterController` en déclarant les attributs correspondant aux éléments du fichier *fxml* et annotez-les avec `@FXML`. Initialisez le texte du `Label` avec la chaîne *"0"*.

Écrivez le code des deux méthodes `increment()`et `decrement()`, qui font varier la valeur de l'attribut `counter`, et modifient le texte du `Label`. Associez ces méthodes avec les éléments du fichier *fxml*, en ajoutant dans les balises appropriées les attributs `onAction="#increment"` et `onAction="#decrement"`.

Associez enfin les fichiers `CounterController.java` et `CounterView.fxml` en ajoutant dans la balise racine un attribut `fx:controller` de valeur égale au nom complet de la classe (c'est-à-dire en précisant aussi le package dans lequel elle est définie).




### Exercice 8 - Création d'une fenêtre de connexion en FXML

L'objectif de cet exercice est de créer une fenêtre de connexion similaire à la maquette ci-dessous. Un clic sur le bouton _OK_ affiche dans la console le nom de l'utilisateur et une suite d'étoiles dont la longueur correspond au nombre de caractères du mot de passe. Un clic sur _Cancel_ vide les deux champs de saisie de l'interface.

![](img/Exo8.png)


Une trame de code est disponible dans le dépôt Github Classroom du TP. Dans le répertoire source de l'exercice 8, vous trouvez la classe `LoginControl`. Elle sera utilisée pour définir le contrôleur Java de l'interface graphique décrite dans le fichier `LoginView.fxml` (qui est dans le répertoire `resources` correspondant). On remarque d'ailleurs que la classe est mentionnée dans la balise `GridPane` du fichier *fxml* au niveau de la propriété `fx:controller`.

Cette trame d'application utilise aussi un fichier *CSS* (fourni) pour définir le style des différents composants graphiques de l'interface. Il n'est pas nécessaire de le modifier. Par contre, il faut l'associer au `GridPane` de votre fenêtre pour que celle-ci applique la feuille de style. Il vous faut donc modifier le fichier fxml et ajouter la propriété `stylesheets="exercicie8/Login.css"` à la balise `GridPane`. 


La totalité de la classe `LoginMain` vous est fournie. Par contre, il faut compléter le fichier *fxml* et intégrer les éléments manquants. Vous devez aussi compléter la classe `LoginControl` en déclarant les attributs manquants (qui correspondent aux éléments du fichier *fxml*) et en implémentant les actions des deux boutons.



Vous pouvez aussi utiliser  [Scene Builder](https://gluonhq.com/products/scene-builder/) pour modifier la fenêtre. Pour utiliser cet outil graphique de création/modification de fenêtres, il vous suffit d'ouvrir dans IntelliJ  le fichier *fxml* et de sélectionner l'onglet `Scene Builder` (en bas de la fenêtre avec le code XML). Si l'outil n'est pas installé, vous pouvez l'installer en cliquant sur le bouton `Download SceneBuilder` en haut de la fenêtre.


### Exercice 9 - Animations

Regardez (et exécutez) le code fourni dans la classe `Animation`, et transformez-le pour que l'image fasse le tour de la fenêtre, puis revienne automatiquement en sens inverse.

### Exercice 10 - Conteneurs et FXML

Reprenez l'exercice 1 (Conteneurs de base) pour réaliser l'interface en utilisant FXML et SceneBuilder. Vous pouvez ajouter un contrôleur à votre fichier *fxml* pour gérer les actions des différents composants de votre application.
