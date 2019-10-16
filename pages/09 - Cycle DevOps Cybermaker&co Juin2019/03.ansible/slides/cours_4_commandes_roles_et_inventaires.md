title: Ansible 
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
## *Infra as Code, DevOps for real !*

---

# Ansible et l'idempotence

- Une commande ou un playbook Ansible doivent pouvoir être relancés plusieurs sans introduire de nouveauté dans le système.

- Normalement une tâche exécuté un deuxième fois devrait renvoyer `changed`

- C'est la caractéristique d'Ansible qui permet d'avoir confiance lorsqu'on lance un playbook et donc d'aller vite dans les opérations (ne pas faire de vérification manuelle à chaque fois).

---

# Ansible et l'orchestration

- Ansible se connecte à des groupes et sous groupes de machines (Cf TP, Swarm_nodes, Swarm_managers, Swarm_workers)

- Cette fonctionnalité est pratique pour la configuration mais également pour lancer des commandes ponctuelles

- On parle d'orchestration car cela permet de jouer au chef d'orchestre entre plusieurs groupes de machine depuis une machine d'administration.

---

# Commandes ad-hoc

Une commande ad-hoc Ansible est une commande bash qui permet d'effectuer une action sur des machines à partir d'un module Ansible mais sans l'écrire dans un playbook yml.

### Syntaxe

```
ansible <groupe_machine> -m <module> -a <arguments>
```

### Exemples

```
ansible all -m shell -a "uname -a"
ansible swarm_nodes -m apt -m "name=docker-ce"
```

---

# Playbook et Roles

- La base d'Ansible consiste à décrire la configuration d'un machine dans des fichiers yml appelés playbook.
- Cependant écrire seulement des playbooks mène souvent à du code peu organisé et difficile à réutiliser.
- Ansible propose des sortes de librairies appelées `roles` qui permettent d'extraire une partie générique et cohérente d'un play pour la ranger dans un projet de code à part (un dossier)
- Fonctionne comme les modules puppets en quelque sorte.

---

# Structure d'un role

```
wordpress
├── defaults
│   └── main.yml
├── files
│   └── wordpress.tar.gz
├── handlers
│   └── main.yml
├── templates
│   └── wp-config.php.j2
├── vars
│   └── main.yml
└── tasks
    └── main.yml
```

Chaque dossier correspond à une section de playbook et contient: des fichiers/templates, des listes de commandes ansible, des liste de variables.


---

# Ansible galaxy

Il s'agit du dépôt officiel de roles ansible.

- On y trouve des roles pour installer plein de choses.

- Plus ou moins officiels et réputés

- Testés, notés et évalués pour leur fiabilité/compatibilité

On installe de nouveau role soit manuellement (git clone du projet dans le dossier roles) soit avec:

```
ansible-galaxy install user.nomrole
```

---

# Inventaires statiques et dynamiques

Ansible fonctionne avec des inventaires qui contiennent:

- des listes de machines rangées par groupes
- les façons de s'y connecter
- des variables spécifiques qui permette d'adapter la configuration à chaque machine/environnement.

Les inventaires statiques (classiques) sont des fichier `ini` ou `yml` qui décrivent **"en dur"** ces informations.

- Ils ne peuvent pas s'adapter à des machines qui changent régulièrement puisqu'il faut changer notamment l'adresse ip ou le nom de domaine à la main.

---

# Les inventaires dynamiques

- Sont des programmes qui génèrent à la volée un inventaire (liste de machine, groupes, variables) en JSON.

- Les inventaires dynamiques interrogent une api pour récupérer ces informations.

- Par exemple celle d'un provider de cloud ou en s'interfaçant avec Terraform etc.



---

# Terraform

Est un outil d'infrastructure as Code qui sert à provisionner des resources/VPS auprès de divers fournisseurs de claoud (IaaS).

- Amazon Web Services
- Azure
- Google Cloud
- Digital Ocean
- Scaleway
- votre VMWare on premisse avec un driver
- etc.

Il utilise un langage descriptif qui liste les resources à créer et les liens entre elles (Cf le TP)

---

# Terraform et le cloud mixte

- L'intérêt de Terraform est de pourvoir provisionner chez différents fournisseurs avec le même outil.

- Cela permet de créer des cloud mixtes et multiprovider (50% chez aws et 50% on premisse etc.)

- Cependant, comme les cloud fournisses des ressources différentes avec de nomenclatures et propriétés différentes, la description des ressources est variable selon le provider (mais dans le même fichier)

---