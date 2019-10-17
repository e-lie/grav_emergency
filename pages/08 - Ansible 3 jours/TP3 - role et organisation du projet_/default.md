---
title: "TP3 Structurer notre projet avec des roles" 
visible: true 
---

## Ajouter un provisionneur d'infra maison pour créer les machines automatiquement

- Dupliquez le dossier du projet tp2 en `tp3_provisionner_roles` ou clonez la correction du TP2 (lien à la fin du TP2).
- Chargez ce dossier dans VSCode (vous pouvez fermer le tp2).

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

- Lancez `sudo lxc list` pour afficher les nouvelles machines de notre infra et vérifier que le serveur de base de données a bien été créé.

## Ajouter une machine mysql simple avec un role externe

- Créez à la racine du projet le dossier `roles` dans lequel seront rangés tous les roles (c'est une convention ansible à respecter).
- Cherchez sur (https://galaxy.ansible.com/)[https://galaxy.ansible.com/] le **nom** du role `mysql` de `geerlingguy`. Il s'agit de l'auteur d'un livre de référence **"Ansible for DevOps"** et de nombreux roles de références.
- Pour décrire les roles nécessaires pour notre projet il faut créer un fichier `requirements.yml` contenant la liste de ces roles. Ce fichier peut être n'importe où mais il faut généralement le mettre directement dans le dossier `roles` (autre convention).

- Ajoutez à l'intérieur du fichier:

```yml
- src: <nom_du_role_mysql>
```

- Pour installez le role lancez ensuite `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Ajoutez la ligne `geerlingguy.*` au fichier `.gitignore` pour ne pas ajouter les roles externes à votre dépot git.

- Pour installer notre base de données, ajoutez un playbook `dbservers.yml` appliqué au groupe `dbservers` avec juste une section:

```yml
    ...
    roles:
        - <nom_role>
```

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

- Les templates et les listes de handlers/tasks sont a mettre dans les fichiers correspondants (voir plus bas)
- Le fichier `defaults/main.yml` permet de définir des valeurs par défaut pour les variables du role. Mettez à l'intérieur une application par défaut:

```yml
flask_apps:
  - name: helloworld
    domain: hello.test
    repository: https://github.com/e-lie/flask_hello_ansible.git
    version: master
    user: flask1
```

Ces valeurs seront écrasées par celles fournies dans le dossier `group_vars` (la liste de deux applications du TP2). Elle est présente pour éviter que le role plante en l'absence de variable (valeurs de fallback).

- Copiez les tâches (juste la liste de tiret sans l'intitulé de section `tasks:`) contenues dans le playbook `appservers` dans le fichier `tasks/main.yml`.

- De la même façon copiez le handler dans `handlers/main.yml` sans l'intitulé `handlers:`.
- Copiez également le fichier `deploy_flask_tasks.yml` dans le dossier `tasks`.
- Déplacez vos deux fichiers de template dans le dossier `templates` du role (et non celui à la racine que vous pouvez supprimer).

- Pour appeler notre nouveau role, supprimez les sections `tasks:` et `handlers:` du playbook `appservers.yml` et ajoutez à la place:

```yml
  roles:
    - flaskapp
```

- Votre role est prêt : lancez `appservers.yml` et debuggez le résultat le cas échéant.

## Ajouter un paramètre d'exécution à notre rôle pour mettre à jour l'application.

Notre role `flaskapp` est jusqu'ici concu pour être un rôle de configuration, idéalement lancé régulièrement à l'aide d'un cron ou de AWX. En particulier, nous avons mis les paramètres `update` et `force` à `false` au niveau de notre tâche qui clone le code avec git. Ces paramètres indiquent si la tâche doit récupérer systématiquement la dernière version. Dans notre cas il pourrait être dangereux de mettre à jour l'application à chaque fois donc nous avons mis `false` pour éviter d'écraser l'application existante avec une version récente.

Nous aimerions maintenant créer un playbook `upgrade_apps.yml` qui contrairement à `configuration.yml` devrait être lancé ponctuellement pour mettre à jour l'application. Il serait bête de ne pas réutiliser notre role pour cette tâche : nous allons rajouter un paramère `flask_upgrade_apps`.

- Remplacez dans la tâche `git` la valeur `false` des paramètres `update` et `force` par cette variable.

!! Vous noterez que son nom commence par `flask_` car elle fait partie du role `flaskapp`. Cette façon de créer une sorte d'espace de nom simple pour chaque role est une bonne pratique.

- Ajoutez une valeur par défaut `no` ou `false` pour cette variable dans le role (defaults/main.yml).

- Créez le playbook `upgrade_apps.yml` qui appelle le role mais avec une section `vars:` qui définit la variable upgrade à `yes` ou `true`.

- Pour tester votre playbook et pourvoir constater une modification de version vous pouvez éditer `group_vars/appservers.yml` pour changer la version des deux applications à `version2`. Le playbook installera alors une autre version de l'application présente dans le dépot git.

- Charger l'application dans un navigateur avec l'une des IPs. Vous devriez voir "version: 2" apparaître en bas de la page.


# Correction avec bonus

Le dépot de correction se trouve à l'adresse: [https://github.com/e-lie/ansible_tp3_et_4_correction](https://github.com/e-lie/ansible_tp3_et_4_correction)

Il contient également des éléments du TP4 (loadbalancer, playbook de déploiement, awx)

# Bonus 

Essayez différents exemples de projets de Geerlingguy accessibles sur github à l'adresse [https://github.com/geerlingguy/ansible-for-devops](https://github.com/geerlingguy/ansible-for-devops).