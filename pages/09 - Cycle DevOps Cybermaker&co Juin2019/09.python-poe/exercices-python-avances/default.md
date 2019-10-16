---
title: 'Exercices Python Avancés'
visible: true
---

## Programme réaliste : une application web avec le framework flask

Suivez le magnifique [Flask Mega Tutorial](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world) (en anglais) de Miguel Grindberg.

C'est un projet assez long qui permet de construire pas à pas un micro twitter. La correction de chaque partie est disponible en ligne sur github.

## Algorithmique avancée : parcours de graphes

Vous récupérez un fichier JSON contenant une liste de personnel d'une entreprise avec les liens hérarchiques :
```json
{
    "Allan": {
        "annee arrivee": 2006,
        "supérieur": "Benjamin"
    },
	"Benjamin": {
        "annee arrivee": 2005,
        "supérieur": "Adrien"
    },
    "Carine": {
        "annee arrivee": 2011,
        "supérieur": "Benjamin"
    },
   	"Danielle": {
        "annee arrivee": 2006,
        "supérieur": "Allan"
    },
    "Hadrien": {
        "annee arrivee": 1999,
        "supérieur": "Gérard"
    },
   	"Émilie": {
        "annee arrivee": 2006,
        "supérieur": "Allan"
    },
   	"Farid": {
        "annee arrivee": 2002,
        "supérieur": "Allan"
    },
   	"Gérard": {
        "annee arrivee": 2002,
        "supérieur": "Farid"
    }
 }
```

Ecrivez un programme qui lit le JSON et vérifie que les données hiérarchiques sont cohérentes, en particulier qu'il n'y a pas de cycles dans le graphe des personnels.

Astuce : la façon la plus simple de détecter les cycles dans un graphe consiste à le **parcourir en largeur** en **coloriant** les noeuds visités. Si le parcours revient sur un noeud colorié c'est qu'il y a un cycle.


