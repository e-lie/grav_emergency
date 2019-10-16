---
title: 'Puppet TP1 - Installer une infrastructure Puppet avec Vagrant et Ansible - Correction'
visible: true
---

### Démarrer le projet Vagrant

- Créez un dossier `tp1_install_puppet_infra` et ouvrez le avec VSCode
- Ajoutez un `Vagrantfile`.
- Ajoutez le code suivant:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
    # Base VM OS configuration.
    config.vm.box = "ubuntu/bionic64"
    config.ssh.insert_key = false # Avoid  vagrant to remove the common ssh private key
    config.vm.provider :virtualbox do |v|
      v.memory = 1200
    end
end
```
- Ajoutez trois machines en copiant trois fois le code suivant **juste avant le dernier end**:

```ruby
    #nom machine
    config.vm.define "<nom machine>" do |<nom_machine>|
      <nom_machine>.vm.hostname = "<hostname>"
      <nom_machine>.vm.network :private_network, ip: "<ip_machine>"
    end
```

- complétez les trous avec les données suivantes:
  - nom: `puppet_node1` ip: `192.168.22.3` 
  - nom: `puppet_node2` ip: `192.168.22.4` 
  - nom: `puppet` ip: `192.168.22.0` 

- Lancez les trois machines avec `vagrant up` et ensuite vérifier leur état avec `vagrant global-status --prune`.
- Initialisez le dépôt git
- Ajoutez un `.gitignore` avec à l'intérieur:

```
.vagrant
*.retry
```

- faites un premier commit `initial commit`.
- synchronisez le dépôt avec framagit.




### Provisionning Ansible et debug

- Créez un dossier `setup_puppet` avec à l'intérieur des fichiers `main.yml`, `common.yml`, `hosts.cfg`.

`hosts.cfg`

```ini
[all:vars] # variables pour le groupe all c'est à dire pour tous les serveurs
ansible_user=vagrant
ansible_ssh_private_key_file="~/.vagrant.d/insecure_private_key"
ansible_python_interpreter="/usr/bin/python3"

[puppet_nodes] # nom du groupe
puppet_node1 ansible_host=192.168.2.2
puppet_node2 ansible_host=192.168.2.3

[puppet-masters] 
puppet ansible_host=192.168.2.0
```

`main.yml`

```yml
- hosts: all
  become: yes
  tasks:
    - include: common.yml
```

`common.yml`

```yml
- name: Check if puppet6-release (repo addition deb) is installed
  command: dpkg-query -W puppet6-release
  register: puppet6_release_check_deb
  failed_when: puppet6_release_check_deb.rc > 1
  changed_when: false

- name: Download puppet6-release
  get_url:
    <...>
  when: puppet6_release_check_deb.rc == 1

- name: Install puppet6-release
  apt:
    deb: "<chemin_du_paquet_deb>"
  when: puppet6_release_check_deb.rc == 1
```

- Dans le playbook précédent, trouvez comment télécharger `puppet6-release-bionic.deb` à l'adresse `https://apt.puppetlabs.com/puppet6-release-bionic.deb` avec le module ansible `get_url`. Mettez le résultat dans `/tmp/puppet6-release-bionic.deb` (utilisez la doc ansible).


- Pour tester votre playbook ajoutez au `Vagrantfile` (et complétez) à l'intérieur du bloc du dernier noeud ie `puppet` (pour l'exécuter à la fin de la création des machines).

```ruby
      puppet.vm.provision "ansible" do |ansible|
        ansible.playbook = "<playbook>"
        ansible.inventory_path = "<inventory>"
        ansible.limit = "all"
      end
```

- Le module `command` de ansible n'est pas idempotent par défaut. C'est-à-dire qu'il affiche `changed` à chaque exécution du playbook car ansible ne peut pas garantir si le module a opérer un changement sur le serveur ou non. Cela casse un peu le principe de la configuration ansible.

- Notez les ajouts ansible `failed_when`, `register`, `changed_when` et `when`. Ils permettent de rendre de nombreuses commandes bash **idempotente**.
  - `register:` permet de ranger le résultat d'une tâche dans une variable (le nom est à votre convenance)
  - On récupère ensuire le return code de la commande précédente pour savoir si le puppet-release est installé : 1 => à installer et 0 => déjà installé.
  - `changed_when` indique à Ansible s'il doit indiquer `changed` ou `ok` à l'exécution => ici le playbook n'affichera jamais changed car cette tache ne change jamais le système (il s'agit juste d'un check pour la suite).
  - `failed_when` permet d'indiquer à ansible dans quelles conditions il doit considérer la tâche comme échouée.
  - Si (`when:`) le paquet n'est pas installé (rc == 1) alors on le récupère avec get_url (wget de ansible)
  - Si (`when:`) le paquet n'est pas installé on l'installe à partir 

