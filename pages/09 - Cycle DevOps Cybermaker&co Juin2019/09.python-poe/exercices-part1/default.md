---
title: 'Exercices Python - Partie 1'
published: true
visible: true
---

## Installer l'environnement

- Importez la machine virtuelle `ubuntu_bionic_desktop_minimal_el4stic.ova`.
- Configurez la dans virtualbox avec 5000Mb de mémoire RAM et 2 processeurs.
- Lancez la machine et méttez la en plein écran : pour éviter de se perdre en jonglerie de fenêtre entre Windows vous pouvez rester dans la machine ubuntu et accéder aux ressources depuis Firefox.
- Cherchez la logitèque ubuntu et installez les éditeurs `Visual Studio Code` et `Thonny`.

## Exercice d'algorithmique

Exercice d'introduction à faire ensemble sans coder:

- Comment calculer la liste des nombres impairs entre 1 et 100 ?
- En utilisant cette liste des nombre impairs comment en créer une nouvelle en ne gardant que les nombres (impairs) qui ne sont pas multiples de 3.

Pour bien démarrer quand on programme il faut réfléchir au problème avant de coder.
Voici une méthode en pour clarifier l'objectif d'un exercice :

1) Quelles sont vos données de départ ? Sous quelle forme ?
2) Quel doit être votre résultat ? Sous quelle forme ?
3) Essayer de calculer **à la main** au moins le début de votre calcul pour clarifier comment faire.

4) Point supplémentaire: comment découper votre problème en sous parties autonomes (pour en faire des fonctions indépendantes)

Illustration des variables, des conditions et des boucles au tableau.


## 0. Hello world

- Définir un dossier pour vos exercices python : par exemple `Bureau/exercices_python`
- Créez un fichier `exercice_0.py`
- La façon d'organiser et de nommez les resources est très importante en programmation. Essayez de faire un fichier de code par exercice en respectant la logique du nom ci-dessus.

### Démarrer VSCode et apprivoiser son interface:
- Lancez le logiciel et faite `ouvrir un dossier` en choisissant votre dossier de code exercice. Ce dossier devient alors votre dossier de projet courant dans VSCode.
- Installez l'extension VSCode python.
- Afficher le panneau de terminal de l'éditeur. Que remarque-t-on ?
- Lancez l'interpréteur python avec la commande `python3`.
- Quittez l'interpréteur avec `CTRL+D`
- Ouvrez votre fichier de code `exercice_0.py`

- Écrire et lancer le programme suivant avec l'interpréteur Python:

```
print("Hello World!")
```

## 1. Variables

Ouvrez le logiciel `Thonny`

**1.1** : Écrire, lancer et analyser l'execution du programme suivant en utilisant la fonction debugger pas à pas de `thonny` (icone insecte) :

```python
message = "Je connais la réponse à l'univers, la vie et le reste"
reponse = 6 * 7

print(message)
print(reponse)
```

**1.2**: Dans un terminal, lancer l'interpréteur python. Calculer le résultat des opérations suivantes en le stockant dans des variables `result_1` à `result_4` et les afficher avec `print()`:

- 567×72
- 33^4
- 98.2/6
- ((7×9)^4)/6


## 2. Interactivité

**2.1** : Demander l'âge de l'utilisateur, puis calculer et afficher l'âge qu'il aura dans deux ans et afficher le résultat.

**2.2** : Calculer l'age dans deux ans, mais cette fois en demandant l'année de naissance (approximativement, sans tenir compte du jour et mois de naissance...).

## 3. Chaînes de caractères

**3.1** : Demander un mot à l'utilisateur. Afficher la longueur du mot avec un message tel que "Ce mot fait X caractères !"

**3.2** : Remplacer les lettres A et E dans le mot, et afficher le mot ainsi modifié.

**3.3** : Encadrer le mot modifié avec des `####` puis afficher le résultat sur trois lignes en une seule commande print() :

  ```
  #############
  # votre_mot #
  #############
  ```

## 4. Fonctions

**4.1** : Nous allons écrire une fonction `centrer` qui permet de centrer une chaîne de
caractère sur 80 caractères.

- Par exemple `print(centrer("Pikachu"))` affichera :

```
|                                    Pikachu                                  |
```
- ...et `print(centrer("Pika"))` affichera :

```
|                                      Pika                                   |
```


**4.1.1** Pour simplifier l'exercice procédons par étapes:

  - Afficher simplement le mot "Pikachu" centré sur 20 caractères (en comptant les `|`)
  - Combien y a-t-il d'espaces de chaque côté?
  - Trouvez la formule générale pour calculer le nombre d'espace.
  - Définir la fonction `centrer` avec le prototype suivant `def centrer(text):`
  - Pour contrôler que votre fonction est correcte, afficher également la longueur du résultat avec `print(len(centrer("mon_mot"))`

**4.1.2** : Ajouter un argument optionnel pour gérer la largeur au lieu du "80" fixé. Par exemple `print(centrer("Pikachu", 40))` affichera :
```
|                Pikachu               |
```

**4.1.3** : Créer une fonction `encadrer` qui **! réutilise !** la fonction `centrer` pour
produire un texte centré et encadré avec des `####`. Par exemple,
`print(encadrer("Pikachu", 40))` affichera :
```
########################################
#                Pikachu               #
########################################
```

**4.1.4** : Au lieu d'utiliser des `#` pour encadrer le texte, passer le caractère en argument optionnel. (Par exemple : `encadrer("Pikachu", 20, '@')`)


## 5. Conditions

**5.1.1** : Dans un nouveau fichier exercice5.py, reprendre les fonctions `centrer` et `encadrer` de l'exercice 4 et modifier la fonction encadrer pour gérer le cas où la largueur demandée est -1 : dans ce cas, ne pas centrer. Par exemple,
`print(encadrer("Pikachu", -1))` affichera :
```
###########
# Pikachu #
###########
```

**5.1.2** : Gérer le cas où le caractère d'encadrement est vide. Dans ce cas, ne pas encadrer. Par exemple : `encadrer("Pikachu", 40, '')` affichera juste :
```
|                Pikachu               |
```

**5.1.3** : Dans la fonction `encadrer`, gérer également le cas où le texte dépasse la largueur demandée. Dans ce cas, tronquer le texte. Par exemple :
`encadrer("Pikachu", 8)` affichera :
```
########
# Pika #
########
```

Trouvez de nouvelles façon de faire bugger la fonction. Conclusion ?


## 6. Exceptions, assertions


**6.1** : Ecrire une fonction `demander_nombre_pair()` qui demande un entier pair à l'utilisateur, puis vérifie que la réponse est bien un entier pair.
	- Si ce n'est pas le cas, déclencher une exception expliquant le problème (en francais).
	- Si tout est bon, la fonction renvoie l'entier entré par l'utilisateur.
	- Ajoutez un programme principal qui capture l'exception lorsque le nombre est impair et renvoie 2 à la place


**6.2** : Reprendre les fonctions `centrer` et `encadrer` et vérifier au début des
fonctions certaines hypothèses faites, comme :
- la longueur doit être un entier positif ou égal à -1
- le caractère d'encadrement doit être un caractère (chaine de longueur 1) ou vide
- le texte donné doit effectivement être une chaîne de caractère

