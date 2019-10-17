---
title: '2 - Les playbooks Ansible'
visible: true
---

# Les playbooks

Les commandes ad-hoc sont des appels directs de modules Ansible qui fonctionnent de façon idempotente mais ne présente pas les avantages du code qui donne tout son intérêt à l'IaC:

- texte descriptif écrit une fois pour toute
- logique lisible et auditable
- versionnable avec git
- reproductible et incrémental

La dimension incrémentale du code rend en particulier plus aisé de construire une infrastructure progressivement en la complexifiant au fur et à mesure plutôt que de devoir tout plannifier à l'avance.

Le `playbook` est une sorte de script ansible, c'est à dire du code.
Le nom provient du football américain : il s'agit d'un ensemble de stratégies qu'une équipe a travaillé pour répondre aux situations du match. Elle insiste sur la versatilité de l'outil.

## Syntaxe yaml

Les playbooks ansible sont écrits au format YAML.

- YAML est basé sur les identations à base d'espaces (2 espaces par indentation en général). Comme le langage python.
- C'est un format assez lisible et simple à écrire bien que les indentations soient parfois difficiles à lire.
- C'est un format assez flexible avec des types liste et dictionnaires qui peuvent s'imbriquer.
- Le YAML est assez proche du JSON (structures isomorphes) mais plus facile à écrire.

A quoi ça ressemble ?

```
marché: # debut du dictionnaire global
  lieu: Crimée Curial
  jour: dimanche
  horaire:
    unité: "heure"
    min: 9


    max: 14 # entier
  fruits:
    - nom: pomme
      couleur: "verte"
      pesticide: avec #les chaines sont avec ou sans " ou '
            # on peut sauter des lignes dans interrompre la liste ou le dictionnaire en court
    - nom: poires
      couleur: jaune
      pesticide: sans
  légumes:
    - courgettes
    - salade

    - potiron
```

## Structure d'un playbook

```yml
--- 
- name: premier play
  hosts: serveur_web # un premier play
  become: yes
  gather_facts: false # récupérer le dictionnaires d'informations (facts) relatives aux machines

  vars:
    logfile_name: "auth.log"

  var_files:
    - mesvariables.yml

  pre_tasks:
    - name: dynamic variable
      set_fact:
        mavariable: "{{ inventory_hostname + 'prod' }}" #guillemets obligatoires

  roles:
    - flaskapp
    
  tasks:
    - name: installer le serveur nginx
      apt: name=nginx state=present # syntaxe concise proche des commandes ad hoc mais moins lisible

    - name: créer un fichier de log
      file: # syntaxe yaml extensive : conseillée
        path: /var/log/{{ logfile_name }} #guillemets facultatifs
        mode: 755

    - import_tasks: mestaches.yml

  handlers:
    - systemd:
        name: nginx
        state: "reloaded"

- name: un autre play
  hosts: dbservers
  tasks:
    ... 
```

- Un playbook commence par un tiret car il s'agit d'une liste de plays.
  
- Un play est un ensemble de taches ordonnées en plusieurs sections et qui commence par préciser sur quelles machines il s'applique (section `hosts`).

- La section `hosts` est obligatoire. Toutes les autres sections sont **facultatives** !

- La section `tasks` est généralement la section principale car elle décrit les taches de configuration à appliquer.

- La section `tasks` peut être remplacée ou complétée par une section `roles` et des sections `pre_tasks` `post_tasks`

- Les `handlers` sont des tâches conditionnelles qui s'exécutent à la fin

Cependant la plupart des sections sont facultatives.

## Ordre d'execution

1. `pre_tasks`
2. `roles`
3. `tasks`
4. `post_tasks`
5. `handlers`

Les roles ne sont pas des taches à proprement parler mais un ensemble de tâches et ressources regroupées dans un module un peu comme une librairie developpement. Cf. cours 3.

### bonnes pratiques de syntaxe

- Indentation de deux espaces.
- Toujours mettre un `name:` qui décrit lors de l'execution la tache en court : un des principes de l'IaC est l'intelligibilité des opérations.

- Utiliser les arguments au format yaml (sur plusieurs lignes) pour la lisibilité, sauf s'il y a peu d'arguments

## Imports et includes

Il est possible d'importer le contenu d'autres fichiers dans un playbook:

- `import_tasks`: importe une liste de tâches (atomiques)
- `import_playbook`: importe une liste de play contenus dans un playbook.

Les deux instructions précédentes désignent un import **statique** qui est résolu avant l'exécution.

Au contraire, `include_tasks` permet d'intégrer une liste de tâche **dynamiquement** pendant l'exécution

Par exemple:

```yml
- include_tasks: install_app.yml
  loop:
    - app1
    - app2
    - app3
```

Ce code indique à Ansible d'executer une série de tâches pour chaque application de la liste. On pourrait remplacer cette liste par une liste dynamique. Comme le nombre d'import ne peut pas être connu à l'avance on **doit** utiliser `include_tasks`.

## Élévation de privilège

Lorsqu'on a besoin d'être `root` pour exécuter une commande ou qu'on a besoin d'exécuter une commande avec un autre utilisateur on peut utiliser:

