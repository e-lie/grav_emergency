---
title: 'Cours 1 - Introduction, installation et commandes ad-hoc'
visible: true
---

## Plan

- Introduction

- Module 1 : Installer ansible, configurer la connexion et commandes ad hoc ansible
  - commande ansible, idempotence, ansible-console
  - créer un lab avec LXD
  - configuration de la connexion
  - gérer des groupes de machines.
  - modules de base
  
- Module 2 : Les playbooks pour déployer une application web
  - modules de déploiement et configuration.
  - variables
  - structures de controle
  - Templates de configuration
  - debugging

- Module 3 : Structurer un projet, utiliser les roles
  - Ansible Galaxy pour installer des roles.
  - Organisation et création d'un role.
  - Imports et includes.

- Module 4 : 
  - TP4 : Application multitiers avec loadbalancer simple.
    - Organisation d'un projet.
    - Variables avancées.
    - Delegate, déployer un logiciel de façon orchestrée.

  - TP5 : Ansible Tower/AWX
    - Sécurité avec Vault.
    - Explorer AWX

  - TP6 : Déployer dans le cloud avec terraform et Ansible
    - Inventaires dynamiques.
    - Terraform comme provisionneur.
    - Intégrer Terraform et Ansible.

# Partie 1

## Introduction

### Ansible

Ansible est un **gestionnaire de configuration** et un **outil de déploiement et d'orchestration** très populaire et central dans le monde de l'**infrastructure as code** (IaC).

Il fait donc également partie de façon centrale du mouvement **DevOps** car il s'apparente à un véritable **couteau suisse** de l'automatisation des infrastructures.

### Le mouvement DevOps

Le DevOps est avant tout le nom d'un mouvement de transformation professionnelle et technique de l'informatique.

Ce mouvement se structure autour des solutions humaines (organisation) et techniques (nouvelles technologies de rupture) apportées pour répondre aux défis que sont:

- L'agrandissement rapide face à la demande des services logiciels et infrastructures les supportant.
- La célérité de déploiement demandée par le développement agile (cycles journaliers de développement).
- Difficultées à organiser des équipes hétérogènes de grande taille et qui s'agrandissent très vite selon le modèle des startups.

Il y a de nombreuses versions de ce que qui caractérise le DevOps mais pour résumer:

Du côté humain:

  - Application des process de management agile aux opérations et la gestion des infrastructures (pour les synchroniser avec le développement).
  - Réconciliation de deux cultures divergentes (Dev et Ops) rapprochant en pratique les deux métiers du développeur et de l'administrateur système.
  - 

Du côté technique:

  - Les conteneurs (Docker surtout mais aussi LXC/LXD): plus léger que la virtualisation = permet d'isoler chaque service dans son "OS" virtuel sans dupliquer le noyau.
  - Le cloud (Infra as a service, Plateforme as a Service, Sofware as a service) = fluidifier l'informatique en alignant un niveau d'abstraction avec sa structuration économique.
  - L'intégration et le déploiement continus des logiciels/produits.
  - L'infrastructure as code: gestion sous forme de code de l'état des infrastructures d'une façon le plus possible déclarative.


### L'infrastructure as code (IaC)

Il s'agit comme son nom l'indique de gérer les infrastructures en tant que code: des fichiers textes avec une logique algorithmique/de données et suivis grâce à un gestionnaire de version (git).

Le problème identifié que cherche a résoudre l'IaC est un écheveau de difficulées pratiques rencontrée dans l'administration système traditionnelle:

1. Connaissance limité de l'état courant d'un système lorsqu'on fait de l'**administration ad-hoc** (manuelle avec des commandes unix/dos).
  - Dérive progressive de l'état des systèmes et difficultés à documenter leur états.
  - Fiabilité limitée et risque peu maîtrisé lors de certaines opérations transversales. (si d'autres méchanismes de fiabilisation n'ont pas été mis en place).
  - Problème de communication dans les grandes équipes car l'information est détenue implicitement par quelques personnes.

2. Faible reproductibilité des systèmes et donc difficultée/lenteur du passage à l'échelle (horizontal scaling).
  - Multiplier les serveurs identiques est difficile si leur état est le résultat d'un processus manuel.
  - Difficulté à reproduire/simuler l'état précis de la production dans les contextes de tests logiciels.

3. Difficultés du travail collaboratif dans de grandes équipes avec plusieurs culture (Dev vs Ops) lorsque les rythmes et les modes de travail diffèrent
  - L'IaC permet de tout gérer avec git et des commits.
  - L'IaC permet aux Ops qui ne le faisait pas de se mettre au code et aux développeur de se confronter plus facilement.
  - L'IaC permet d'accélérer la transformation des infrastructures pour l'aligner sur la livraison logicielle quotidienne (idéalement ;) )

### Ansible

Ansible a été créé en 2012 (plus récent que ses concurrents Puppet et Chef) autour d'une recherche de simplicité et du principe de configuration **agentless**.

Très orienté linux/opensource et versatile il obtient rapidement un franc succès et s'avère être un couteau suisse très adaptés à l'automatisation DevOps et Cloud dans des environnements très hétérogènes.

