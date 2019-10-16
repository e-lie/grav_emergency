---
title: 'Exercices Java - Partie 1'
published: true
visible: true
---

Avant de commencer vérifiez que vous avez correctement installé et configuré Java:
[Installer le kit de dev Java 8 sur ubuntu 18.04](/java-poe/install-jdk8)

## 0 - Organisation de votre dossier d'exercices et HelloWorld

**Java** est un langage qui **impose de nombreuses contraintes de structuration** pour garantir que les développeurs produisent un code standard et structuré même s'ils ne maîtrisent pas complètement les enjeux d'architecture logicielle. En particulier le code **Java** est nécessairement **organisé en classes** (Cf cours POO ou SDZ pour une explication détaillée) et **en packages**.

Nous allons dès le départ créer une structure standard de packages (c'est-à-dire de dossiers) pour ranger les exercices. Cela permettra de comprendre dès le départ la philosophie de Java et prendre de bonnes habitudes.

Les packages sont simplement des dossiers dans lesquels sont rangés (et déclarées) les classes ce qui permet ensuite de les trouver plus facilement et de les importer (Cf Cours sur les bibliothèques et packages ou https://openclassrooms.com/fr/courses/26832-apprenez-a-programmer-en-java/21583-creez-des-packages).

Souvent on utilise un logiciel de développement (IDE) comme Eclipse ou Intellij IDEA pour structurer le le projet à notre place. VSCode est également capable de le faire. Mais il est intéressant de le faire à la main au moins au départ pour comprendre la logique de l'organisation (et pour pouvoir comprendre les problèmes quand les outils automatiques s'avères buggés ou incompatibles) :

**0.1 :** Créez sur le bureau un dossier `Exercices Java` (ou le nom que vous préférez) sur le bureau et chargez le dans VSCode (`Open Folder` or `Add folder to workspace`).

**0.2 :** A l'intérieur créez l'arborescence suivante: 
```
    └── edu
        └── devops
            └── exercice0
```

**0.3 :** A l'intérieur du dernier dossier créez un fichier **Exercice0.java**. Le nom du fichier et sa casse (les majuscules et minuscules) son importants. En effet, les règles conventionnelles Java sont les suivantes :
- Chaque programme Java est composé de **classes** dont le nom doit commencer par une majuscule.
- Chaque classe est décrite dans un fichier qui porte sont nom.
- Chaque fichier **.java** d'un package doit déclarer le chemin du package sur sa première ligne.

Selon ces règles il nécessaire:
- d'ajouter une ligne **en haut du fichier Exercice0.java** contenant:
```java
  package edu.devops.exercice0;
```
- Puis d'ajouter une classe `Exercice0`  avec la syntaxe comme suit:
```java
  public class Exercice0{
      <membres_de_la_classe>
  }
```

**0.4 :** Maintenant que nous avons le code de base de notre classe empaquetée nous pouvons ajouter notre programme.
**Une autre règle conventionnelle Java** est qu'un programme exécutable contient une fonction dont le nom est `main` déclarée exactement comme suit :
```java
    public static void main(String[] args){
		<opérations_du_programme>
    }
```
Ajoutez donc cette fonction à l'intérieur de la classe **Exercice0**


**0.5**: Ajoutons maintenant l'opération de notre programme : afficher `Hello World !`.
- Pour cela nous allons utilisez la fonction de base pour afficher du texte : 
```java
System.out.println(<texte_a_afficher>);
```
- Le texte en Java s'écrit entre *double quote comme ceci :  `"mon texte"`
- Autre remarque: toutes les lignes d'instructions Java doivent se terminer par un point virgule : `;`. VSCode soulignera en rouge si vous oubliez de le mettre et le programme ne compilera pas.

**0.6**: Ouvrez un terminal dans VSCode et vérifiez que vous être bien à la racine du projet (dossier **Exercices Java**). Lancez ensuite la commande de compilation: `javac edu/devops/exercice0/Exercice0.java`

- Si le compilateur ne vous renvoie pas d'erreur vous pouvez exécuter le programme avec la commande:
`java edu.devops.exercice0.Exercice0`



## 1. Variables

Selon la même logique que l'exercice précédent,  utilisez un package `edu.devops.exercice1` pour l'exercice 1 (questions 1.1, 1.2) avec comme en-tête de chacun de vos fichiers:
```java
package edu.devops.exercice1;
```

**1.1. :** Écrire, lancer et analyser l’execution du programme suivant :
```java
public class Exercice1_1{
    public static void main(String[] args) {
        String message = new String("Je connais la réponse à l’univers, la vie et le reste :");
        int reponse = 6 * 7;

        System.out.println(message);
        System.out.println(reponse);
    }
}
```
Utilisez le debugger java de VSCode (extension).

**1.2 :**  À l’aide d'un programme Java, calculer le résultat des opérations suivantes :
- 567×72
- 33ˆ4
- 98.2/6
- ((7×9)ˆ4)/6

## 2. Interactivité

**2.1 :** Demander l’âge de l’utilisateur, puis calculer et afficher l’âge qu’il aura dans deux ans.

**2.2 :** Même chose, mais en demandant l’année de naissance (approximativement, sans tenir compte du jour et mois de naissance. . . )

## 3. Chaînes de caractères

**3.1 :** Demander un mot à l’utilisateur. Afficher la longueur du mot avec un message tel que “Ce mot fait X caractères !”

**3.2 :** Remplacer les lettres `e` par `a` dans le mot, et afficher le mot ainsi modifié.

**3.3 :** Encadrer le mot donné par l'utilisateur avec des #. Par exemple avec le mot `Pikachu` :
```java
###########
# Pikachu #
###########
```
Pour cette question vous avez besoin de la fonction `repeatString()` comme suit:
```java
    static String repeatString(String s, int n) {
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < n; i++) {
            builder.append(s);
        }
        return builder.toString();
    }
  ```
  Collez ce code au dessus de la fonction `main`.

## 4. Fonctions et polymorphisme

Pour cet exercice quelques indications :
- Réfléchissez bien sur papier à comment obtenir le résultat avec la méthode de brouillon algorithmique du cours.
- Nous allons utiliser seulement un fichier de classe : `Encadreur.java` (avec la classe `Encadreur` à l'intérieur). Toutes les fonctions ferons parties de cette classe.
- Pour cette partie vous aurez également besoin d'une fonction `repeatString(s, n)` qui  répète la chaîne `s`  `n` fois. Elle permettra par exemple de contruire une chaîne de 20 espaces directement.

**4.0 :** Créer un fichier **Operations.java** avec une classe `Operations`. A l'intérieur de cette classe, créez les fonctions `additions(a, b)` et `division(a, b)` valable pour des `int` et pour des `double`. Utilisez ensuite ces fonctions dans la fonction main pout calculer et afficher:

- 4 + 172.
- 5 + 10.7.
- 17 / 3 exactement.
- 17 / 3 avec des entiers.


**4.1.0 :** Créez le fichier `Encadreur.java` et sa classe. À l'intérieur ajoutez une fonction repeatString comme suit:
```java
    static String repeatString(String s, int n) {
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < n; i++) {
            builder.append(s);
        }
        return builder.toString();
    }
  ```


**4.1.1 :** Ecrire une fonction centrer qui permet de centrer une chaîne de caractère sur 60 caractères. Par exemple `System.out.println(Encadreur.centrer("Pikachu"));` affichera :
```
|                         Pikachu                          |
```
Afficher également la longueur du résultat pour prouver que votre fonction est adéquate. Tester avec un mot de longueur paire et un mot de longueur impaire.

**4.2 :** Ajouter un argument optionnel pour gérer la largeur au lieu du “60” fixé. Par exemple `System.out.println(Encadreur.centrer("Pikachu", 40));` affichera :
```
|               Pikachu                |
```

**4.3 :** Créer une fonction encadrer qui utilise la fonction centrer pour produire un texte centré et encadré avec des ####. Par exemple, `System.out.println(Encadreur.encadrer("Pikachu", 40));` affichera :
```
########################################
#               Pikachu                #
########################################
``` 

4.4 : Au lieu d’utiliser des `#` pour encadrer le texte, passer le caractère en argument optionnel. (Par exemple : `Encadreur.encadrer("Pikachu", 20, "@")`)

## 5. Conditions

**5.1 :** Reprendre la fonction centrer de l’exercice 4.1 et gérer le cas où la largueur demandée est -1 : dans ce cas, ne pas centrer. Par exemple, `System.out.println(Encadreur.encadrer("Pikachu", -1))` affichera :
```
###########
# Pikachu #
###########
```

**5.2 :** Gérer le cas où le caractère d’encadrement est vide. Dans ce cas, ne pas encadrer. Par exemple : `encadrer("Pikachu", 40, '')` affichera juste :
```
| Pikachu |
```

**5.3 :** Dans la fonction centrer, gérer également le cas où le texte dépasse la largueur demandée. Dans ce cas, tronquer le texte. Par exemple `System.out.println(Encadreur.encadrer("Pikachu", 8))` affichera :
```
########
# Pika #
########
```

Trouvez de nouvelles façon de faire bugger la fonction. Conclusion ?


## 6. Boucles

**6.1 :** Créez une classe TableMult dans un autre fichier. Écrire une fonction qui, pour un nombre donné, renvoie la table de multiplication. Par exemple `System.out.println(TableMult.table_du_7())` affichera :
```
Table du 7
----------
1 x 7 = 7
2 x 7 = 14
[..]
10 x 7 = 70
```

**6.2 :** Cette fois, passer le nombre en argument. La fonction devient par exemple `TableMult.table_multiplication(7)`

**6.3 :** En utilisant cette fonction, afficher les tables de multiplication pour tous les nombres entre 1 et 10.

**6.4 :** Protéger l’accès à toute cette connaissance précieuse en demandant, au début du programme, un “mot de passe” jusqu’à ce que le bon mot de passe soit donné.

**6.5 :** Écrire une fonction qui permet de déterminer si un nombre est premier. Par exemple `isPrime(3)` renverra `true`, et `isPrime(10)` renverra `false`.

**6.6 :** Écrire une fonction qui permet de générer les **n** premiers nombres de la suite de Fibonnaci.







