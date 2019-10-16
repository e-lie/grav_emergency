---
title: 'Exercices Python - Partie 3'
visible: true
---

## 9. Orienté Objet

**9.1**: Pour commencer tranquillement, suivez la présentation pas à pas des classes dans le [MOOC d'open classroom](https://openclassrooms.com/fr/courses/235344-apprenez-a-programmer-en-python/232721-apprehendez-les-classes). Faites les trois première leçon:
	- [Appréhendez les classes](https://openclassrooms.com/fr/courses/235344-apprenez-a-programmer-en-python/232721-apprehendez-les-classes)
	- [Définissez des propriétés](https://openclassrooms.com/fr/courses/235344-apprenez-a-programmer-en-python/232793-definissez-des-proprietes)
	- [Appliquez des méthodes spéciales](https://openclassrooms.com/fr/courses/235344-apprenez-a-programmer-en-python/233046-appliquez-des-methodes-speciales)

**9.2**: Nous allons recommencer l'exercice Table de multiplication en version orientée objet.
	- Créez une classe `TableDeMultiplication` avec des paramètres "privés" `_base_table` (le nombre de la table) et `_resultats` pour stocker le nombre de la table (exp 8) et la liste des résultat (`[8, 16, 24, 32, ..., 80]`).
	- Améliorer le constructeur pour qu'il initialise les deux paramètres précédents.
	- Ajouter une méthode `__repr__(self)` qui permet à la table de s'afficher elle-même. Testez la avec par exemple `print(maTableDeMult)`.
	- Ajoutez une méthode `get_base()` qui renvoie la base d'une table de multiplication.
	- Ajoutez une méthode `__getitem__(self, position)` qui permet de récupérer la valeur d'un résultat de la table de mutiplication (sans utiliser directement `_resultats`)
	- Testez votre méthode `__getitem__(self, position)` en utilisant la syntaxe `ma_table_de_mult[3]` et `ma_table_de_mult[2:5]`
	- Ajoutez une méthode `change_base(self, nouvelle_base)` qui permet de changer la base de la table et recalculer les résultats.
	- Ajoutez un nouveau paramètre `_longueur` qui permet de créer des tables de longueur variable et une méthode `change_longueur(self, longueur)`
	- Ajoutez une méthode `__len__(self)` qui permet de connaître la longueur de la table avec `len(table)`.
	- Ajoutez un déclenchement d'exceptions dans les méthodes `__init__` et `change_base` et `change_longueur` si les paramètres donnés ne sont pas bon.
	- Créez un programme principal qui affiche les tables de base 1 à 10 de longueur 20.



## 10. Fichiers et librairies

Les énoncés des exercices suivants sont un peu plus compliqués et techniques que les précédents, et ont aussi pour objectifs de vous inciter à explorer la documentation des librairies pour trouver les outils dont vous avez besoin. Il existe de nombreuse façon de résoudre chaque exercice. 

**10.1** : Écrire une fonction qui prends un nom de fichier en argument et retourne le contenu si elle a été capable de le récupérer. Sinon, elle doit déclencher une exception qui explique en français pourquoi elle n'a pas pu.

**10.2** : Écrire une fonction qui remplace un mot par un autre dans un fichier. On pourra pour cela se servir de `une_chaine.replace("mot", "nouveau_mot")` qui renvoie une version modifiée de `une_chaine` en ayant remplacé "mot" par "nouveau mot".

**10.3** : Télécharger le fichier `https://app.yunohost.org/community.json` (avec votre navigateur ou `wget` par exemple). Écrire une fonction qui lit ce fichier, le charge en tant que données json. Écrire une autre fonction capable de filtrer le dictionnaire pour ne garder que les apps d'un level `n` donné en argument. Écrire une fonction similaire pour le status (`working`, `inprogress`, `broken`).

**10.4** : Améliorez le programme précédent pour récupérer la liste directement depuis le programme avec `requests`. Gérer les différentes exceptions qui pourraient se produire (afficher un message en français) : syntaxe json incorrecte, erreur 404, time-out du serveur, erreur SSL