Red Hat rachète Ansible en 2015 et développe un certain nombre de produits autour (Ansible Tower, Ansible container avec Openshift). 


### Architecture : simplicité et portabilité avec ssh et python

![](../../../images/ansible_overview.jpg)

Ansible est agentless c'est à dire qu'il ne nécessite aucun service/daemon spécifique sur les machines à configurer.

La simplicité d'Ansible provient également du fait qu'il s'appuie sur des technologies linux omniprésentes et devenues universelles.

- ssh -> connexion et authentification classique avec les comptes présents sur les machines.
- python -> multiplateforme, un classique sous linux, adapté à l'admin sys et à tous les usages.
- git -> ansible c'est du code donc on versionne dans des projets gits.

De fait Ansible fonctionne efficacement sur toutes les distributions linux, debian, centos, ubuntu en particulier (et maintenant également sur Windows).

### Ansible pour la configuration

Ansible est **semi-déclaratif** c'est à dire qu'il s'exécute **séquentiellement** mais idéalement de façon **idempotente**.

Il permet d'avoir un état descriptif de la configuration:

- qui soit auditable
- qui peut évoluer progressivement
- qui permet d'éviter que celle-ci ne dérive vers un état inconnu

Ansible est au départ plus versatile que Puppet ou Chef car il est hybride et peut s'utiliser selon un mode plutôt script ou plutôt déclaratif.

Il est donc:
- adapté à la gestion de configuration classique (en utilisation plannifiée cron/tower).
- adapté aux déploiements (utilisation ponctuelle) et à la CI/CD.

### Ansible pour le déploiement et l'orchestration

