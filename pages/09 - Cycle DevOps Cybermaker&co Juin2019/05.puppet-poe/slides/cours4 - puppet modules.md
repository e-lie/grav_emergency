title: Puppet
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
-->

---

class: impact

# {{title}}
## *Un gestionnaire de configuration important*

---

# Admin sys = gérer l'état des machines

## Traditionnellement

- Monitoring sur certains signaux de la machine (disque, bon fonctionnement apparent des services, etc.)
- Installation à la main des nouvelles machines éventuellement avec des scripts d'aide.

## Problèmes

- Difficile de connaître l'état
  - généralement pas documenté car pénible et long
  - dérive inévitable du système depuis son état optimal d'installation

- Difficile de faire des infra compliquée et multi-machines de cette façon.

---

# Gestionnaires de configuration

- Infra as Code (rappel) : on décrit sous forme de code un état logiciel du système comme ça:
  - pas (trop) de dérive de la configuration (la configuration est rectifiée régilièrement)
  - on peut connaître de façon fiable l'état des composants du système
  - on peut travailler en collaboration plus facilement grace à git notamment

- Mais en fait les gestionnaires de configuration sont plus anciens que le concept d'infrastructure as code et que le DevOps.
  
- Au départ il s'agit surtout de gérer un grand parc de machine sans effort.

---

# Piloter le parc

- Les métaphores qui président à la création de Puppet et Chef : le chef cuisinier et ses commis, tirer les ficelles des marionnettes (puppet).
- Limiter au maximum le nombre de connexion ssh manuelles nécessaires.
- Pouvoir orchestrer des opérations qui impliquent plusieurs machines de façon coordonnée.


# Exprimer des opérations complexe plus facilement

- Un langage de haut niveau : éviter les petits détails du bash
- Des Dépendances entre tâches (modules puppet)
- Des abstractions au dessus des outils

---

# Architecture de puppet

- master / agent : centralisé


