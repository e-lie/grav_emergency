---
title: 'Cours 4 - Sécurité et Cloud'
visible: true
---

# Sécurité

La sécurité linux n'est pas résolue magiquement par Ansible. Tous le travail de réflexion et de sécurisation reste identique mais peut comme le reste être mieux controllé.

Un livre si la securité DevOps vous intéresse : `Security automation with Ansible`

A noter tout de même qu'Ansible est généralement apprécié car il n'augmente pas (vraiment) la surface d'attaque de vos infrastructure.

## Authentification et SSH

Bonne pratique importante : changez le port de connexion ssh pour un port atypique. Ajoutez la variable `ansible_ssh_port=17728` dans l'inventaire

Éviter de créer un seul compte ansible de connexion pour toutes les machines:
- difficile à bouger
- responsabilité des connexions pas auditable (auth.log + syslog)

Il faut utiliser comme nous avons fait dans les TP des logins ssh avec les utilisateurs humain réels des machines et des clés ssh. C'est à dire le même modèle d'authentification que l'administration traditionnelle.

## Les autres modes de connexion

Le mode de connexion par défaut de Ansible est SSH cependant il est possible d'utiliser de nombreux autres modes de connexion spécifiques :

- La liste des plugins de se trouve ici [https://docs.ansible.com/ansible/latest/plugins/connection.html#plugin-list]

- Une autre connexion courante est `ansible_connection=local` qui permet de configurer la machine locale sans avoir besoin d'installer un serveur ssh.

- Les questions de sécurités de la connexion se posent bien sur différemment selon le mode de connexion utilisés (port, authentification, etc.)

- Pour débugger les connexions et diagnotiquer leur sécurité on peut afficher les détails de chaque connection ansible avec le mode de verbosité maximal (network) en utilisant le paramètre `-vvvv`.

## Variables et secrets

Principal risque de sécurité lié à ansible comme avec Docker et l'IaC en général consiste à laisser trainer des secrets (mot de passe, identités de clients, api token, secret de chiffrement / migration etc.) dans le code ou sur les serveurs (moins problématique).

## Désactiver le logging des informations sensibles

Ansible propose une directive `no_log: yes` qui permet de désactiver l'affichage des valeurs d'entrée et de sortie d'une tâche.

Il est ainsi possible de limiter la prolifération de données sensibles.

## Ansible vault

Pour éviter de divulguer des secrets par inadvertance, il est possible de gérer les secrets avec des variables d'environnement ou avec un fichier variable externe au projet qui échappera au versionning git, mais ce n'est pas idéal.

Ansible intègre un trousseau de secret appelé , **Ansible Vault** permet de chiffrer des valeurs **variables par variables** ou des **fichiers complets**.
Les valeurs stockées dans le trousseaux sont déchiffrée à l'exécution après dévérouillage du trousseau. 

- `ansible-vault create /var/secrets.yml`
- `ansible-vault edit /var/secrets.yml` ouvre `$EDITOR` pour changer le fichier de variables.
- `ansible-vault encrypt_file /vars/secrets.yml` pour chiffrer un fichier existant
- `ansible-vault encrypt_string monmotdepasse` permet de chiffrer une valeur avec un mot de passe. le résultat peut être ensuite collé dans un fichier de variables par ailleurs en clair.

Pour déchiffrer il est ensuite nécessaire d'ajouter l'option `--ask-vault-pass` au moment de l'exécution de `ansible` ou `ansible-playbook`

Il existe également un mode pour gérer plusieurs mots de passe associés à des identifiants.

## Ansible dans le cloud

L'automatisation Ansible fait d'autant plus sens dans un environnement d'infrastructures dynamique:

- L'agrandissement horizontal implique de résinstaller régulièrement des machines identiques
- L'automatisation et la gestion des configurations permet de mieux contrôler des environnements de plus en plus complexes.

Il existe de nombreuses solutions pour intégrer Ansible avec les principaux providers de cloud (modules ansible, plugins d'API, intégration avec d'autre outils d'IaC Cloud comme Terraform ou Cloudformation).

## Inventaires dynamiques

Les inventaires que nous avons utilisés jusqu'ici implique d'affecter à la main les adresses IP des différents noeuds de notre infrastructure. Cela devient vite ingérable.

La solution ansible pour le pas gérer les IP et les groupes à la main est appelée `inventaire dynamique` ou `inventory plugin`. Un inventaire dynamique est simplement un programme qui renvoie un JSON respectant le format d'inventaire JSON ansible, généralement en contactant l'api du cloud provider ou une autre source.

```
$ ./inventory_terraform.py
{
  "_meta": {
    "hostvars": {
      "balancer0": {
        "ansible_host": "104.248.194.100"
      },
      "balancer1": {
        "ansible_host": "104.248.204.222"
      },
      "awx0": {
        "ansible_host": "104.248.204.202"
      },
      "appserver0": {
        "ansible_host": "104.248.202.47"
      }
    }
  },
  "all": {
    "children": [],
    "hosts": [
      "appserver0",
      "awx0",
      "balancer0",
      "balancer1"
    ],
    "vars": {}
  },
  "appservers": {
    "children": [],
    "hosts": [
      "balancer0",
      "balancer1"
    ],
    "vars": {}
  },
  "awxnodes": {
    "children": [],
    "hosts": [
      "awx0"
    ],
    "vars": {}
  },
  "balancers": {
    "children": [],
    "hosts": [
      "appserver0"
    ],
    "vars": {}
  }
}%  
```

On peut ensuite appeler `ansible-playbook` en utilisant ce programme plutôt qu'un fichier statique d'inventaire: `ansible-playbook -i inventory_terraform.py configuration.yml`


## Étendre et intégrer Ansible


Bonne pratique : Normalement l'information de configuration Ansible doit provenir au maximum de l'inventaire. Ceci est conforme à l'orientation plutôt déclarative d'Ansible et à son exécution descendante (master -> nodes). La méthode à privilégier pour intégrer Ansible à des sources d'information existantes est donc de développer un **plugin d'inventaire**.

[https://docs.ansible.com/ansible/latest/plugins/inventory.html](https://docs.ansible.com/ansible/latest/plugins/inventory.html)

On peut cependant alimenter le dictionnaire de variable Ansible au fur et à mesure de l'exécution, en particulier grâce à la directive `register` et au module `set_fact`.

Exemple:

```yml
# this is just to avoid a call to |default on each iteration
- set_fact:
    postconf_d: {}

- name: 'get postfix default configuration'
  command: 'postconf -d'
  register: postconf_result
  changed_when: false

# the answer of the command give a list of lines such as:
# "key = value" or "key =" when the value is null
- name: 'set postfix default configuration as fact'
  set_fact:
    postconf_d: >
      {{ postconf_d | combine(dict([ item.partition('=')[::2]map'trim') ])) }}
  loop: postconf_result.stdout_lines
```

On peut explorer plus facilement la hiérarchie d'un inventaire statique ou dynamique avec la commande:

```
ansible-inventory --inventory <inventory> --graph
```

### Liste des type de plugins possibles pour étendre Ansible

[https://docs.ansible.com/ansible/latest/dev_guide/developing_plugins.html](https://docs.ansible.com/ansible/latest/dev_guide/developing_plugins.html)

- Ansible modules
- Inventory plugins
- Connection plugins

#### Secondaire

- Action plugins
- Cache plugins
- Callback plugins
- Filter plugins
- Lookup plugins
- Test plugins
- Vars plugins


### Intégration Ansible et AWS

Utiliser le plugin d'inventaire AWS et les modules adaptés.

Par exemple:
- [https://docs.ansible.com/ansible/latest/modules/ec2_module.html](https://docs.ansible.com/ansible/latest/modules/ec2_module.html).

### Intégration Ansible Nagios

Laisser le contrôle à Nagios et utiliser un plugin pour que Nagios puisse lancer des plays Ansible.

### Intégration Ansible avec Openstack

Utiliser un `inventory plugin` basé sur le composant `Nova` d'`OpenStack` c'est à dire la base de données partagé du cloud Openstack.