- Ajoutez au playbook `common.yml` une tache `apt` qui permet d'installer le fichier `/tmp/puppet6-release-bionic.deb` quand (`when`) `puppet6_release_check_deb.rc == 1` c'est à dire quand le paquet n'est pas encore installé (d'après la tache de vérificatio précédente).


- Ajoutez les taches de debug suivantes qui permet d'**observer la valeur des variables ansible**:

```yml
- debug:
    msg: "{{ puppet6_release_check_deb }}"

- debug:
    msg: "{{ puppet6_release_check_deb.rc }}"
```

On remarque que le register enregistre tout un dictionnaire qui contient plein d'informations sur l'exécution de la commande. `rc` n'est qu'une information de ce dictionnaire. `debug:` est très pratique pour construire des playbooks dynamiques avec des variables complexes. Il permet de bien vérifier que les variables contiennent la bonne valeur.

- Que remarque-t-on à la première exécution du playbook (après un `vagrant destroy`) et à la seconde ?
```
A la première (pas installé) rc == 1 et à ma deuxième (déjà installé) rc == 0
```

### Hosts files


Ajoutez la tache suivante qui permet d'ajouter toutes les machines aux fichiers `/etc/hosts` dans chaque machine:

```yml
- name: Configure hosts file for the cluster
  lineinfile:
    path: /etc/hosts
    line: "{{ hostvars[item].ansible_enp0s8.ipv4.address }}  {{ item }}" 
    # enp0s8 est la deuxième interface (l'interface bridge de virtualbox car
    # la premiere est l'interface NAT toujours créée par Vagrant)
  loop:
    "{{ groups['all'] }}"
```

- Supprimez (ou commentez) les taches de debug précédentes et ajoutant une nouvelle pour afficher le contenu des variables `groups['all']` et `hostvars['puppet']`.


```yml
- debug:
    msg: "{{ groups['all'] }}"

- debug:
    msg: "{{ hostvars['puppet'] }}"
```

- Exécutez le playbook en redirigeant la sortie vers un fichier pour pouvoir explorer le dictionnaire `hostsvars`:
  - `vagrant provision >> result_provision.log && cat result_provision.log | less`

Conclusion: il y a à boire et à manger dans `hostvars`. Pour chaque machine de l'inventaire on peut récupérer ses `facts` par exemple ses adresses ip, ses interfaces réseau, son OS, ses certificats ssh, etc. Cela permet d'adapter le comportement du playbook à la machine sur laquelle il s'exécute.


### Installer puppet server sur le master

- Créez le fichier de taches suivant.

`master.yml`

```yml
- name: Install puppetserver
  apt:
    name: <paquet puppet server>
    update_cache: yes
```

- Le nom du paquet est `puppetserver`.
- Importez `master.yml` dans le playbook `main.yml` en duplicant les 4 lignes à l'intérieur et en changeant le nécessaire (on veut lancer ces taches uniquement sur `puppet_master`).

```yml
- hosts: all
  become: yes
  tasks:
    - include: common.yml


- hosts: puppet-masters
  become: yes
  tasks:
    - include: master.yml
```

- Testez le playbook avec `vagrant provision`.

- Lancez la commande shell suivante `/opt/puppetlabs/bin/puppetserver ca setup` à l'aide du module `command` de ansible.

```
- name: Install Puppet Master certificates
  command: /opt/puppetlabs/bin/puppetserver ca setup
```

- Ajoutez les 3 lignes suivantes pour rendre la commande idempotente:

```yml
  args:
    creates: "/etc/puppetlabs/puppet/ssl/ca"
  failed_when: false
```

correction: le failed_when est facultatif ici (utilisé pour contourner un bug local)

```
- name: Install Puppet Master certificates
  command: /opt/puppetlabs/bin/puppetserver ca setup
  args:
    creates: "/etc/puppetlabs/puppet/ssl/ca"
  failed_when: false
```

- Relancez le playbook deux fois pour tester cette tache.
- Nous voudrions ensuite limiter la ram java utilisée par puppet en ajoutant une ligne au fichier `/etc/default/puppetserver`.
- La ligne est `'JAVA_ARGS="-Xms800m -Xmx800m -Djruby.logger.class=com.puppetlabs.jruby_utils.jruby.Slf4jLogger"'`
- Utilisez le module `lineinfile` en allant regarder sa documentation.
- Utilisez la regexp `'^JAVA_ARGS=.*'` pour trouver la ligne à éditer.