- Au moment de l'exécution l'argument `--become` en ligne de commande avec `ansible`, `ansible-console` ou `ansible-playbook`.
- La section `become: yes`
  - au début du play (après `hosts`) : toutes les tâches seront executée avec cette élévation par défaut.
  - après n'importe quelle tâche : l'élévation concerne uniquement la tâche cible.

Pour executer une tâche avec un autre utilisateur que l'utilisateur de connexion on le précise en ajoutant à `become:`, `become_user: username`

Par défaut la méthode d'élévation est `become_method: sudo`. Il n'est donc pas besoin de le préciser à moins de vouloir l'expliciter.

`su` est aussi possible ainsi que d'autre méthodes fournies par les "become plugins" exp `runas`).


## Jinja2 et variables

La plupart des fichiers Ansible (sauf l'inventaire) sont traités avec le moteur de template python JinJa2.

Ce moteur permet de créer des parties dynamiques dans le code des playbooks, des roles, et des fichiers de configuration.

Ces parties dynamiques utilisent 3 types de procédés:

- Les variables: `{{ mavariable }}` est remplacée par la valeur de cette variable provenant du dictionnaire d'exécution d'Ansible.

- Les structures de contrôles (seulement dans les templates): `{% for host in groups['appserver'] %}`.

- Les filtres qui permettent de transformer la valeur des variables: exemple : `{{ hostname | default('localhost') }}`

## Variables Ansible

Ansible utilise en arrière plan un dictionnaire contenant de nombreuses variables.

Pour s'en rendre compte on peut lancer : 
`ansible <hote_ou_groupe> -m debug -a "msg={{ hostvars }}"`

Ce dictionnaire contient en particulier des configurations ansible (`ansible_ssh_user` par exemple), des facts c'est à dire des variables dynamique caractérisant les systèmes cible (par exemple `ansible_os_family`) et des variables personnalisé de l'utilisateur que vous définissez avec vos propre nom généralement en **snake_case**.

On peut définir des variables à différents endroits comme

- La section `vars:` du playbook.
- Un fichier de varibles appelé avec `var_files:`
- L'inventaire !
  - pour chaque machine ou pour le groupe.
- Des dossiers de variables dans les roles ou les projets `defaults` (cf partie sur les roles), `group_vars`, `host_bars`
- A runtime ! avec `--extra-vars "version=1.23.45 other_variable=foo"`
- Dans une tache avec le module `set_facts`.

Lorsque définies plusieurs fois, les variables ont des priorités en fonction de l'endroit de définition.
L'ordre de priorité est très complexe: `https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable`

en résumé: les variables de runtime sont prioritaires sur les variables dans un playbook qui sont prioritaires sur les variables de l'inventaire qui sont prioritaires sur les variables par défaut d'un role.

- Bonne pratique: limiter au maximum les redéfinitions de variables (au maximum une valeur par défaut, une valeur contextuelle et une valeur runtime).

Remarque: `groups.all` et `groups['all']` sont deux syntaxes équivalentes pour désigner les éléments d'un dictionnaire.

## variables spéciales

https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html

Les plus utiles:

- `hostvars`: dictionaire de toute les variables liée à un hote.
- `ansible_host`: information utilisée pour la connexion (ip ou domaine).
- `inventory_hostname`: non de la machine dans l'inventaire (nom, domain ou ip).
- `groups`: dictionnaire de tous les groupes avec la liste des machines appartenant à chaque groupe.

Pour explorer chacun de ces variables vous pouvez utiliser:

`ansible <hote_ou_groupe> -m debug -a "msg={{ ansible_host }}"`

ou encore:

`ansible <hote_ou_groupe> -m debug -a "msg={{ groups.all }}"`


# Structures de controle ansible (et non JinJa2)


## La directive `when`

Elle permet de rendre une tâche conditionnelle (une sorte de `if`)

```yml
- name: start nginx service
  systemd:
    name: nginx
    state: started
  when: ansible_os_family == 'RedHat'
```

Sinon la tache sautée (skipped) durant l'exécution.

## La directive `loop:`

Cette directive permet d'executer une tache plusieurs fois basée sur une liste de valeur:

[https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

exemple:

```yml
- name: exemple de boucle
  debug:
    msg: {{ item }}
  loop:
    - message1
    - message2
    - message3
```

On peut également controler cette boucle avec quelques paramètres:

```yml
- debug:
    msg: "message numero {{ num }} : {{ message }}"
  loop: "{{ messages }}"
  loop_control:
    loop_var: message
    index_var: num
    
```

Cette fonctionnalité était anciennement accessible avec le mot clé `with_items:` qui est maintenant déprécié.


## Debugger un playbook.

Avec Ansible on dispose d'au moins trois manières de debugger un playbook:

- Rendre la sortie verbeuse (mode debug) avec `-vvv`.

- Utiliser une tache avec le module `debug` : `debug msg="{{ mavariable }}"`.

- Utiliser la directive `debugger: always` ou `on_failed` à ajouter à la fin d'une tâche. L'exécution s'arrête alors après l'exécution de cette tâche et propose un interpreteur de debug.
  - `p <ma_variable>` permet d'afficher la valeur d'une variable.
  - `continue` ou `c` de reprendre l'exécution.

