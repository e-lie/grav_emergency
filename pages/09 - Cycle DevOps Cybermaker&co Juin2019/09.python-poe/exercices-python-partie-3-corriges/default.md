---
title: 'Exercices Python - Partie 3 - Corrigés'
visible: true
---

## Exercice 9.2

```python
class TableDeMultiplication:

    def __init__(self, base=1, longueur=10):
       if base < 1:
           raise TypeError
       
       # les attributs dont le nom commence par un underscore sont conventionnellement privés
       # On ne doit pas y accéder directement mais à l'aide des méthodes (publiques) disponibles
       self._base = base
       self._longueur = longueur
       self._resultats = TableDeMultiplication._calculate_results(self._base, self._longueur)

    @classmethod
    def _calculate_results(cls, base, longueur):
        return [ i * base for i in range(1,longueur+1) ]

    def get_base(self):
        return self._base

    def __getitem__(self, position):
        return self._resultats[position]

	# fonction de représentation texte de l'objet (affichage)
    def __repr__(self):
        return "TableDeMultiplication de {}".format(self._base)
	
    # fonction d'affichage (si existe remplace __repr__ dans le cas des print et conversion en string)
    def __str__(self):
        print("méthode d'affichage de TableDeMultiplication")
        res = "TableDeMultiplication de {}\n -------------------\n".format(self._base)
        for index, result in enumerate(self._resultats):
            res += "{} x {} = {}\n".format(index+1, self._base, result)
        return res

	# Fonction pour définir la longueur des objets de la classe de façon pythonique (avec len(obj) )
    def __len__(self):
        return self._longueur

	# Fonction getter pour une propriété de la classe : cette fonction est appelée dès qu'on accède à la valeur longueur (propriété publique)
    @property
    def longueur(self):
        print("j'accède à la longueur")
        return self._longueur

	# Fonction setter pour définir la longueur et faire toutes les modifications afférantes nécessaires
    @longueur.setter
    def set_longueur(self, longueur):
        if longueur < 1:
            raise TypeError
        print("Je modifie la longueur")
        self._longueur = longueur
        self._resultats = TableDeMultiplication._calculate_results(self._base, self._longueur)
    
    # Autre méthode pour la property
    # longueur = property(get_longueur, set_longueur)

	# Définit l'opération d'addition de deux tables
    def __add__(self, table2):
        return TableDeMultiplication(
            self._base + table2._base,
            self._longueur + table2._longueur
        )

if __name__ == "__main__":
    table = TableDeMultiplication(2)
    print(len(table))
    print(table.longueur)
    print(table)
    print(table[3])
    print(table[3:10])
    print(table + TableDeMultiplication(3,3))
```


## Exercice 10.1

```python

def ouvrir_fichier(chemin):
    with open(chemin, 'r') as fichier:
        toute_les_lignes = fichier.readlines()

    result = "".join(toute_les_lignes)

    return result


if __name__ == '__main__':
    print(ouvrir_fichier("libraries.py"))
```

## Exercice 10.2

```python
def remplacer_dans_fichier(chemin, mot_a_remplacer, nouveau_mot):
    with open(chemin, 'r') as fichier:
        toute_les_lignes = fichier.readlines()

    lignes_remplacees = [ ligne.replace(mot_a_remplacer, nouveau_mot) for ligne in toute_les_lignes ]
    result = "".join(lignes_remplacees)

    with open(chemin, 'w') as fichier:
        fichier.write(result)

if __name__ == '__main__':
    remplacer_dans_fichier("monfichier.py", "mon_mot", "nouveau_mot")
```