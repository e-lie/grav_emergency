---
title: 'QCM Puppet Correction'
visible: true
---

##### Prénom : \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

##### Nom : \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


#### Entourez la bonne réponse.


**Question 1**

Puppet est un outil :

1. Très différent et complémentaire d'Ansible.
1. **V** Concurrent d'Ansible basé sur une architecture différent.
1. Concurrent d'amazon web services pour créer des application dans le cloud.

**Question 2**

Ce type d'outil s'appelle

1. Un orchestrateur de conteneurs.
1. Un langage de scripting nouvelle génération.
1. **V** Un gestionnaire de configuration.

**Question 3**

Quelle est l'architecture de Puppet ?

1. Agentless avec connexion en ssh et configuration ad-hoc.
1. **V** Master/agent avec des noeuds connectés grâce à une API REST et des signatures SSL.
1. Orchestré dans des conteneurs avec Kubernetes ou Swarm. 

**Question 4**

Puppet utilise un langage de configuration : 

1. **V** Descriptif basé sur des resources et des dépendances entre ressources.
2. Impératif basé sur des commandes et des structures de contrôle.
3. Immutable car il encourage la suppression des resource et leur recréation plutôt que leur modification.

**Question 5**

La puissance de Puppet provient notamment de:

1. **V** Son écosystème de modules répertoriés sur la forge puppet.
2. Son système automatique de génération de la configuration.
3. Sa documentation stable et éprouvée.
3. Sa communauté libriste très impliquée.

**Question 6**

Comment appliquer manuellement la configuration sur un agent puppet:

1. **V** `sudo puppet agent --test`
1. `tail -f /var/log/syslog`
1. `sudo puppetserver apply`

**Question 7**

Un environnement puppet est :

1. Un kit de développement pour créer des modules puppet et les tester facilement.
1. Un environnement shell qu'on active comme un environnement virtuel python.
1. **V** Un ensemble de configuration correspondant à un contexte de l'infrastructure.


**Question 8**

La configuration standard master/agent de puppet implique que:

1. **V** Le démon/service installé sur les agents contacte régulièrement le master pour mettre à jour la configuration.
1. Le master prend l'iniative de pousser la configuration sur les clients dans le bon environnement.
1. Le démon/service installé sur les agents contacte un dépôt git pour récupérer sa configuration directement.

**Question 9**

Les logs de configuration de puppet sont par défaut stockés dans:

1. **V** Simplement dans `/var/log/syslog` sur chaque noeud agent.
2. Dans la base elasticsearch elasticsearch installée avec puppet.
3. Dans un dossier spécial de puppet qu'on peut consulter avec `puppet logs`

**Question 10**

Un template dans puppet est généralement:

1. **V** Un fichier (texte) erb ou epp dont certaines parties sont complétées au moment de l'application du catalogue.
1. Un fichier xml qui permet de préconfigurer la création d'un module pour la forge.
1. Un fichier jinja qui permet de d'interfacer avec un cloud comme AWS ou DigitalOcean.