---
title: 'Exercices  Java - Partie 2'
visible: true
---

## 8. Structures de données

8.1.1 : Écrire une fonction qui retourne le plus grand élément d'un tableau. de nombres entiers, et une autre fonction qui permet retourner le plus petit.
Par exemple, `plus_grand({5, 9, 12, 6, -1, 4})` retournera 12.

8.1.2 : Dans la fonction précédente, vérifier que l'argument donné est bien un tableau de taille non nulle vide.

8.2 : Écrire une fonction qui calcule la somme d'un tableau de nombres. Par exemple, `somme({3, 4, 5})` renverra 12.

8.3 : Comme 8.2, mais cette fois sans utiliser de variable intermédiaire (utiliser la récursivité)

8.4 : Écrire une fonction qui retourne seulement les entiers pairs d'une liste

8.5 : Écrire une fonction qui permet de trier une liste (ou un set) d'entiers

8.6 : Écrire une fonction qui prend en argument un chemin de fichier comme "/usr/bin/toto.py" et extrait le nom du fichier, c'est à dire "toto". On pourra utiliser la méthode `chaine.split(caractere)` des chaînes de caractère.

8.7 : Écrire un "générateur de formule de compliment / encouragement". Voir : http://www.nioutaik.fr/Felicitron/ . Pour cela, construire des chaines de caractère avec la structure suivante : {superlatif} + {chose/personne} + {adjectif} + {verbe} + {beneficiaire} + {duree}. Chaque élément sera choisi aléatoirement dans une liste de choix possibles... 
On pourra utiliser :
```java
import java.util.Random;

...
    
Random rand = new Random();

// Obtenir un nombre entre 0 et 49.
int n = rand.nextInt(50);
```

## 9. Orienté Objet

9.1 Reprennez l'exercice Table de multiplication.
	- Créez une classe `TableDeMultiplication` avec des paramètres `int nombreTable` et `int[] resultatsMult` pour stocker le nombre de la table (exp 8) et le tableau des résultat (`{8, 16, 24, 32, ..., 80}`).
	- Ajoutez un constructeur `TableDeMultiplication(8)` qui initialise les deux paramètre précédents.
	- Ajouter une fonction toString() qui permet à la table de s'afficher et affichez le résultat.
	- Créez une version modifiée du programme qui affiche les 10 tables avec un mot de passe pour utiliser votre classe `TableDeMultiplication`.

9.2 Reprennez les exercices sur l'encadrement. Créez une version modifiée de la classe Encadreur pour l'organiser comme une classe normale.
	- Créer un unique constructeur `Encadreur(String mot, int largeur, String caractere)` qui initialise les paramères sur le mot à encadrer et le format.
	- Mettez les méthodes `centrer` et `encadrer` comme méthode **privée** et **non statiques** de la classe.
	- Créez une méthode `toString()` qui affiche un encadrement conforme aux paramètres en utilisant les méthode privées.


