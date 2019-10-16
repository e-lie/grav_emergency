---
title: 'TP1 - Installer un cluster Elasticsearch et Kibana à l''aide d''Ansible'
visible: true
---

### Mettre en place son cluster avec Vagrant

- Récupérez le modèle de projet en clonant (`git clone`) le dépôt `https://github.com/e-lie/ansible-tpl-elk-forma`.
- Testez si vagrant est installé sur votre machine avec : `vagrant --version`. Sinon installez le avec `apt`.
- Installez également `virtualbox` si nécessaire avec apt.

Vagrant est une solution qui permet de décrire un ensemble de machines virtuelles grâce à un fichier texte nommé `Vagrantfile`. Par défaut `Vagrant` se base sur virtualbox (notre cas) mais peut être utilisé avec de nombreux autres providers (VMware, LXC, Docker, etc).

- Créez le `Vagrantfile` et copiez à l'intérieur le code de base:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config| # bloc de configuration global
    # Base VM OS configuration.
    config.vm.box = "ubuntu/bionic64"
    config.ssh.insert_key = false  # nécessaire pour utiliser la clé ssh par défaut
    config.vm.provider :virtualbox do |v|
      v.memory = 1536
    end

end # fin du bloc de configuration global
```

- Ajoutons la définition d'une machine virtuelle `elastic_node1` juste avant le `end` de fin:
```ruby
    # elk_node1
    config.vm.define "elastic_node1" do |elastic_node1|
      elastic_node1.vm.hostname = "elastic-node1"
      elastic_node1.vm.network :private_network, ip: "192.168.2.2"
    end
```

- Copier ensuite cette définition et modifier pour configurer un deuxième noeud `elastic_node2`.
- Lancez `vagrant up`. Cette commande crée (télécharge la box et importe) puis démarre les machines virtuelles.
- Pour connaître l'état des machines lancez virtualbox ou utilisez la commande `vagrant global-status --prune`
- Vérifiez que vous pouvez vous connecter avec la commande `vagrant ssh elastic_node1`


### Configurer et tester Ansible

- Sur l'hôte installez `ansible` avec `apt`
- Observons le projet modèle ansible:
    - `ansible.cfg` : config de base d’ansible	
    - `hosts.cfg` : fichier d'**inventaire**qui liste les machines à configurer 
    - `ping.yml` : playbook (sorte de script) ansible pour tester si les machines sont joignables avec ansible
    - `setup_ansible.yml` : playbook à remplir avec les étapes d’installations dans la partie suivante

- Compléter le fichier `ansible.cfg` pour préciser le chemin (relatif)  du fichier d'inventaire.

- Compléter le fichier `hosts.cfg` avec toutes les informations nécessaires pour se connecter en ssh
	- l'adresse ip (ou hostname en modifiant le `/etc/hosts`) pour contacter chaque serveurs
	- l'utilisateur de connexion ssh est `vagrant` sur toutes les box Vagrant par défaut.
	- le fichier de clé privé par défaut de vagrant pour se connecter aux machines: `"~/.vagrant.d/insecure_private_key"`

- Quelles machines essaie-t-on de joindre ? Quel est le groupe correspondant dans l'inventaire ?
- Compléter la section `hosts:` de ping.yml avec le nom de groupe adéquat.

- Complétons le Vagrantfile pour qu'il appelle ansible correctement. Pour cela ajoutez à la fin du dernier bloc `vm.define`:
```ruby
 # Run Ansible provisioner once for all VMs at the end.
      elastic_node2.vm.provision "ansible" do |ansible|
        ansible.playbook = "ping.yml"
        ansible.inventory_path = "hosts.cfg"
        ansible.limit = "all"
        ansible.extra_vars = {
          ansible_ssh_user: 'vagrant',
          ansible_ssh_private_key_file: "~/.vagrant.d/insecure_private_key"
        }
      end
