---
title: 'Git - Partie2 - Explorer un dépôt'
visible: true
---

Durant cette partie nous allons explorer un dépot git existant grâce aux commandes git de base mais également grâce au GUI de VSCode.

## Récupérer un dépôt de code

- Récupérez le dépot https://github.com/leszko/calculator sur votre **Bureau** à l'aide de la commande `git clone`.
- Ouvrez le projet dans VSCode.

Il s'agit d'un dépôt exemple d'une application Spring Boot assez simple pour illustrer le déploiement continu avec Jenkins.

## Explorer le dépôt

- Tentez de lancez l'application avec la commande `./gradlew bootRun` Que ce passe-t-il ? 

Plutôt que d'utiliser la version finale de l'application qui ne fonctionne pas en l'état, remontons l'historique du dépôt pour retrouver un état plus simple de l'application.

- Quel est le premier commit du dépôt ? A quoi sert-il ?

- Utilisez la commande `git blame` sur le fichier `Jenkinsfile` pour identifier la personne qui a introduit la ligne 83 concernant le `smoke test`. Cette commande est très utile quand on travaille à plusieurs car elle permet de savoir à qui s'adresser lorsqu'on cherche à comprendre le code ou qu'on a trouvé un bug.

- Installez `tig` qui est un utilitaire pour explorer un dépôt depuis le terminal.

- À l'aide de `tig` cherchez le premier commit de l'historique avec ce fichier `Jenkinsfile`. Notez éventuellement son numéro dans un fichier à part.

- Déplacez vous au niveau de ce commit avec `git checkout <num_commit>`. Votre dépôt est en mode "HEAD détaché" c'est à dire que le pointeur HEAD se balade le long de l'historique.
C'est un état anormal dans lequel il ne faut généralement pas modifier le code. Il est très facile de se perdre dans un dépôt git (le cas échéant utilisez `git reflog` pour bien comprendre les opérations qui vous ont amené dans l'état courant).

- Cherchez les fichiers de code java et la fonction `sum` dans cette application.

- Lancez à nouveau l'application avec la commande `./gradlew bootRun`

- Utilisez l'application en visitant l'adresse `http://localhost:8080/sum?a=<un_entier>&b=<un_autre_entier>` (remplacez par des entiers pour effectuez l'addition)

- Utilisez `git reflog` pour observer les déplacement de votre pointeur HEAD.

## Créer une branche pour étendre l'application

Nous allons maintenant créer une branche en repartant du début du projet pour étendre l'application avec une fonction soustraction.

- Installez dans VSCode l'extension `GitLens`.

- Retounez à la fin de l'historique du projet comme précédemment (master).

- Nous allons expérimenter de réinitialiser (violemment) le projet au début de son historique avec `git reset --hard`. Réinitialisez au niveau du premier commit avec `Jenkinsfile` identifié précédemment. Constatez sur gitlens ou git graph que les commits on été effacés et les fichiers également (sans le `--hard` les commits auraient disparu mais les fichiers et leur contenus auraient été gardés et désindexés comme dans la partie 1).

- La commande précédente a effacé toutes les modifications du dépôt des 106 derniers commits. Faites bien attention avec cette commande ! Dans notre cas ce n'est pas un problème car ces commits sont disponibles sur le serveur. Pour récupérer les commits effacés utilisez `git pull`. `pull` va récupérer les modifications depuis le serveur.

- Pour créer une branche plus "doucement" nous allons a nouveau déplacer HEAD au niveau du commit d'introduction du `Jenkinsfile`.

- Créez une nouvelle branche avec `git checkout -b <nom branche>` appelez la **substract**.

- Trouvez comment ajouter une fonction substract à l'application et une route http pour afficher le résultat (pas besoin de connaître le Java normalement il suffit de copier coller et changer 2 ou 3 choses).

- Une fois votre fonction ajoutée faites simplement `git diff`. Cette fonction affiche en vert le code que vous venez d'ajouter.

- Ajoutez (`git add`) et commitez toutes ces nouvelles modifications.

- Maintenant que les modifications sont engagées (commitées) refaites `git diff`. Que se passe-t-il ?

- Trouvez comment faire pour comparer avec le commit précédent en utilisant `git diff` ?

- Utilisez `git reset HEAD~1` pour annuler le dernier commit puis refaites le en utilisant l'interface graphique de VSCode.

