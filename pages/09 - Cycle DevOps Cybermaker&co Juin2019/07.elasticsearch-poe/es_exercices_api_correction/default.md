---
title: 'Elasticsearch Exercices API Corrections'
---

# Exercices API Elasticsearch Corrections

### Créer une bibliothèque et un mapping implicite

Lorsqu'on ajoute un document de format inconnu elasticsearch crée automatiquement un index et un mapping correspondant. Ajoutons un tel document:

- Chercher un livre sur http://lalibrairie.com
- Écrire un fichier JSON pour décrire le livre avec:
  - le titre (title)
  - l’auteur (author)
  - le prix (price)
  - la première phrase de la description à mettre entre guillemets (description)
  - d’autres infos si vous voulez 
- Choisissez un nom simple pour votre bibliothèque par ex `bibli_elie`
- Ajoutez ce livre à votre bibliothèque dans Kibana avec la syntaxe:
```json
PUT /<votre_bibli>/livre/1
<DATA>
```
correction:
```json
PUT /mabibli/livre/1
{
    "title": "Awesome Thriller",
    "author": "Stephen King", // pas de guillemets
    "description": "\"Un roman haletant\"",
    "price": 9.80
}

GET /mabibli
GET /mabibli/_search
```

### Gérer les documents dans Elasticsearch.

Dans la vue Devtools et à l’aide de votre feuille de mémento de l’API :
- mettre à jour le livre que vous avez ajouté en changeant le prix
- ajouter deux nouveaux livre avec la méthode POST
- lister tous les livres de l’index
- lister les index présents sur le cluster
- supprimer le livre numéro 2 (avec son _id)

### Créer un index et un mapping explicitement
- supprimer votre index
- Cherchez dans la documentation comment ajouter un mapping
- Décrivez en JSON les propriétés suivantes pour ce mapping en choisissant les types: title, description, author, price, ISBN/EAN, weight
- Ajoutez le mapping. Indication : il faut un nouvel index d’abord (mettez 1 shard et 0 replicas)
- Recréez vos deux livres avec POST sans renseigner l’ISBN
- ajoutez l’ISBN avec PUT problème
- ajoutez un champ de type long pour régler le problème