---
title: 'Memento Docker'
visible: true
---


### Manipulation de conteneurs

Utilisez `--help` la CLI Docker est très vaste mais pas très compliquée (régulière).

| Commande                                   | Effet                                                |
| ------------------------------------------- | ------------------------------------------------------------ |
| `docker ps`            |  Lister les conteneurs en train de tourner  |
| `docker ps -a`  |  Lister les conteneurs arrêtés et tournants |
| `docker run <image> [<command>]`| récupérer et lancer un nouveau conteneur a partir d'une image et éventuellement avec une commande à lancer dans le conteneur  |
| `docker pull <image>[:<version>]`|  récupérer une image depuis un registry (docker hub par défaut) sans la lancer     |
| `docker run --rm --name <nom_conteneur> <image>`  | lancer une image avec un nom et en mode suppression automatique (lorsqu'on arrête le conteneur ) |
| `docker stop <num_ou_nom_conteneur>` | arrêter un conteneur |
| `docker kill <num_ou_nom_conteneur>` | arrêter de force un conteneur récalcitrant |
| `docker rm <num_ou_nom_conteneur>`       | supprimer un conteneur arrêté (`--force` pour supprimer un conteneur allumé)     |
| `docker image ls`         		    |   lister les images                        |
| `docker image rm <id_ou_nom_image>`   | Supprimer une image                  |