Peut être utilisé pour des opérations ponctuelles comme le déploiement:
    - vérifier les dépendances et l'état requis d'un système
    - récupérer la nouvelle version d'un code source
    - effectuer une migration de base de données (si outil de migration)
    - tests opérationnels (vérifier qu'un service répond)

Permet d'exécuter des opérations transversales synchronisées sur plusieurs machines:

- comme sortir une ou plusieurs machines d'un pool de machines
- mettre à jour les dépendances d'une application
- redéployer le code de l'application sur ces machines
- remettre la machine dans le pool.

### Ansible à différentes échelles

Les cas d'usages d'Ansible vont de ...:

- petit:
  - ... un petit playbook (~script) fournit avec le code d'un logiciel pour déployer en mode test.
  - ... la configuration d'une machine de travail personnelle.
  - etc.

- moyen:
  - ... faire un lab avec quelques machines.
  - ... déployer une application avec du code, une runtime (php/jav etc) et une base de données à migrer.
  - etc.
  
- grand:
  - ... gestion de plusieurs DC avec des produits multiples.
  - ... gestion multi-équipes et logging de toutes les opérations grâce à Ansible Tower.
  - etc.

### Comparaison d'Ansible avec les technologies d'IaC les plus connues

- Docker et Kubernetes: gestion des infrastructures par construction de boîtes immutables et leur orchestration déclarative.
- Terraform: gestion des infrastructures dans le cloud, mais pas adapté à la configuration/déploiement logicielle (complémentaire de Ansible cf. TP).
- Puppet/Chef/Saltstack: des concurrents directs (gestionnaires de configuration) mais originellement moins versatiles
  - Versatilité du à l'architecture agentless d'Ansible.
  - Salt et Puppet proposent des solutions agentless également depuis quelques temps mais pas par défaut.

## Ansible et Docker

Ansible est très complémentaire à docker:

Il permet de provisionner des machines avec docker ou kubernetes installé pour ensuite déployer des conteneurs.

Il permet une orchestration simple des conteneur avec le module `docker_container`.

Plus récemment avec l'arrivé d'`Ansible container`  il est possible de construire et déployer des conteneurs docker avec du code ansible. Cette solution fait partie de la stack Red Hat Openshift. Concrêtement le langage ansible remplace (avantageusement ?) le langage Dockerfile pour la construction des images Docker.


# Partie 1, Installation, configuration et commandes ad hoc.

Pour l'installation plusieurs options sont possibles:
  - Avec le gestionnaire de paquet de la distribution ou homebrew sur OSX:
    - version généralement plus ancienne (2.4 ou 2.6)
    - facile à mettre à jour avec le reste du système
    - Pour installer une version récente on il existe des dépots spécifique à ajouter: exemple sur ubuntu: `sudo apt-add-repository --yes --update ppa:ansible/ansible`
  - Avec `pip` le gestionnaire de paquet du langage python: `sudo pip3 install`
    - installe la dernière version stable (2.8 actuellement)
    - commande d'upgrade spécifique `sudo pip3 install ansible --upgrade`
    - possibilité d'installer facilement une version de développement pour tester de nouvelles fonctionnalité ou anticiper les migrations.

Pour voir l'ensemble des fichier installé par un paquet `pip3` :

`pip3 show -f ansible | less`

Pour tester la connexion aux serveurs on utilise la commande ad hoc suivante. `ansible all -m ping`


### Faire des lab DevOps : Vagrant+virtualbox, LXD ou Terraform et le cloud.

Pour faire des labs on veut pouvoir décrire un ensemble de machines virtuelles, les créer et les détruires rapidement.

La solution classique pour cela est vagrant qui permet de décrire dans un Vagrantfile des machines et de piloter par exemple virtualbox pour créer ces machines virtuelles.

Nous utiliserons une alternative linux assez différentes: des conteneurs LXC pilotés avec le démon LXD.
  - plus légers car des conteneurs (beaucoup moins de ram utilisée pour un lab normal)
  - seulement sur linux

Il est également très indiqué de faire des labs dans le cloud en louant des machines à la volée.
Pour cela nous intégrerons `Terraform` et `Ansible` avec le provider DigitalOcean.

### Les inventaires statiques

Il s'agit d'une liste de machines sur lesquelles vont s'exécuter les modules Ansible. Les machines de cette liste sont:
    - Classées par groupe et sous groupes pour être désignables collectivement (exp executer telle opération sur)
    - La méthode connexion est précisée soit globalement soit pour chaque machine.
    - Des variables peuvent être définies pour chaque machine ou groupe pour contrôler dynamiquement par la suite la configuration ansible.

exemple:

```ini
[all:vars]
# ansible_connection=lxd
ansible_ssh_user=elie
ansible_python_interpreter=/usr/bin/python3

[awx_nodes]
awxnode1 node_state=started ansible_host=10.164.210.101 container_image=centos_ansible_20190901

[dbservers]
pgnode1 node_state=started ansible_host=10.164.210.111 container_image=centos_ansible_20190901
pgnode2 node_state=started ansible_host=10.164.210.112 container_image=centos_ansible_20190901

[appservers]
appnode1 node_state=started ansible_host=10.164.210.121 container_image=centos_ansible_20190901
appnode2 node_state=started ansible_host=10.164.210.122 container_image=centos_ansible_20190901
```

Les inventaires peuvent également être au format YAML (plus lisible mais pas toujours intuitif) ou JSON (pour les machines).


### Configuration

Ansible se configure classiquement au niveau global dans le dossier `/etc/ansible/` dans lequel on retrouve en autre l'inventaire par défaut et des paramètre de configuration.

Ansible est très fortement configurable pour s'adapter à des environnement contraints.
Liste des paramètre de configuration:

Alternativement on peut configurer ansible par projet avec un fichier `ansible.cfg` présent à la racine. Toute commande ansible lancée à la racine du projet récupère automatiquement cette configuration.


### La commande `ansible`

- version minimale : 
`ansible <groupe_machine> -m <module> -a <arguments_module>`

- `ansible all -m ping`: Permet de tester si les hotes sont joignables et ansible utilisable (SSH et python sont présents et configurés).

- version plus complète :
`ansible <groupe_machine> --inventory <fichier_inventaire> --become -m <module> -a <arguments_module>`


### Les modules Ansible

Ansible fonctionne grâce à des modules python téléversés sur sur l'hôte à configurer puis exécutés. Ces modules sont conçu pour être cohérents et versatiles et rendre les tâches courante d'administration plus simple.

Il en existe pour un peu toute les tâches raisonnablement courante : un slogan Ansible "Batteries included" ! Plus de 1300 modules.


- `ping`: un module de test Ansible (pas seulement réseau comme la commande ping)

- `yum/apt`: pour gérer les paquets sur les distributions basées respectivement sur Red Hat ou Debian.

`... -m yum -a "name=openssh-server state=present` 
  
- `systemd` (ou plus générique `service`): gérer les services/daemons d'un système.

`... -m systemd -a "name=openssh-server state=started` 

- `user`: créer des utilisateurs et gérer leurs options/permission/groupes

`... -m yum -a "name=openssh-server state=present` 

- `file`: pour créer, supprimer, modifier, changer les permission de fichiers, dossier et liens.

- `shell`: pour exécuter des commandes unix grace à un shell

### Option et documentation des modules

La documentation des modules Ansible se trouve à l'adresse [https://docs.ansible.com/ansible/latest/modules/<nom_module>.html](https://docs.ansible.com/ansible/latest/modules/<nom_module>.html)

Chaque module propose de nombreux arguments pour personnaliser son comportement:

exemple: le module `file` permet de gérer de nombreuses opérations avec un seul module en variant les arguments.

Il est également à noter que la plupart des arguments sont facultatifs.

- cela permet de garder les appel de modules très succints pour les taches par défaut
- il est également possible de rendre des paramètres par défaut explicites pour augmenter la clarté du code.

Exemple et bonne pratique: toujours préciser `state: present` même si cette valeur est presque toujours le défaut implicite.


### La console `ansible-console`

Pour exécuter des commandes ad-hoc ansible propose aussi un interpréteur spécifique avec la commande `ansible-console`:

```bash
ansible-console --become webservers`

Welcome to the ansible console.
Type help or ? to list commands.

elie@webservers (2)[f:5]# ping
app1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
app2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

- Une fois loggué sur un groupe de serveur, on peut y exécuter les même commandes qu'avec `ansible` an fournissant les arguments à la suite.

- Exemple: `systemd name=nginx state=started`


# TP1