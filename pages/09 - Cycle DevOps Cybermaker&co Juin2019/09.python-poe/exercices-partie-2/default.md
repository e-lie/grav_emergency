---
title: 'Exercices  Python - Partie 2'
visible: true
---

## 7. Boucles

**7.1** : Écrire une fonction qui, pour un nombre donné, renvoie la table de
multiplication. Par exemple `print(table_du_7())` affichera :
```
Table du 7
----------
 1 x 7 = 7
 2 x 7 = 14
 [..]
 10 x 7 = 70
```
  - 

**7.2** : Cette fois, passer le nombre en argument. La fonction devient par
exemple `table_multiplication(7)`

**7.3** : En utilisant cette fonction (sans la modifier), afficher les tables de multiplication pour
tous les nombres entre 1 et 10.

**7.4** : Protéger l'accès à toute cette connaissance précieuse en demandant, au
début du programme, un "mot de passe" jusqu'à ce que le bon mot de passe soit
donné.
  - Définir un mot de passe et le stocker dans une variable
  - Demander un mot de passe à l'utilisateur et le stocker dans une autre variable
  - Écrire la condition qui permet de vérifier le mot de passe entré avec if
  - Écrire une boucle infinie (qui ne s'arrête jamais). Ne pas lancer le programme en l'état sinon il sera bloqué
  - Quelle instruction doit-on ajouter pour arrêter cette boucle ?
  - En combinant la boucle et le test précédent, faire en sorte que la boucle s'arrête si le mot de passe est le bon.
  - Ajouter la boucle des tables de multiplication à l'intérieur de ce dispositif pour afficher les tables si le mot de passe est bon et sortir de la boucle.

**7.5** : Écrire une fonction qui permet de déterminer si un nombre est premier. Par
exemple `is_prime(3)` renverra True, et `is_prime(10)` renverra False.

**7.6** : Écrire une fonction qui permet de générer les n premiers nombres de la
suite de Fibonnaci

**7.7** : Jeu des allumettes

Le jeu des allumettes est un jeu pour deux joueurs, où n allumettes sont
disposées, et chaque joueur peut prendre à tour de rôle 1, 2 ou 3 allumettes. Le
perdant est celui qui se retrouve obligé de prendre la dernière allumette.

- Écrire une fonction `afficher_allumettes` capable d'afficher un nombre donné
  d'allumettes (donné en argument), par exemple avec le caractère `|`
- Écrire une fonction `choisir_nombre` qui demande à l'utilisateur combien
  d'allumette il veut prendre. Cette fonction vérifiera que le choix est valide
  (en entier qui est soit 1, 2 ou 3).
- Commencer la construction d'une fonction `partie_allumettes` qui pour le moment,
  se contente de :
  - Initialiser le nombre d'allumette sur la table
  - Afficher des allumettes avec `afficher_allumettes`
  - Demander à l'utilisateur combien il veut prendre d'allumettes avec `choisir_nombre`
  - Propager ce choix sur le nombre d'allumette actuellement sur la table
  - Afficher le nouvel état avec `afficher_allumettes`
- Modifier `partie_allumettes` pour gérer deux joueurs (1 et 2) et les faire
  jouer à tour de rôle jusqu'à ce qu'une condition de victoire soit détectée (il
  reste moins d'une allumette...) moins d'une allumette.

**7.8** : Intelligence artificielle

Reprendre le jeu précédent et le modifier pour introduire une "intelligence"
artificielle qui soit capable de jouer en tant que 2ème joueur. (Par exemple,
une stratégie très simple consiste à prendre une allumette quoiqu'il arrive)

Produire un fichier unique contenant l'"intelligence" artificielle : le fichier
devra au moins contenir une fonction `play(allumettes_restantes)` qui renvoie le
nombre d'allumettes que l'IA décide de prendre. Partager le fichier pour faire
combattre les différentes IA dans l'arène !

## 8. Structures de données

**8.1.1** : Écrire une fonction qui retourne le plus grand élément d'une liste (ou
d'un set) de nombres, et une autre fonction qui permet retourner le plus petit.
Par exemple, `plus_grand([5, 9, 12, 6, -1, 4])` retournera 12.

**8.1.2** : Dans la fonction précédente, vérifier que l'argument donné est bien une
liste non vide.

**8.2** : Écrire une fonction qui calcule la somme d'une liste de nombres. Par
exemple, `somme([3, 4, 5])` renverra 12.

**8.3** : Comme 8.2, mais cette fois sans utiliser de variable intermédiaire (utiliser la récursivité)

**8.4** : Écrire une fonction qui retourne seulement les entiers pairs d'une liste

**8.5** : Écrire une fonction qui permet de trier une liste (ou un set) d'entiers

**8.6** : Écrire une fonction qui prends en argument un chemin de fichier comme
"/usr/bin/toto.py" et extrait le nom du fichier, c'est à dire "toto". On pourra
utiliser la méthode `chaine.split(caractere)` des chaînes de caractère.