correction:

```yml
- name: Ensure to limit memory of puppetserver
  lineinfile:
    path: /etc/default/puppetserver
    regexp: '^JAVA_ARGS=.*'
    line: 'JAVA_ARGS="-Xms800m -Xmx800m -Djruby.logger.class=com.puppetlabs.jruby_utils.jruby.Slf4jLogger"'
```

- Créez à la racine du projet le dossier `puppet_manifests` dans lequel nous rangerons les fichiers de code puppet.
- Créez un premier manifeste vide `site.pp` à l'intérieur.

- Complétez ensuite le fichier avec les taches suivantes:

```yml
- name: Ensure puppetserver service is running and enabled
  systemd:
    name: puppetserver
    state: started
    enabled: yes

- name: Link local puppet manifest folder to the master manifest folder
  file:
    src: /vagrant/puppet_manifests
    dest: /etc/puppetlabs/code/environments/production/manifests
    state: link
    force: 'yes'
```

- A quoi servent ces taches ?

```
- La première tâche permet de lancer le service puppetserver, c'est à dire le master puppet qui écoute sur le port 8140.
- La deuxième va créer un lien symbolique (comme `ln -s`) sur le dossier de manifestes pour que le puppet master puisse lancer les fichiers manifeste de notre projet. En effet vagrant monte par défaut le dossier du projet dans chaque VM au niveau du chemin `/vagrant`. Vous pouvez vérifier avec vagrant ssh puppet que le dossier manifests est bien monté et linké.
- Du coup vous savez ou puppet tire par défaut ses fichiers manifeste de production.
```

### Installer les agents puppet sur les noeuds

Créez un nouveau fichier de tache:

`agent.yml`

```yml
- name: Install puppet agent
  apt:
    name: puppet-agent
    update_cache: yes


- name: Request signing certificate from the master
  command: /opt/puppetlabs/bin/puppet agent -t
  args:
    creates: /etc/puppetlabs/puppet/ssl/certs/ca.pem
  failed_when: false
```

- Que font ces tâches ?
- Sur quels noeuds faut-il les jouer ? Ajoutez le bon bloc d'import dans `main.yml` en copiant l'un des précédents.

`main.yml`

```yml
- hosts: puppet_nodes
  become: yes
  tasks:
    - include: agent.yml
```

### Valider l'authentification des noeuds puppet

Les noeuds agent puppet ont demandé précédemment au master de les authentifier (`puppet agent -t`). Il faut retourner du côté du master pour qu'il valide ces demandes d'authentification.

- Ajoutez le code ci dessous dans un fichier `master_signing.yml` avec à l'intérieur:

```yml
- name: Sign pending agent certificate
  command: /opt/puppetlabs/bin/puppetserver ca sign --all
  changed_when: false
  failed_when: false.
```

- Du coup `puppet ca sign --all` permet de signer les requêtes envoyées de tous les agent.
- Exécutez les modifications avec `vagrant provision`. Si tout se passe bien votre cluster puppet devrait être installé correctement.
- Pour le vérifier connectez vous au master avec `vagrant ssh puppet`, connectez vous en root avec `sudo -i`. Exécutez ensuite `puppetserver ca list --all`. Vous devriez voir une liste avec puppet-node1 et puppet-node2. Cela indique que les agents sont bien connectés.

