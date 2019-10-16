---
title: 'Gradle et JUnit'
visible: true
---

## Collaborez grâce à gitlab

- Mettez vous par binome
- L'un des membres du binome créé un projet gitlab par exemple avec le nom `EncadreurCLI`, ajoutez un README
- Il invite l'autre développeur sur son projet avec des droits mainteneur (pour qu'il puisse également accepter des pull requests)
- Clonez chacun le dépôt localement.
- Créez chacun une branche portant le nom `encadreur` + votre nom ou pseudo exp `encadreur_elie`.
- Réalisez ensuite la partie suivante individuellement.

## Créez un projet Gradle from scratch

### Démonstration présentation de Gradle

- Suivez le tutoriel Gradle de démarrage proposé par Spring pour :
	- Créer l'arborescence standard d'un projet java
	- ajouter le `plugin java` qui permet de compiler automatiquement un projet Java.
	- ajouter JUnit comme dépendance
	- ajouter le `plugin application` pour lancer automatiquement la classe principale de votre projet
	- initialisez le gradle wrapper `gradlew`
- créez un fichier `.gitignore` avec à l'intérieur:
```bash
*.class
build
bin
.gradle
.project
.vscode
.classpath
org.eclipse.*
.settings
.idea
```
- Faites un commit.

## Créer un encadreur en ligne de commande

- Remplacez les classes `Greeter` et `HelloWorld` par des classes `Encadreur` et `Main`.
	- La classe Encadreur doit être une vraie classe qu'on instancie avec `new` avant d'appeler ses méthodes (non statiques).
	- Le programme principal affiche le résultat d'un encadrement.
	- Faites un commit de cette étape.

- Trouver comment faire de votre application un programme en ligne de commande :
	- Utilisez le tableau d'argument `args` de la fonction main
	- Vous pouvez lancer votre programme avec `gradle run --args="monMot 40 @"`
	- Faites attention au fait que le nombre d'arguments fournis par l'utilisateur peut être variable. Le programme doit fonctionner avec 1, 2 ou trois arguments (testez la longueur de `args`)

- Commitez votre travail.

## Merger les programmes

- Maintenant chaque membre du binome va pousser son travail de branche sur le dépôt gitlab avec la commande `git push`
- Sur gitlab initiez chacun une `Merge request` depuis votre branche vers la branche master.
- La première personne à merger ne rencontrera pas de conflit (cliquer sur le bouton merge fusionnera automatiquement à master)
- Par contre la seconde Merge request sera en conflit et devra être résolue localement par la procédure
	- faites un `git pull` sur votre branche pour être à jour. vérifier avec `git status` que la branche est synchronisée avec le dépot distant.
	- basculer sur master (git checkout)
	- faites un `pull` également pour récupérer les dernières modifications
	- démarrez le merge de la branche dans master grâce à la commande `git merge <nom_branche>`
	- résolvez le merge par exemple grâce à VSCode
	- Une fois les modifications arbitrées utilisez `git merge --continue` pour valider votre commit de merge.
	- faites un `push` du résultat
	- En visitant la page de la merge request, celle ci devrait maintenant être résolue.

## Ajouter des tests JUnit

- Mettez chacun à jour votre dépôt avec `git pull`
- Créez une nouvelle branche pour cette partie `junit_<nom>`

### Cours sur JUnit et le TDD

### Démarrage

- Dans le dossier `src` de votre projet ajoutez :
```
└── test
        └── java
            └── hello
```

- A l'intérieur de ce package ajoutez une classe `EncadreurTests`
- Importez `org.junit.*` et `org.hamcrest.*`

## Étape d'initalisation

Nous avons besoin d'un objet encadreur pour effectuer les tests

- Ajoutez à la classe `EncadreurTests` en paramètre privé `Encadreur encadreur` sans l'initialiser
- Ajoutez ensuite une étape JUnit de setup c'est à dire une fonction comme celle ci : 

### Un test sur la présence du mot

- Pour tester que le retour de encadrer contient bien le mot utiliser la méthode `containsString()` comm présentée dans cet exemple:
	- [HelloWorldTests.java](https://github.com/spring-guides/gs-gradle/blob/master/complete/src/test/java/hello/HelloWorldTests.java)

### Tests sur la largeur

- De me, tester largeur du retour de votre fonction encader. Utilisez pas exemple la fonction JUnit `assertEquals(int expected, int actual).

### Edge Cases (cas limites)

- Votre fonction encadrer gère-t-elle correctement une largeur trop faible ou négative ? un caractère absent ou de longueur supérieure à 1 ?

- Une largeur négative devrait renvoyer :
```
##########
# monMot #
##########
```

- Écrivez d'abord les test pour vérifier que ces cas inhabituels sont gérés correctement (Test Driven Developpement)

- Corrigez ensuite votre fonction encadrer pour que les tests passent.

## Merger à nouveau

- Comme précédemment commitez et pousser votre code avec git.
- Créez chacun une merge request.
- Merger ces requests en inversant l'ordre pour que chacun ai pratiqué le merge.
