---
title: 'Git - Partie3'
---

# Partie3 : Développer de façon collaborative avec la forge logicielle gitlab.

Dans ce TP vous allez travailler par binôme sur le tutoriel flask de Miguel Grindberg : https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates

### Créer un compte sur framagit et pousser un projet

Framagit est une forge libre basée sur `gitlab` (et qui respecte votre vie privée).

- Rendez-vous sur [https://framagit.org](https://framagit.org) pour créer un compte.
- Créez ensuite un nouveau projet `webcalculator` pour héberger le projet de la partie 2.
- Rendez vous dans le dossier du projet en terminal et suivez les instructions gitlab pour pousser votre dépôt existant

### Reprise du tutoriel Flask

Le tutoriel a des chapitres. Le but du TP consistera a travailler à deux sur un chapitre avec un.e qui code et l'autre qui relit le code, suit le tutoriel et conseille et le/la codeur.e. Ce principe est très proche d'une méthodologie de développement agile nommée XP (extreme programming):

- A chaque chapitre le code sera :
	- Ajouté dans une nouvelle branche.
	- Poussé sur un projet framagit partagé.
- Le code sera revu par la personne qui n'a pas codé grâce à une **merge request** puis sera fusionnée (merge) dans la branche master (Cf cours).
- Pour le chapitre suivant la personne qui n'a pas codé récupère la dernière version du code.

### Merge

Les fusions de branche peuvent s'effectuer en local sur la machine ou sur la forge logicielle.

- Les deux premiers chapitres seront à merger en local et les deux suivants sur framagit.