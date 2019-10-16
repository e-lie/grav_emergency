---
title: "TP2 créer un playbook de déploiement d'application flask" 
visible: false
---

## Création du projet

- Créez un nouveau dossier `tp2_flask_deployment`.
- Créez le fichier `ansible.cfg` comme précédemment.
- Créez deux machines ubuntu `app1` et `app2`.
- Créez l'inventaire statique `inventory.cfg`.
- Ajoutez à l'intérieur les deux machines dans un groupe `appservers`.
- Pinguez les machines.
- Initialisez un dépôt git dans ce dossier.
- Ajoutez un fichier `.gitignore` avec à l'intérieur:

```bash
*.retry   # Fichiers retry produits lors des execution en echec de ansible-playbook
```

- Committez vos modifications avec git.

## Premier playbook : installer les dépendances

Le but de ce projet est de déployer une application flask, c'est a dire une application web python.
Le code (très minimal) de cette application se trouve sur github à l'adresse: [https://github.com/e-lie/flask_hello_ansible.git](https://github.com/e-lie/flask_hello_ansible.git).

- N'hésitez pas consulter extensivement la documentation des modules avec leur exemple ou d'utiliser la commande de doc `ansible-doc <module>`

- Créons un playbook : ajoutez un fichier `flaskhello_deploy.yml` avec à l'intérieur:

```yml
- hosts: <hotes_cible>
  
  tasks:
    - name: ping
      ping:
```

- Lancez ce playbook avec la commande `ansible-playbook <nom_playbook>`.

- Commençons par installer les dépendances de cette application. Tous nos serveurs d'application sont sur ubuntu. Nous pouvons donc utiliser le module `apt` pour installer les dépendances. Il fournit plus d'option que le module `package`.

- Avec le module `apt` installez les applications: `python3-dev`, `python3-pip`, `python3-virtualenv`, `virtualenv`, `nginx`, `git`. Donnez à cette tache le nom: `ensure basic dependencies are present`. ajoutez pour cela la directive `become: yes` au début du playbook.

- Ajoutez une tâche `systemd` pour s'assurer que le service est démarré.

- Ajoutez une tache pour créer un utilisateur `flask` et l'ajouter au groupe `www-data`. Utilisez bien le paramètre `append: yes` pour éviter de supprimer des groupes à l'utilisateur.

- Relancez bien votre playbook à chaque tache : comme Ansible est idempotent il n'est pas grave en situation de développement d'interrompre l'exécution du playbook et de reprendre l'exécution après un échec.

## Récupérer le code de l'application

- Pour déployer le code de l'application deux options sont possibles.
  - Télécharger le code dans notre projet et le copier sur chaque serveur avec le module `sync` qui fait une copie rsync.
  - Utiliser le module `git`.

- Nous allons utiliser la deuxième option (`git`) qui est plus cohérente pour le déploiement et la gestion des versions logicielles. Allez voir la documentation comment utiliser ce module.
  
- Utilisez le pour télécharger le code source de l'application (branche `master`) dans le dossier `/home/flask/hello` mais en désactivant la mise à jour (au cas ou le code change).

- Lancez votre playbook et allez vérifier sur une machine en ssh que le code est bien téléchargé.

## Installez les dépendances python de l'application

Le langage python a son propre gestionnaire de dépendances `pip` qui permet d'installer facilement les librairies d'un projet. Il propose également un méchanisme d'isolation des paquets installés appelé `virtualenv`. Normalement installer les dépendances python nécessite 4 ou 5 commandes shell.

- La liste de nos dépendances est listée dans le fichier `requirements.txt` à la racine du dossier d'application.

- Nous voulons installer ces dépendances dans un dossier `venv` également à la racine de l'application.

- Nous voulons installer ces dépendance en version python3 avec l'argument `virtualenv_python: python3`.

Avec ces informations et la documentation du module `pip` installez les dépendances de l'application.

## Changer les permission sur le dossier application

Notre application sera executée en tant qu'utilisateur flask pour des raisons de sécurité. Pour cela le dossier doit appartenir à cet utilisateur or il a été créé en tant que root (à cause du `become: yes` de notre playbook).

- Créez une tache `file` qui change le propriétaire du dossier de façon récursive.

## Déployer configurer le service qui fera tourner l'application

Notre application doit tourner comme c'est souvent le cas en tant que service (systemd). Pour cela nous devons créer un fichier service adapté `hello.service` dans le le dossier `/etc/systemd/system/`.

Ce fichier est un fichier de configuration qui doit contenir le texte suivant:

