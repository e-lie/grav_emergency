
title: Introduction Python et Git
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
-->

---

# 10. Fichiers

## 10.1 Lire

```python
with open("/etc/passwd", "r") as f:
    toutes_les_lignes = f.readlines()

for ligne in toutes_les_lignes:
    print(ligne)
```

### Explications

- `open("fichier", "r")` ouvre un fichier en lecture
- `with ... as ...` ouvre un contexte, à la fin duquel le fichier sera fermé
- `f.readlines()` permet d'obtenir une liste de toutes les lignes du fichier

---

# 10. Fichiers

## 10.1 Lire

- `f.readlines()` renvoie une **liste** contenant les lignes une par une
- `f.read()` renvoie une (grande) **chaĩne** contenant toutes les lignes concaténées

---

# 10. Fichiers

## 10.2 Ecrire

### En remplacant tout !

```python
with open("/home/elie/test", "w") as f:
    f.write("Plop")
```

### À la suite (« append »)

```python
with open("/home/elie/test", "a") as f:
    f.write("Plop")
```

---

# 10. Fichiers

## 10.3 Fichiers et exceptions

```python
try:
    with open("/some/file", "r") as f:
        lines = f.readlines()
except:
    # problème lors de l'ouverture du fichier 
    traiter_le_probleme()
```

---

# 10. Exceptions, assertions

### 10.3 Fichiers et exceptions (autre exemple)

```python
try:
    with open("/etc/shadow", "r") as f:
        lines = f.readlines()
except PermissionError:
    demander_les_permission_ou_ouvrir_un_autre_fichier()
except FileNotFoundError:
    creer_le_fichier()
```

---