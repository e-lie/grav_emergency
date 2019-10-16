---
title: 'Git - Explorer un dépôt'
visible: true
---

Durant cette partie nous allons explorer un dépot git existant grâce aux commande git de base mais également grâce au GUI de VSCode

## Récupérer un dépôt de code

- Récupérez le dépot https://github.com/leszko/calculator sur votre **Bureau** à l'aide de la commande `git clone`.
- Ouvrez le projet dans VSCode

Il s'agit d'un dépôt exemple d'une application Spring Boot assez simple pour illustrer le déploiement continu avec Jenkins.

## Explorer

- Tentez de lancez l'application avec la commande `./gradlew bootRun` Que ce passe-t-il ?

- Quel est le premier commit du dépôt ? A quoi sert-il ?

- Utilisez la commande `git blame` sur le fichier Jenkinsfile pour identifier la personne qui a introduit la ligne 93 concernant le `smoke test`

- Qui est l'auteur du commit en question ?

- À l'aide de tig cherchez le premier commit de l'historique avec ce fichier `Jenkinsfile`. Notez éventullement son numéro dans un fichier à part.

- Déplacez vous au niveau de ce commit avec `git checkout <num_commit>`. Votre dépôt est en mode HEAD détaché c'est à dire que le pointeur HEAD se balade le long de l'historique.
C'est un état anormal.

- Cherchez les fichiers de code java et la fonction `sum` dans cette application.

- Lancez à nouveau l'application avec la commande `./gradlew bootRun`

- Utilisez l'application en visitant l'adresse http://localhost:8080/sum?a=<un_entier>&b=<un_autre_entier> (remplacez par des entiers pour effectuez l'addition)

- Utilisez git reflog pour observer les déplacement de votre pointeur HEAD.

## Créer une branche pour étendre l'application

Nous allons maintenant créer une branche en repartant du début du projet pour étendre l'application avec une fonction soustraction.

- Installez dans VSCode l'extension `git graph`

- Retounez à la fin de l'historique du projet comme précédemment (master).

- Nous allons expérimenter de réinitialiser (violemment) le projet au début de son historique avec `git reset --hard`. Réinitialisez au niveau du premier commit avec Jenkinsfile identifié précédemment. Voyez  sur git graph que les commits on été effacés.

- La commande précédente a effacé toutes les modifications du dépôt des 106 derniers commits. Faites bien attention avec cette commande ! Dans notre cas ce n'est pas un problème car ces commits sont disponibles sur le serveur. Pour récupérer les commits effacés utilisez `git pull`.

- Pour créer une branche plus "doucement" nous allons a nouveau déplacer HEAD au niveau du commit d'intrudction du Jenkinsfile

- Créez une nouvelle branche avec `git checkout -b <nom branche>` appelez la **substract**.

- Trouvez comment ajouter une fonction substract à l'application et une route http pour afficher le résultat.

## Fonctionnalité suivante:

- Dans la branche substract ajoutez les 2 autres opérations et affichez l'opération complète (exp 2+5=7) au lieu de simplement le résultat.

- Encadrez le résultat le résultat affiché par le navigateur en incorporant la classe encadreur à l'application (à côté de Calculator.java).

## Avancé

Tout ce ceci était trop simple pour vous ?

- Téléchargez l'application exemple [Spring Petclinic](https://github.com/spring-projects/spring-petclinic)

- Explorez l'application.

- Renseignez vous sur le principe d'injection de dépendance caractéristique du framework Spring. (différent de l'import).

- Essayez de debugger dans Eclipse ou Intellij IDEA le programme pour suivre son exécution depuis la requête jusqu'à la construction d'une page.

- Trouvez comment ajouter de nouvelles pages et modifier le modèle de données.