```

- Lancez le playbook ping.yml avec la commande: `ansible-playbook ping.yml`. A quoi sert le module ping ?


## Installer Elasticsearch

- Connectez vous en ssh dansle noeud elastic n°1 et installez manuellement avec apt : `apt-transport-https  uuid-runtime openjdk-11-jre-headless gpg`. 

- Lancez `ansible-playbook setup_elastic.yml` avoir pris soin de vérifier que hosts pointait vers le bon groupe.

- Les requirements sont installé ! Voyez les `ok` et `changed` apparaissant lorsque vous lancez le playbook : ansible est verbeux il informe de sa réussite.

- Interprétons la différence entre `ok` et `changed` : `ok` signifie que le serveur était déjà dans le bon état et `changed` que l'état a été modifié pour correspondre au playbook. Normalement vous devriez avoir `ok` pour `elastic_node1` car dans la question précédente nous avions déjà installé les paquet et par contre `changed` pour le `node2` car les paquets devaient encore être installés et Ansible s'en est occupé.

- Ajoutez à la suite ces trois tasks à compléter  (qui vont servir à installer elasticsearch sur chacun des nodes) :

```yml
    - name: Add elasticsearch repo GPG key
      apt_key:
        url: <url>
        state: <state>

    - name: Add elasticsearch apt repo
      apt_repository:
        repo: <repo debian>
        state: <state>

    - name: Install Elasticsearch # requires java preinstalled
      apt:
        name: <nom du paquet>
        state: <state>
```

- Complétez ces commande à l’aide des paramètres ci-dessous
    - url de la clé GPG des dev de elastic : `https://artifacts.elastic.co/GPG-KEY-elasticsearch`
    - répertoire debian pour elastic : `"deb https://artifacts.elastic.co/packages/6.x/apt stable main"`

- Chercher sur internet quoi mettre à la place de `<state>`. Par exemple en cherchant : `ansible doc apt module`.

- Relancez le playbook !

## Bilan Ansible

- Ansible peut être rejoué plusieur fois (il est idempotent)
- Ansible garanti l’état de certains éléments du système lorsqu’on le (re)joue
- Ansible est (dès qu’on est un peu habitué) plus limpide que du bash


## Configurer Elastic en cluster

Elastic est installé mais il manque la configuration !

- Observez les fichiers dans le dossier templates : le fichier `template/elasticsearch.yml.j2` est modèle de fichier de configuration. Il contient des trous  `{{ var }}` qui doivent être remplis à l'exécution par les variables du playbook.
    
- Ajoutez une nouvelle commande pour créer les fichiers de configuration sur les noeuds elastic :

```yml
- name: Configure Elasticsearch.
  template:
    src: <template>
    dest: /etc/elasticsearch/elasticsearch.yml
    owner: root
    group: elasticsearch
    mode: 0740
  notify: restart elasticsearch
```

- Pour "boucher les trous" de la configuration, ajoutez les variables suivantes (section vars: prendre exemple sur le cours) avant les tasks : attention aux alignement ! `vars:` doit être aligné avec `tasks:`

```yml
vars:
  - elasticsearch_cluster_name: elk_formation
  - elasticsearch_network_host: "0.0.0.0"
  - elasticsearch_http_port: 9200
  - elk_node_ips:
    - <ip_node_1>
    - <ip_node_2>
```

- Pensez à changer les ips pour désigner vos nœuds elastic.
- Lorsqu'on change la configuration il faut généralement recharger ou redémarrer le service : ajoutez la fin suivante au playbook :

```yml
    - name: Setup Heap Memory size in /etc/elasticsearch/jvm.options
      lineinfile:
        path: /etc/elasticsearch/jvm.options
        regexp: "^-Xm{{ item }}.*"
        line: "-Xm{{ item }}800m"    
      loop:
        - 's'
        - 'x'
      notify: restart elasticsearch

    - name: Start Elasticsearch.
      systemd:
        name: elasticsearch
        state: <state>
        enabled: yes
        daemon_reload: yes #required before first run

    - name: Make sure Elasticsearch is running before proceeding.
      wait_for:
        host: "{{ elasticsearch_network_host }}"
        port: "{{ elasticsearch_http_port }}"
        delay: 3
        timeout: 60

  handlers:
    - name: restart elasticsearch
      service:
        name: elasticsearch
        state: <state>
```

