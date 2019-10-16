---
title: 'Initiation au shell'
---

Les slides de cours seront mis en ligne au fur et a mesure [ici](eliegavoty.fr/documents).

Pour vous familiariser plus largement à Linux en autonomie vous pouvez suivre [ce cours openclassroom sur linux et la ligne de commande](https://openclassrooms.com/fr/courses/43538-reprenez-le-controle-a-laide-de-linux).



### Exercices à faire en priorité

Les énoncés des exercices sont trop longs pour être faits sur deux jours.
Pour avoir un bon aperçu du shell scripting faites en priorité les questions:

4.11 - 5.1 - 5.2 - 5.3 - 5.4 - 5.5 - 5.6 - 6.1 - 6.2 - 6.3 - 8.1 - 8.2 - 8.6 - 9.1 - 9.3 - 9.9 - 9.10 - 9.14 - 9.15 - 10.1 - 10.3 - 10.4 - 10.5

### Application concrête du shell linux : tester elasticsearch et kibana avec docker

Docker est un logiciel de conteneurs très intégré sous linux. Il permet notamment de lancer facilement des logiciels complexes pour les tester.

- Installez docker avec `yaourt docker` choisissez le bon numéro (demandez si vous n'êtes pas sur)
- Lisez la [documentation officielle pour installer elasticsearch avec docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html).
- Trouvez comment lancez elasticsearch avec docker (un des premiers exemple de commande de la documentation)
- Affichez la liste des conteneurs en train de tourner avec `docker ps`
- Lancez un deuxième conteneur kibana avec `docker run docker.elastic.co/kibana/kibana:7.0.0`
- Attendez quelques secondes (30?) que le conteneur soit bien lancé puis visitez dans un navigateur [http://localhost:5601](http://localhost:5601)
- A la fin arrêtez les conteneurs avec `docker stop <num_conteneur>` récupérez le numéro avec `docker ps`.