---
title: 'Exercices Python - Partie 2 Corrigés'
visible: true
---

## Exercice 7.1, 7.2, 7.3, 7.4

```python
def table_de_multiplication(nombre):

    assert isinstance(nombre, int)
    
    resultat = '| Table de {} \n--------------\n'.format(nombre)

    for n in range(1,11):
        resultat += '| {} x {} = {} \n'.format(n, nombre, n * nombre)

    return resultat

if __name__ == '__main__':
    
    while True:
        mdp = input('Entrez le mot de passe\n')

        if mdp == 'pyth0ng1t':
            for n in range(1, 11):
                print(table_de_multiplication(n))

            break
```


# Exercice 7.5

```python
def is_prime(nombre):
    for n in range(3, nombre):
        if nombre % n == 0:
            return False
    return True
```


# Exercice 7.6

```python
def fibo(maximum):
    fib_a = 1
    fib_b = 1
    result = '{} '.format(fib_a)
    for n in range(1, maximum):
        result += '{} '.format(fib_b)
        fib_c = fib_a + fib_b
        fib_a = fib_b
        fib_b = fib_c
    return result

if __name__ == '__main__':
    print(is_prime(73))
    print(is_prime(60))

    n = int(input('nombre de terme pour fibonacci:\n'))
    print(fibo(n))
```


# Exercice 7.7

```python
def afficher_allumettes(n):
    allumettes = "nombre d'alumette restante: {}  {}".format(n * '|', n)
    print(allumettes)

def choisir_nombre():
        nombre = int(input("Combien d'allumettes voulez vous retirer ? Choisir un nombre entre 1 et 3 :\n"))
        assert 1 <= nombre <= 3, "Vous devez choisir un nombre entre 1 et 3"
        return nombre

def partie_allumettes(total):

	joueur_courant = True
    while True:
    	print("Joueur courant: {}".format(int(joueur_courant) + 1))
        afficher_allumettes(total)
        
        try:
            choix = choisir_nombre()
        except AssertionError as e:
            print(e)
            continue
        
        total -= choix
        if total <= 0:
        	break
        
		joueur_courant = not joueur_courant
    
    print("la partie est terminée, le joueur {} a perdu".format(int(joueur_courant) + 1))
        
        
if __name__ == '__main__':
	nb_initial = int(input("Combien d'alumette pour la partie ?  ")
    partie_alumette(nb_initial)
```

# Exercice 7.8

```python
def play(allumettes_restantes):
    if 9 > allumettes_restantes > 5:
        return allumettes_restantes - 5
    elif allumettes_restantes == 5:
        return 2
    elif 5 > allumettes_restantes > 1:
        return allumettes_restantes - 1
    else:
        return 1
        
            
partie_allumettes(17)
```


## Exercices 8.1.1, 8.1.2

```python
def plus_grand(l):
    try:
        assert l
        assert isinstance(l, list)
    except AssertionError:
        raise Exception("le parametre de plus_grand doit être une liste non vide")

    plus_grand = l[0]
    for n in l:
        if n > plus_grand:
            plus_grand = n
    return plus_grand
    
print(plus_grand([5, 9, 12, 6, -1, 4]))
```

# Exercice 8.2 

```python
def somme(l):
    total = 0
    for n in l:
        total += n
    return total
    
print(somme([1,9,4,8]))
```

# Exercice 8.3

```python
def somme_recursive(l):
    if len(l) == 1:
        return l[0]
    else:
        return l[0] + somme_recursive(l[1:])
        
print(somme_recursive([1,9,4,8]))
```

# Exercice 8.4

```python
def elem_pairs(l):
    return [elem for elem in l if elem % 2 == 0]
```


# Exercice 8.5

```python
# tri par insertion plusieurs version possible
# Version 1 itérative:
def tri_insertion_1(liste):
    for i in range(1, len(liste)): # on parcoure la liste de gauche à droite
        pivot = liste[i] # pivot = nombre courant à ranger ; on le sauvegarde
        j = i
        while j > 0 and liste[j - 1] > pivot: # j parcoure les cases vers la gauche. Tant que le pivot est plus petit ... 
            liste[j] = liste[j - 1] # ... on décale les nombres de gauche (déjà triés) vers la droite de 1
            j -= 1
        liste[j] = pivot # quand on a trouvé la place du pivot on l'insère
    return liste

print(tri_insertion_1([4,6,3,5,9,1,7]))

# Version 2 itérative:

def tri_insertion_2(liste):
    liste_triee = [liste[0]]
    for pivot in liste[1:]:
    	for indice, elem in enumerate(liste_triee):
            if pivot < elem:
            	liste_triee.insert(indice, pivot)
                break
            if indice == len(liste_triee) - 1:
            	liste_triee.append(pivot)
                break
  	return liste_triee

print(tri_insertion_2([4,6,3,5,9,1,7]))
```