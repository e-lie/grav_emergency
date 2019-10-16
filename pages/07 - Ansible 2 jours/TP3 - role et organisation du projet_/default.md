---
title: 'TP3 créer un playbook de déploiement d''application flask'
visible: false
---

## Ajouter une machine mysql simple avec un role externe

- Dupliquez le dossier du projet tp2 en `tp3_lab_infra`.
- Chargez ce dossier dans VSCode (vous pouvez fermer le tp2).
- Créez à la racine du projet le dossier `roles` dans lequel seront rangés tous les roles (c'est une convention ansible à respecter).
- Cherchez sur (https://galaxy.ansible.com/)[https://galaxy.ansible.com/] le **nom** du role `mysql` de `geerlingguy`. Il s'agit de l'auteur d'un livre de référence **"Ansible for DevOps"** et de nombreux roles de références.
- Pour décrire les roles nécessaires pour notre projet il faut créer un fichier `requirements.yml` contenant la liste de ces roles. Ce fichier peut être n'importe où mais il faut généralement le mettre directement dans le dossier `roles` (autre convention).
- Ajoutez à l'intérieur du fichier:

```yml
- src: <nom_du_role_mysql>
```

- Pour installez le role lancez ensuite `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Ajoutez la ligne `geerlingguy.*` au fichier `.gitignore` pour ne pas ajouter les roles externes à votre dépot git.

## Ajouter un provisionneur d'infra maison pour créer les machines automatiquement

Dans notre infra virtuelle, nous avons trois machines dans deux groupes. Quand notre lab d'infra grossit il devient laborieux de créer les machines et affecter les ip à la main. En particulier détruire le lab et le reconstruire est pénible. Nous allons pour cela introduire un playbook de provisionning qui va créer les conteneurs lxd en définissant leur ip à partir de l'inventaire.

- modifiez l'inventaire comme suit:

```ini
[all:vars]
ansible_user=<votre_user>

[appservers]
app1 ansible_host=10.x.y.121 container_image=ubuntu_ansible node_state=started
app2 ansible_host=10.x.y.122 container_image=ubuntu_ansible node_state=started

[dbservers]
db1 ansible_host=10.x.y.131 container_image=centos_ansible node_state=started
```

- Ajoutez un dossier `provisioners` avec à l'intérieur un playbook `provision_lxd_infra.yml` contenant:

```yml
- hosts: localhost
  connection: local
  become: yes

  tasks:
    - name: setup linux container for the infrastructure simulation
      lxd_container:
        name: "{{ item }}"
        state: "{{ hostvars[item]['node_state'] }}"
        source:
          type: image
          alias: "{{ hostvars[item]['container_image'] }}"
        profiles: ["default"]
        config:
          security.nesting: 'true' 
          security.privileged: 'false' 
        devices:
          # configure network interface
          eth0:
            type: nic
            nictype: bridged
            parent: lxdbr0
            # get ip address from inventory
            ipv4.address: "{{ hostvars[item].ansible_host }}"

        # uncomment if you installed lxd using snap
        url: unix:/var/snap/lxd/common/lxd/unix.socket
        wait_for_ipv4_addresses: true
        timeout: 600

      register: containers
      loop: "{{ groups['all'] }}"
    


    - name: Config /etc/hosts file accordingly
      lineinfile:
        path: /etc/hosts
        regexp: ".*{{ item }}$"
        line: "{{ hostvars[item].ansible_host }}    {{ item }}"
        state: "present"
      loop: "{{ groups['all'] }}"
```

- Etudions le playbook (explication démo).

- Lancez le playbook avec `sudo` car `lxd` se contrôle en root.

- Lancez `sudo lxc list` pour afficher les nouvelles machines de notre infra.

- Pour installer notre base de données, ajoutez un playbook `dbservers.yml` appliqué au groupe `dbservers` avec juste une section:

```yml
    ...
    roles:
        - <nom_role>
```

- Comme bonne pratique 

- Faire un playbook `configuration.yml` qui importe juste les deux playbooks `flaskapp_deploy.yml` et `dbservers.yml` avec `import_playbook`.

- Lancer la configuration de toute l'infra avec ce playbook.


## Transformer notre playbook en role

- Créer un dossier `flaskapp` dans `roles`.
- Ajoutez à l'intérieur l'arborescence:

```
flaskapp
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── tasks
│   ├── deploy_app_tasks.yml
│   └── main.yml
└── templates
    ├── app.service.j2
    └── nginx.conf.j2
```

- Mettez les templates et les listes de handlers/tasks dans les fichiers correspondants.
- Le fichier `defaults/main.yml` permet de définir des valeurs par défaut pour les variables du role. Mettez à l'intérieur une application par défaut:

```yml
flask_apps:
  - name: helloworld
    domain: hello.test
    repository: https://github.com/e-lie/flask_hello_ansible.git
    version: master
    user: flask1
```

Cette valeur sera écrasée par celle fournie dans le dossier `group_vars` (la liste de deux applications du TP2).

- Copiez les tâches (juste la liste de tiret sans l'intitulé de section `tasks:`) contenues dans le playbook `appservers` dans le fichier `tasks/main.yml`.


- De la même façon copiez le handler dans `handlers/main.yml` sans l'intitulé `handlers:`.
- Copiez également le fichier `deploy_flask_tasks.yml` dans le dossier `tasks`.
- Déplacez vos deux fichiers de template dans le dossier `templates` du role (et non celui à la racine que vous pouvez supprimer).

- Supprimez les section `tasks:` et `handlers:` du playbook `appservers.yml` et ajoutez à la place:

```yml
  roles:
    - flaskapp
```

- Votre role est prêt : lancez `appservers.yml` et debuggez le résultat le cas échéant.


# Correction avec bonus

Le dépot de correction se trouve à l'adresse: [https://github.com/e-lie/ansible_tp3_et_4_correction](https://github.com/e-lie/ansible_tp3_et_4_correction)

Il contient également des éléments du TP4 (loadbalancer, playbook de déploiement, awx)

# Bonus 

Essayez différents exemples de projets de Geerlingguy accessibles sur github à l'adresse [https://github.com/geerlingguy/ansible-for-devops](https://github.com/geerlingguy/ansible-for-devops).