- Le corrigé de cette installation est disponible dans le dépôt : [https://framagit.org/e-lie/puppet_lab](https://framagit.org/e-lie/puppet_lab)

### Première config puppet

Puppet utilise des fichiers déclaratifs nommés **manifestes** pour stocker la configuration.
La manifeste principal est classiquement `/etc/puppetlabs/code/environments/production/manifests/site.pp` situé sur le master. Le lien symbolique créé dans master.yml permet que les manifestes dans `puppet_manifests` soient automatiquement détectés par puppet.


- À l'intérieur de ce dossier ajoutez le fichier `site.pp`:

```
node 'puppet-node1', 'puppet-node2' {
  package { 'cowsay':
    ensure => present,
  }
}
```

- Rendons nous sur le node2 avec `vagrant ssh puppet_node2`. Lancez `sudo -i` pour être root (puppet doit être manipulé en root).
- Vérifier l'état du service puppet. ne le démarrez pas et stoppez le éventuellement.

```
systemctl status puppet
```

- Essayez de lancer `/usr/games/cowsay "puppet!"`. `no such file or directory` : cowsay n'est pas encore installé.
- Lançons manuellement la configuration avec la commande : `puppet agent --test --no-daemonize`
- Normalement `cowsay` a été installé.

### Lancer l'agent en mode service et définir l'intervale

Le mode d'utilisation de base de puppet consiste à démarrer le service `puppet` sur les noeuds et définir un intervale (600 = 10 minutes par défaut) après lequel la configuration est mise à jour depuis le master. Pour voir notre serveur évoluer au fur et à mesure, définission cette interval à 20 secondes.

- Sur l'un des nodes, éditez le fichier `/etc/puppetlabs/puppet/puppet.conf` et ajoutez la section:

```ini
[agent]
runinterval=20
```

- Démarrez le service `puppet`.
- Lancez `tail -f /var/log/syslog` et laissez tourner.
- Modifiez votre manifeste pour désinstaller `cowsay` en mettant le statut `absent`. Sauvegardez.
- Observez l'évolution de `syslog` en parallèle dans le terminal.
- Vérifiez que cowsay a bien été désinstallé.


- Créez deux tâches ansible dans le playbook `agent.yml` pour ajouter la configuration `runinterval=20` précédente et une autre pour démarrer le service `puppet` sur chaque puppet node.

```yml
- name: define runinterval for puppet agent
  blockinfile: 
    block: |
      [agent]
      #default 600 seconds
      runinterval=20
    path: /etc/puppetlabs/puppet/puppet.conf

- name: start puppet agent service
  systemd:
    name: puppet
    state: started
    enabled: 'yes'
```

- Sur l'hôte relancez la configuration ansible avec: `vagrant provision`.

### Installer nginx modifier la page d'accueil

- Ajoutez une resource `package` `nginx` au manifeste `site.pp` pour installer ce serveur.

```
  package { 'nginx':
    ensure => present,
  }
```
- Normalement nginx devrait être installé automatiquement en 30 secondes après la sauvegarde de `site.pp`. Pour le vérifier, surveillez le fichier `syslog` ou visitez `192.168.2.3`.
- Connectez vous en ssh à l'un des noeuds. cherchez dans `/var` avec `grep -Rn "<texte>"` le titre de la page d'accueil nginx pour retrouver ou est enregistrer le fichier html de base.

- Nous allons ajoutez au manifeste une ressource `file` pour contrôler le contenu de ce fichier:

```
  file { '<chemin_fichier_html>':
    content => "<h1>Bienvenue!</h1>",
  }
```

- Au bout de quelques instants rechargez la page de l'un des noeuds (CTRL + F5 pour vider le cache). Le contenu devrait avoir été mis à jour.

- Pour faire une véritable page nous allons utiliser un template `inline_epp` puppet. Tout comme Jinja pour ansible il s'agit d'une façon de créer des modèles de texte à trou ou avec des if et des for (cf tp ansible elasticsearch). Dans le fichier `site.pp` au dessus du bloc principal ajoutez : 

```html
$html_bienvenue_template = @(END)

<html><head>
<title>Bienvenue sur le site du server nomserveur !</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Bienvenue sur le site du server nomserveur </h1>
<p>Ceci est une page d'exemple servant a montrer le potentiel
du templating avec le gestionnaire de configuration Puppet.</p>
<p><em>Thank you.</em></p>
</body></html>

END
```

- il s'agit d'une variable (template inline) qui définit notre template. changez la caractéristique `content` de notre fichier par `=> inline_epp($html_bienvenue_template)`

- Maintenant remplaçons `nomserveur` par la valeur d'une variable. La syntaxe puppet pour cela est  `<%= $variable %>` et notre variable est le nom d'hote de la machine désignée par `$hostname`.

```html
$html_bienvenue_template = @(END)

<html><head>
<title>Bienvenue sur le site du server <%= $hostname %> !</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Bienvenue sur le site du server <%= $hostname %> </h1>
<p>Ceci est une page d'exemple servant a montrer le potentiel
du templating avec le gestionnaire de configuration Puppet.</p>
<p><em>Thank you.</em></p>
</body></html>

END
```

- Rechargez la page pour voir la modification. Visitez l'autre noeud.

# Ajouter deux nouveaux noeuds

- Trouvez comment ajouter de nouveaux noeuds puppet à votre cluster an modifiant votre Vagrantfile et votre inventaire ansible.
