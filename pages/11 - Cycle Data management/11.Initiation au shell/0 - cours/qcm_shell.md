
### Prénom : \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

### Nom : \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

# Initiation au shell : questionnaire

## Entourez **la** bonne réponse.


### Question 1
Une commande shell simple est composée ... :

- Du nom du programme, des éventuelles options pour préciser son comportement et des arguments éventuels sur lequel s'applique le programme.
- Du nom d'une fonction suivie de parenthèses contenant les arguments éventuels de la commande.
- Du nom du programme, d'un chemin de fichier entre parenthèses et d'un filtre éventuel.

### Question 2
Un chemin absolu est:

- Une façon unique de désigner un fichier UNIX en décrivant comment y accéder depuis la racine `/`.
- Un ensemble de façon de désigner un fichier UNIX en combinant `~`, `..` et des nom de fichiers.
- Une façon unique de désigner un fichier UNIX à partir du dossier home `~`.

### Question 3
Que produit la commande `chmod 777 fichier.txt`

- ...`fichier.txt` appartiendra à `root` ne sera donc accessible qu'à lui.
- ...`fichier.txt` deviendra un script qui pourra être exécuté par un shell.
- ...`fichier.txt` sera accessible par tous les utilisateurs pour tout type d'usage.

### Question 4
Quelles sont les trois types de permissions qui décrivent les usages possible d'un fichier UNIX ?

- Permission de copier, de télécharger et de modifier.
- Permission de lire, d'écrire et d'exécuter.
- Permission de transfert, de filtrage et de processus.

### Question 5
Quelle est la commande très simple qui permet de connaître depuis un shell l'endroit où l'on se trouve dans le système de fichiers ?

__________


### Question 6
Quelle ligne ajoute-t-on au début d'un fichier pour en faire un script shell ?

- `#!/bin/bash`
- `!!/bin/ash`
- `#/bash`

### Question 7
Comment rediriger la sortie d'une commande vers un fichier texte ?

- Utiliser `> monfichier.txt` ou `>> monfichier.txt` après la commande.
- Utiliser `wc -l monfichier.txt` ou `wc -e monfichier.txt` après la commande.
- Utiliser `&& monfichier.txt` ou `|| monfichier.txt` après la commande.

### Question 8
Pour filtrer un fichier et ne garder que les lignes contenant un certain mot j'utilise:

- `find`
- `grep`
- `wc`

### Question 9 
Comment déclare-t-on classiquement une variable d'environnement :

- `$variable = valeur;`
- `export VAR="valeur";`
- `echo $VAR="valeur";`

### Question 10
Qu'affiche `ls -l` ?

- l'ensemble des utilisateurs du système avec leux shell et leur UID.
- l'ensemble des liens symbolique dans le dossier et ses sous dossiers.
- l'ensemble des fichiers du dossier courant avec leurs permissions, possesseur et taille.
