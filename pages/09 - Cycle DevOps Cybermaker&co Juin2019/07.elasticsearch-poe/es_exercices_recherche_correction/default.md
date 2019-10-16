---
title: 'Exercices Recherche et Visualisation - Correction'
visible: false
---

Pour ces exercices nous nous baserons sur les données d'exemples de kibana qui sont très complètes et générées en live pour ressembler à des données de monitoring.

Une fois que vous avez installé elasticsearch et kibana en suivant le TP1 chargez la page kibana (192.168.2.4:5601) puis ajoutez les données d'exemple.

### Recherches dans Kibana

Dans la vue `Discover` pour les logs de vols:
- De quand date les plus veilles données en base ?
```
=> Jouer avec le Time Range en haut à droite
=> Essayer `Last Year` puis réduire jusqu'à pouvoir distinguer correctement le début des données.
```

- Tous les avions en provenance de `New York` (depuis le début des données).
```
=> changer la période à `This month`.
OriginCityName:"New York"
```

- Tous les avions à destination de `Seattle` qui ont eu du retard (depuis le début des données).
```
=> changer la période à `This month`.
OriginCityName:"Seattle" AND FlightDelay:true
```

- Tous les avions provenant d'une ville avec "New" dans le nom. Que remarque-t-on ?


Dans la vue `Discover` pour les logs d'application.

- L'ensemble des requête provenant d'un navigateur "Mozilla"
```
=> Les champs `agent` et `message` sont des champs de type `fulltext` donc chercher un mot suffit: Mozilla
```

Dans la vue `Discover` pour les logs de vols:
- La quantité d'avions n'ayant pas eu du retard hier soir entre 21h30 et 22h.
```
=> utilisez le Time Range en mode absolu pour définit la période
=> cherchez: FlightDelay:false

=> Autre méthode cherchez:
FlightDelay:false AND timestamp:[2019-07-27T19:30:00.000Z TO 2019-07-27T20:00:00.000Z]
=> les heure sont à convertir en temps universel (UTC)
```


### Créer des visualisations

- Aller dans la section dashboard des données de vols. Explorer les différentes visualisations pour comprendre de quoi elles parlent.

- Le prix moyen des billets par companie sous forme de camembert.

```
=> Créez une visualisation de type `Heat Map`
=> Sélectionnez les données de vol kibana
=> Conservez `count` sur l'axe Y
=> Sur l'axe X ajoutez un bucket de type histogram
=> Utilisez le champ `DayOfWeek`
```

- Créez un un histogramme (diagramme en barre) du nombre de vol par jour de la semaine.`

```
=> Créez une visualisation de type `Vertical Bar`
=> Sélectionnez les données de vol kibana
=> Conservez `count` sur l'axe Y
=> Sur l'axe X ajoutez un bucket de type histogram
=> Utilisez le champ `DayOfWeek`
```

- Créez une nouvelle DashBoard avec vos deux widgets

### Pour aller plus loin

Pour créer des visualisation plus complexes, inspirez vous des visualisations d'exemple des dashboards.