```ini
[Unit]
Description=Gunicorn instance to serve hello
After=network.target

[Service]
User=flask
Group=www-data
WorkingDirectory=/home/flask/hello
Environment="PATH=/home/flask/hello/venv/bin"
ExecStart=/home/flask/hello/venv/bin/gunicorn --workers 3 --bind unix:hello.sock -m 007 app:app

[Install]
WantedBy=multi-user.target
```

Pour gérer les fichier de configuration on utilise généralement le module `template` qui permet à partir d'un fichier modèle situé dans le projet  ansible de créer dynamiquement un fichier de configuration adapté sur la machine distante.

- Créez un dossier `templates`, avec à l'intérieur le fichier `app.service.j2` contenant le texte précédent.
- Utilisez le module `template` pour le copier au bon endroit avec le nom `hello.service`.

- Utilisez ensuite `systemd` pour démarrer ce service (`state: restarted` ici pour le cas ou le fichier à changé).

# Configurer nginx

- Comme précédemment créez un fichier de configuration `hello.test.conf` dans le dossier `/etc/nginx/sites-available` à partir du fichier modèle:

`nginx.conf.j2`

```
server {
    listen 80;

    server_name hello.test;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/flask/hello/hello.sock;
    }
}
```

- Utilisez `file` pour créer un lien symbolique de ce fichier dans `/etc/nginx/sites-enabled` (avec l'option `force:yes` pour écraser le cas échéant).

- Ajoutez une tache pour supprimer le site `/etc/nginx/sites-enabled/default`.

- Ajouter une tache de redémarrage de nginx.

- Ajoutez `hello.test` dans votre fichier `/etc/hosts` pointant sur l'ip d'un des serveur d'application.

- Visitez l'application dans un navigateur et debugger le cas échéant.


<!-- ### Correction

`flaskhello_deploy.yml`

```yml
- hosts: appservers
  become: yes

  tasks:
    - name: update apt cache
      apt:
        update_cache: yes

    - name: ensure basic apps present
      apt:
        name:
          - python3-dev
          - python3-pip
          - python3-virtualenv
          - virtualenv
          - nginx
          - git
        state: present

    - name: ensure nginx started
      systemd:
        name: nginx
        state: started

    - name: "add the user flask"
      user:
        name: "flask"
        state: present
        append: yes # important pour ne pas supprimer les groupes d'un utilisateur existant
        groups:
        - "www-data"

    - name: git get app files
      git:
        repo: "https://github.com/e-lie/flask_hello_ansible.git"
        dest: /home/flask/hello
        version: "master"
        clone: yes
        update: no
    
    - name: install modules in a virtualenv
      pip:
        requirements: /home/flask/hello/requirements.txt
        virtualenv: /home/flask/hello/venv
        virtualenv_python: python3
    
    - name: change permissions of app directory
      file:
        path: /home/flask/hello
        state: directory
        owner: "flask"
        recurse: true
    
    - name: template systemd service config
      template:
        src: templates/app.service.j2
        dest: /etc/systemd/system/hello.service
    
    - name: start systemd app service
      systemd:
        name: "hello.service"
        state: restarted
        enabled: yes
    
    - name: template nginx site config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/hello.test.conf
    
    - name: remove default nginx site config
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent
    
    - name: enable nginx site
      file:
        src: /etc/nginx/sites-available/hello.test.conf
        dest: /etc/nginx/sites-enabled/hello.test.conf
        state: link
        force: yes
    
    - name: restart  nginx service
      systemd:
        name: "nginx"
        state: restarted
        enabled: yes
``` -->


# Améliorer notre playbook avec des variables.

## Variables

Ajoutons des variables pour gérer dynamiquement les paramètres de notre déploiement:

- Ajoutez une section `vars:` avant la section `tasks:` du playbook.

- Mettez dans cette section la variable suivante (dictionnaire):

```yaml
  app:
    name: hello
    user: flask
    domain: hello.test
```

- Remplacez dans le playbook précédent et les deux fichiers de template:
  - toutes les occurence de la chaine `hello` par `{{ app.name }}`
  - toutes les occurence de la chaine `flask` par `{{ app.user }}`
  - toutes les occurence de la chaine `hello.test` par `{{ app.domain }}`

- Relancez le playbook : toutes les tâches devraient renvoyer `ok` à part les "restart" car les valeurs sont identiques.

- Ajoutez deux variables `repository` et `version` pour l'adresse du dépôt git et la version de l'application `master` par défaut.


<!-- ```yaml
app:
  name: hello
  user: flask
  domain: hello.test
  repository: https://github.com/e-lie/flask_hello_ansible.git
  version: master
``` -->

## Ajouter un handler pour nginx et le service

Pour le moment dans notre playbook, les deux tâches de redémarrage de service sont en mode `restarted` c'est à dire qu'elles redémarrent le service à chaque exécution (résultat: `changed`) et ne sont donc pas idempotentes. En imaginant qu'on lance ce playbook toutes les 15 minutes dans un cron pour stabiliser la configuration, on aurait un redémarrage de nginx 4 fois par heure sans raison.

On désire plutôt ne relancer/recharger le service que lorsque la configuration conrespondante a été modifiée. c'est l'objet des taches spéciales nommées `handlers`.

Ajoutez une section `handlers:` à la suite

- Déplacez la tâche de redémarrage/reload de `nginx` dans cette section et mettez comme nom `reload nginx`.
- Ajoutez aux deux taches de modification de la configuration la directive `notify: <nom_du_handler>`.

- Testez votre playbook. il devrait être idempotent sauf le restart de `hello.service`.
- Testez le handler en ajoutant un commentaire dans le fichier de configuration `nginx.conf.j2`.

<!-- ```yml
    - name: template nginx site config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/{{ app.domain }}.conf
      notify: reload nginx

      ...

  handlers:
    - name: reload nginx
      systemd:
        name: "nginx"
        state: reloaded

=> penser aussi à supprimer la tâche de restart de nginx précédente
``` -->

## Rendre le playbook dynamique avec une boucle.

Plutôt qu'une variable `app` unique on voudrait fournir au playbook une liste d'application à installer (liste potentiellement définie durant l'exécution).

- Identifiez dans le playbook précédent les tâches qui sont exactement communes aux deux installation.

!!! il s'agit des taches d'installation des dépendances apt et de vérification de l'état de nginx (démarré)

- Créez un nouveau fichier `deploy_app_tasks.yml` et copier à l'intérieur la liste de toutes les autres taches mais sans les handlers que vous laisserez à la fin du playbook.

!!! Il reste donc dans le playbook seulement les deux premières taches et les handlers, les autres taches (toutes celles qui contiennent des parties variables) sont dans `deploy_app_tasks.yml`.

- Ce nouveau fichier n'est pas à proprement parlé un `playbook` mais une liste de taches. utilisez `include_tasks:` pour importer cette liste de tâche à l'endroit ou vous les avez supprimées.
- Vérifiez que le playbook fonctionne et est toujours idempotent.
- Ajoutez une tâche `debug: msg={{ app }}` au début du playbook pour visualiser le contenu de la variable.


- Ensuite remplacez la variable `app` par une liste `flask_apps` de deux dictionnaires (avec `name`, `domain`, `user` différents les deux dictionnaires et `repository` et `version` identiques).

<!-- ```yml
flask_apps:
  - name: hello
    domain: "hello.test"
    user: "flask1"
    version: master
    repository: https://github.com/e-lie/flask_hello_ansible.git

  - name: hello2
    domain: "hello2.test"
    user: "flask2"
    version: master
    repository: https://github.com/e-lie/flask_hello_ansible.git
``` -->

- Utilisez les directives `loop` et `loop_control`+`loop_var` sur la tâche `include_tasks` pour inclure les taches pour chacune des deux applications.

- Créez le dossier `group_vars` et déplacez le dictionnaire `flask_apps` dans un fichier `group_vars/appservers.yml`. Comme son nom l'indique ce dossier permet de définir les variables pour un groupe de serveurs dans un fichier externe.

- Testez en relançant le playbook que le déplacement des variables est pris en compte correctement.

## Correction

Vous trouverez la correction complète à l'adresse [https://github.com/e-lie/ansible_flask_tp2_correction](https://github.com/e-lie/ansible_flask_tp2_correction).

(`git clone https://github.com/e-lie/ansible_flask_tp2_correction tp2_correction` pour la récupérer).

# Bonus

Pour ceux ou celles qui sont allés vite, vous pouvez tenter de créer une nouvelle version de votre playbook portable entre centos et ubuntu. Pour cela utilisez la directive `when: ansible_os_family == 'Debian'` ou `RedHat`.

# Bonus 2 pour pratiquer

Essayez de déployer une version plus complexe d'application flask avec une base de donnée mysql: [https://github.com/miguelgrinberg/microblog/tree/v0.17](https://github.com/miguelgrinberg/microblog/tree/v0.17)

Il s'agit de l'application construite au fur et à mesure dans un [magnifique tutoriel python](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvii-deployment-on-linux). Ce chapitre indique comment déployer l'application sur linux.
