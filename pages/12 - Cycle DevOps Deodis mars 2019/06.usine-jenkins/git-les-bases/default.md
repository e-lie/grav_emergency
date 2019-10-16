---
title: 'Git - les bases'
visible: false
---

# Créer un projet git

Durant ces exercices utilisez git en ligne de commande (sans GUI) l'objectif est de pratiquer les différentes commandes de base git.

### Commencez par choisir un exercice de programmation Java : 
- Soit un exercice de la semaine dernière que vous n'avez pas fini
- Soit un exercice de exercism (pour cela voir [installer exercism](https://eliegavoty.fr/devops/installer-exercism))

### Installer Gradle
Pour utiliser exercism vous aurez besoin de Gradle. Installez le comme indiqué [ici](https://eliegavoty.fr/devops/java-poe/gradle-bootcamp)

### git init
- Créer ou visitez le dossier de code de votre exercice.
- Initialisez un nouveau dépot de code avec la commande `git init`
- Utilisez ensuite `git status` pour voir l'état de votre dépôt

### Dire à git de suivre un fichier
- Utilisez `git add <nom_fichier>` sur un fichier .java. Puis faites à nouveau `git status`. Le fichier est passé à l'état suivi (tracked)

### Faire votre premier commit
- Faites `git status` pour constater que tous les fichiers sont non suivis sauf un.
- Pour valider une version du code on utilise la commande `git commit -m <message_de_commit>` (commit signifie engager alors réfléchissez avant de lancer cette commande ! ). Généralement on utilise le message `initial commit` pour le premier commit d'un dépôt. Valider la version courante.
- Configurez votre identité de développeur git le cas échéant (avec les commandes proposées)

### Commit de tous les fichiers
- Utiliser `git add` avec l'option `-A` pour ajouter tous les fichiers actuels de votre projet (avant résolution de l'execice).
- Supprimez le suivi d'un des fichier avec la commande `git rm`
- Faites `git status` pour regarder l'état et confirmer que tous les fichiers sont suivis sauf un.
- Créer un nouveau commit.

### Annuler un commit.
Oh non ! vous avez oublié un fichier dans votre commit précédent :p.
- Cherchez dans votre memento comment annuler le dernier commit (sans en créer un nouveau)
- Faites un nouveau commit comprenant le fichier manquant (avec tous les fichiers)
- Utilisez `git log` pour voir la liste des commits du dépot (vous devriez en avoir 2 à ce stade)

### Faire l'exercice.
- Commencez la résolution de l'exercice de Java.
- A chaque fois que vous modifiez un fichier, vous pouvez constater les modifications ajoutées avec `git diff`
- Faites régulièrement des commits avec des messages décrivant vos modification. Vérifiez à chaque fois avant de commiter que votre code ne comporte pas d'erreur de syntaxe (compilez le au moins)
- Constatez à l'aide de `git log` que vos commit sont corrects.
- Lorsque votre exercice est terminé (les tests passent ou le formateur à confirmé la solution):
	- Créez un commit avec un message adéquat.
	- Créez un tag `v1.0` sur le commit grâce à la commande git `git tag`

### Nouvel exercice
- Choisissez un second exercice.
- Créez un nouveau dépot.
- Résolvez l'exercice en versionnant le code avec git comme précédemment.

### Ignorer les fichier .class ou le dossier build (gradle)
- Nous voudrions ne pas suivre les fichiers de compilation java avec l'extension `.class` ou le dossier `build`
- Installez le logiciel `tig`.
- Utilisez le pour chercher le premier commit dans lequel il y a un fichier .class ajouté.
- Créez un commit d'annulation revenant au niveau du dernier commit sans fichier .class avec `git revert <num_commit>`.
- Récupérez dans votre historique le code de l'exercice résolu pour remettre vos fichiers dans l'état final (sans les .class).
- Cherchez sur internet comment ignorer certains fichiers avec git grâce au `.gitignore` et ignorez les fichier `.class`.
- Compilez à nouveau votre programme et vérifiez avec `git status` que les fichiers .class sont ignorés
- Faites un commit avec votre exercice résolu sans les .class.