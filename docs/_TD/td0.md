---
layout: post
title: "TD/TP 0 -- Introduction à l'environnement de développement"
categories: jekyll update

mathjax: true
---


Dans ces TD, nous allons utiliser IntelliJ IDEA. Cet environnement de développement intégré (EDI) est installé sur le VDI d'AMU, notamment dans la VM "Lunix (Linux pour tous)". Pour information, la société JetBrains commercialisant ce logiciel propose des licences gratuites pour les étudiants. Vous pouvez donc aussi l'installer sur vos machines personnelles. Pour cela, il suffit de remplir [ce formulaire](https://www.jetbrains.com/shop/eform/students). Par ailleurs, nous utiliserons aussi [Maven](https://fr.wikipedia.org/wiki/Apache_Maven) comme système de build pour les projets JavaFX (il est intégré à IntelliJ IDEA). 


## 0.1 - Application Hello World

Dans un premier temps, nous allons implémenter une application basique "Hello World" avec IntelliJ et JavaFX, **en local** sur votre machine de l'IUT. Pour cela, vous suivrez le tutoriel prévu à cet effet dans la documentation de IntelliJ. La procédure peut varier sensiblement en fonction de la version de IntelliJ. Pour la dernière version, vous pouvez vous référer à [cette page](https://www.jetbrains.com/help/idea/javafx.html). Pour la version installée sur les machines de l'IUT (version 2024.3 sous Linux), vous devez vous référez à [cette page](https://www.jetbrains.com/help/idea/2024.3/javafx.html).

A la fin du tutoriel, nous obtenons le projet et l'application suivante:

![capture Hello World app](img/helloWorldApp_files.png)
![fichiers Hello World app](img/helloWorldApp.png)




Ainsi le répertoire **src/main/java** contiendra :
* L'ensemble des paquetages de votre application ; dans l'exemple de l'image ci-dessus, il n'y en a qu'un -- `com.example.javafxdemo`.
* Un fichier spécial `module-info.java` qui décrit l'ensemble des paquetages externes utilisés dans votre application. En l'occurrence, les paquetages de la librairie JavaFX (on vous rappelle que JavaFX ne fait pas partie de l'API native Java). Les modules constituent une fonctionnalité importante de Java depuis la version 9 de la plateforme. Ils permettent de construire des exécutables légers (_.jar_) en y encapsulant l'ensemble de dépendances nécessaires au bon fonctionnement. En quelque sorte, on peut les voir comme une généralisation de la notion de paquetage. Pour plus de détails :
    * [https://en.wikipedia.org/wiki/Java_Platform_Module_System]([https://en.wikipedia.org/wiki/Java_Platform_Module_System)
    * [https://en.wikipedia.org/wiki/Modular_programming](https://en.wikipedia.org/wiki/Modular_programming)
    * [https://www.baeldung.com/java-9-modularity](https://www.baeldung.com/java-9-modularity)
    * [https://www.oracle.com/fr/corporate/features/understanding-java-9-modules.html](https://www.oracle.com/fr/corporate/features/understanding-java-9-modules.html)

Le répertoire **src/main/resources** contiendra l'ensemble de ressources du projet : images, feuilles de styles CSS, les fichiers `.fxml` contenant la description déclarative de vos interfaces graphiques, etc.

Le répertoire **src/main/test** contiendra l'intégralité des tests de votre application. Notez que pour le moment, vous n'allez pas écrire des tests unitaires pour tester vos IHM, donc le répertoire **src/main/test** n'apparaîtra pas pour le moment dans vos projets. Au moment où vous voudrez ajouter des tests unitaires, vous aurez éventuellement à créer ce répertoire.


## 0.2 - Connexion au dépôt distant GitHub Classroom avec le squelette des exercices

Il faut maintenant aller sur Ametice, aller dans la section "les liens classroom pour les TP", et cliquer sur le lien de votre groupe. Un dépôt GitHub va alors être automatiquement créé avec le code de base à compléter pour les exercices. 

Nous allons maintenant relier IntelliJ avec notre compte GitHub afin de pouvoir  versionner le code ajouté dans la suite des TP. Pour cela, il faut ouvrir IntelliJ et sélectionner ``Clone Repository``.

![](img/cloneGithub0.png)

Dans la fenêtre qui s'ouvre, vous devez ensuite copier l'URL de votre dépôt GitHub créé par le lien Ametice, puis cliquer sur le bouton ``clone``. 

![](img/cloneGithub1.png)

Une fenêtre d'IntelliJ s'ouvre alors sur les répertoires avec le code de base des différents exercices à réaliser pendant les TP. Ce projet sera à utiliser et à compléter pendant tout le cours.

![](img/cloneGithub2.png)

Dans la suite de ces TP, vous devrez enregistrer vos modifications au fur et à mesure, les valider (**commit**) et les envoyer sur le dépôt distant sur GitHub Classroom (**push**). Vous pouvez faire cela directement à partir du menu principal d'IntelliJ en cliquant sur le sous-menu ``Git``, qui ouvrira une fenêtre permettant de valider les modifications et de les pousser sur le dépôt distant (bouton ``Commit and Push ..``). Faites une petite modification (p.ex. ajout d'un espace dans le code), et tester de valider et pousser votre code.

![](img/commitPush.png)


IntelliJ va alors vous demander de saisir les informations sur son compte GitHub. Un code d'authentification vous  est alors envoyé. Vous devez le copier dans l'espace prévu dans la fenêtre du navigateur. Un message vous demande d'autoriser l'IDE de JetBrains à se connecter au compte GithHub. Un fois que cela est fait, IntelliJ est relié à votre compte GitHub et vous pouvez versionner votre code. 



Tout au long des TD/TP, vous aurez besoin de **consulter [les pages de documentation de JavaFX](https://openjfx.io/javadoc/18/)**. 


#### Consignes pour une installation sur votre machine personnelle :

Notez que depuis 2018 JavaFX ne fait pas partie de l'API Java officielle, le développement étant externalisé dans la communauté [OpenJDK](https://openjdk.java.net/) dans le projet [OpenJFX](https://wiki.openjdk.java.net/display/OpenJFX/Main).

La librairie JavaFX n'est donc pas automatiquement installée avec la plateforme Java. 
Si vous utilisez IntelliJ IDEA, JavaFX sera installé par défaut. Sinon, pour installer JavaFX sur votre machine, on vous recommande de télécharger le SDK complet : [https://gluonhq.com/products/javafx/](https://gluonhq.com/products/javafx/). Sur Linux, il suffit d'extraire l'archive contenant la SDK dans votre répertoire d'installation, par exemple dans `/opt`. Lors de la première utilisation de JavaFX avec votre IDE, vous devriez indiquer le chemin d'accès à la SDK JavaFX sur votre machine.