(attention aux alignements : handlers est au même niveau que tasks)

-  Jouez le playbook enfin complet.

Si votre playbook s'est exécuté sans erreurs, c'est que elastic devrais être installé. Pour vérifier sont fonctionnement essayons de le contacter sur le réseau:
    
- Lancez les commandes suivantes:

```bash
curl http://<ip_node_1>:9200/_cat/nodes?pretty
curl -XGET '<ip_node_2>:9200/_cluster/state?pretty'
```

Si tout est bien configuré vous devriez voir une liste de deux nœuds signifiant que les deux elastic se « connaissent ».

### Installer kibana
    
Pour installer kibana (le GUI web de elasticsearch) il faut idéalement un nouveau noeud et un autre playbook très proche celui que nous venons de compléter.

- Ajoutez un nouveau noeud `kibana_node` au `Vagrantfile` en copiant et modifiant la section adéquate.
- Lancez `vagrant up` pour créer le troisième noeud.
- Ajoutez un nouveau groupe à la fin de l'inventaire comme suit:

```ini
[kibana_nodes] 
kibana_node ansible_host=<ip>
```
    
- Lancez le playbook `ping.yml` après avoir changé `hosts: elastic_nodes` en `hosts: all` dans ce playbook. Vous devriez maintenant atteindre 3 serveurs au lieu de deux.
- Copier `setup_elastic.yml` et renommer le en `setup_kibana.yml`.
- Remplacez son contenu par :

```yml
- hosts: <group>
  become: yes

  vars:
    - kibana_server_port: 5601
    - kibana_server_host: "0.0.0.0"
    - kibana_elasticsearch_url: "http://<elastic_node_ip>:9200"
    - kibana_elasticsearch_username: ""
    - kibana_elasticsearch_password: ""

  tasks:
    - name: Install apt requirements
      apt:
        name: 
          - apt-transport-https
          - uuid-runtime
          - openjdk-11-jre-headless
          - gpg
        state: present
        update_cache: yes

    - name: Add elasticsearch repo GPG key
      apt_key:
        url: https://artifacts.elastic.co/GPG-KEY-elasticsearch
        state: present

    - name: Add elasticsearch apt repo
      apt_repository:
        repo: "deb https://artifacts.elastic.co/packages/6.x/apt stable main"
        state: present

    - name: Install Kibana
      apt: 
        name: <package_name>
        state: <state>

    - name: Configure Kibana.
      template:
        src: <template_name>
        dest: /etc/kibana/kibana.yml
        owner: root
        group: kibana
        mode: 0740 # Kibana service run with user kibana in group kibana
      notify: restart kibana

    - name: Create kibana log file
      file:
        state: touch
        path: /var/log/kibana.log
        owner: kibana
        mode: 740

    - name: Start Kibana.
      systemd:
        name: kibana
        state: started
        enabled: yes
        daemon_reload: yes #required before first run

  handlers:
    - name: restart kibana
      service:
      	name: kibana
        state: restarted
```

- Remplacez : `<elastic_node_ip>` par l’ip d’un des nœuds elastic (192.168.2.x)
- Complétez les (4) trous dans le playbook de façon logique et en s'inspirant du précédent.
- Lancer : `ansible-playbook setup_kibana.yml`
- Accéder à `http://<ip_kibana>:5601` dans firefox :D
- Créez un commit dans le dépot pour sauvegarder tout ce travail :D !

### Correction

La correction du TP se trouve dans le dépôt modèle `https://github.com/e-lie/ansible-tpl-elk-forma` dans la branche `correction_elastic_install_vagrant`.
