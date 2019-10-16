---
title: 'Exercices Java Avancés'
visible: true
---

Voici deux exercices de développement Java plus complexes.

Vous récupérez un fichier JSON contenant une liste de personnel d'une entreprise avec les liens hérarchiques :
```json
{
    "Allan": {
        "annee arrivee": 2006,
        "supérieur": "Benjamin"
    },
	"Benjamin": {
        "annee arrivee": 2005,
        "supérieur": "Hadrien"
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

## Exercice 1

Écrivez un programme qui lit ce Fichier JSON insère ses données dans une base de donnée Mysql ou mieux PostgreSQL grâce à JDBC.

Pour lire un fichier JSON vous pouvez utilisez les classes **org.json.*** comme dans cet exemple:
[https://crunchify.com/json-manipulation-in-java-examples/](https://crunchify.com/json-manipulation-in-java-examples/)

Pour installer les dépendances de votre programme ( JSON et JDBC) utilisez Gradle.
Pour démarrer un projet Gradle suivez par exemple ce tutoriel:
[https://spring.io/guides/gs/gradle/](https://spring.io/guides/gs/gradle/)

Pour installer le driver mysql avec Gradle voir:
[https://mariadb.com/kb/en/library/java-connector-using-gradle/](https://mariadb.com/kb/en/library/java-connector-using-gradle/)

Exemple d'usage de JDBC avec mysql:
https://stackoverflow.com/questions/38427176/jdbc-connection-msql-error-no-suitable-driver-found

## Exercice2

Ecrivez un programme qui lit le JSON ou la base de donnée et vérifie que les données hiérarchiques sont cohérentes, en particulier qu'il n'y a pas de cycles dans le graphe des personnels.

Pour cela vous créerez une classe **grapheOrienté** qui stocke une matrice d'adjacence des personnels.

Astuce : la façon la plus simple de détecter les cycles dans un graphe consiste à le **parcourir en largeur** en **coloriant** les noeuds visités. Si le parcours revient sur un noeud colorié c'est qu'il y a un cycle.


