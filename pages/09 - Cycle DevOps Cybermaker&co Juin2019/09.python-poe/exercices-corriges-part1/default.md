---
title: 'Exercices Python - Partie 1 Corrigés'
visible: true
---

# Exercice 1.2

```python
result_1 = 567 * 72
result_2 = 33**4
result_3 = 98.2 / 6
result_4 = (7 * 9)**4 / 6

print(result_1)
print(result_2)
print(result_3)
print(result_4)
```

# Exercice 2.1


```python
age_string = input('Quel est votre age ?\n')
age_int = int(age_string)

print("Dans deux ans vous aurez")
print(age_int+2)
```


# Exercice 2.2

```python
annee = int(input('Quelle est votre année de naissance ?\n'))
age = 2019 - annee + 2
print("Dans deux ans vous aurez {} ans.".format(age))
```

## 3.1 Compter les lettres

```python
mot = input("Donnez moi un mot.\n")
print("Ce mot fait {} caractères (espaces inclus).".format(len(mot)))
```

## 3.2 Changer des lettres

```python
mot_modifie = mot.replace('a', 'z').replace('e', 'y')
print("Mot modifié : {}".format(mot_modifie))
```

## 3.3 Encadrer le mot avec ##

```python
mot_encadre = '#### ' + mot + ' ####'
print("mot encadré: {}".format(mot_encadre))
```


## Exercice 4. fonctions de centrage

```python
def centrer(mot, largeur=80):
    
	nb_espaces = largeur - len(mot) - 2
    nb_espaces_gauche = nb_espaces // 2     # division entière:  25 // 2 -> 12
    nb_espaces_droite = nb_espaces - nb_espaces_gauche

    resultat = "|" + nb_espaces_gauche * " " + mot + nb_espaces_droite * " " + "|"

    return resultat
    
    
def encadrer(mot, largeur=80, caractere='@'):
	ligne1 = caractere * largeur
    ligne2 = centrer(mot, largeur).replace("|", caractere)
    
    return "{}\n{}\n{}".format(ligne1, ligne2, ligne1)
	

print(centrer("Pikachu"))
print(len(centrer("Pikachu")) # 80
print(centrer("Pikachu", 40))

print(encadrer("Pikachu"))
print(encadrer("Pikachu", 37))
print(encadrer("Pikachu", 71, "#"))
```


## Exercice 5. conditions

```python
def centrer(mot, largeur=80):
	nb_espaces = largeur - len(mot) - 2
    nb_espaces_gauche = nb_espaces // 2     # division entière:  25 // 2 -> 12
    nb_espaces_droite = nb_espaces - nb_espaces_gauche

    resultat = "|" + nb_espaces_gauche * " " + mot + nb_espaces_droite * " " + "|"

    return resultat
    
    
def encadrer(mot, largeur=80, caractere='@'):
	if largeur == -1:
    	largeur = len(mot) + 4
	
    if caractere == '':
    	return centrer(mot, largeur)
    
    longueur_max = largeur - 4
    if len(mot) > longueur_max:
    	mot = mot[:longueur_max]
        
 	ligne1 = caractere * largeur
    ligne2 = centrer(mot, largeur).replace("|", caractere)
    
    return "{}\n{}\n{}".format(ligne1, ligne2, ligne1)
	

print(encadrer("Pikachu", -1))
print(encadrer("Pikachu", 34, ''))
print(encadrer("Pikachu", 8, '@'))



```

## Exercice 6.1

```python
def demander_nombre_pair():
    entier = int(input('Donner moi un entier pair :\n')) # ValueError si l'entrée n'est pas un entier

    if entier % 2 != 0:
        raise Exception("Ce n'est pas un entier pair !")
    return entier

if __name__ == '__main__':
    
    while True:
        try:
            result = demander_nombre_pair()
            print("ah enfin ! {}".format(result))
            break
        except Exception as e:
            print(e)
            print("Mauvaise entrée recommence")
```


## Exercice 6.2

```python
def centrer(mot, largeur=80):
    assert type(mot) is str, "pas une chaine de caractere"
    assert largeur >= 5 or largeur == -1, "largeur inadaptee"

    nb_espaces = largeur - len(mot) - 2
    nb_espaces_gauche = nb_espaces // 2 # division entière:  25 // 2 -> 12
    nb_espaces_droite = nb_espaces - nb_espaces_gauche

    resultat = "|" + nb_espaces_gauche * " " + mot + nb_espaces_droite * " " + "|"

    return resultat



def encadrer(mot, largeur=80, caractere='@'):
    assert type(mot) is str, "pas une chaine de caractere"
    assert largeur >= 5 or largeur == -1, "largeur inadaptee"
    assert type(caractere) is str and (len(caractere) == 1 or caractere == ''), "caractere inadapté"

    if largeur == -1:
        largeur = len(mot) + 4

    if caractere == '':
        return centrer(mot, largeur)

    longueur_max = largeur - 4
    if len(mot) > longueur_max:
        mot = mot[:longueur_max]
        
    ligne1 = caractere * largeur
    ligne2 = centrer(mot, largeur).replace("|", caractere)

    return "{}\n{}\n{}".format(ligne1, ligne2, ligne1)


print(encadrer("Pikachu", -1))
print(encadrer("Pikachu", 34, ''))
print(encadrer("Pikachu", 8, '@'))

#Errors

try:
    # print(encadrer("Pikachu", 4, '@'))
    print(encadrer("Pikachu", 14, '@@@'))
    # print(encadrer(3.14, 4, '@'))
except Exception as e:
    print(e)
```



