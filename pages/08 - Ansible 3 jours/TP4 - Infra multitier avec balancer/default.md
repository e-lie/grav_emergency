---
title: "TP4 Loadbalancer, AWX et Cloud" 
visible: false
---


## Infrastructure multi-tiers avec load balancer

- Comme pour le TP précédent, dupliquez le dossier du projet tp3 en `tp4_loadbalancer` ou clonez la correction du TP3 (lien à la fin du TP3).

<!-- - Chargez ce dossier dans VSCode (vous pouvez fermer le tp2).
- Clonez le projet exemple : [https://github.com/e-lie/ansible_tp3_et_4_correction](https://github.com/e-lie/ansible_tp3_et_4_correction) -->

- Complétez l'inventaire avec
    - le nom de l'utilisateur login de vos conteneurs
    - les `x` et `y` de votre range d'ip lxd
    - le nom de vos images lxd ubuntu et centos

- Lancez le playbook `provisionner/provision_lxd_infra.yml` avec `sudo`

- Lancez `ansible all -m ping` pour tester les machines.

<!-- ### Installer un loadbalancer HAProxy

- Ajouter le role à `requirements.yml`.
- Ajouter un playbook d'installation et ajouter à configuration.yml
- faire d'abord la version statique en utilisant le module `haproxy`

### Configurer les backend dynamiquement

- Ajouter deux instances de apps.
- filtre

### Ajouter un peu d'orchestration sur `upgrade_apps.yml` pour la haute disponibilité.

- étude du problème
- utiliser serial pour limiter l'exécution à 1 backend
- tester
- utiliser le module haproxy pour désactiver un backend
- utiliser delegate -->



- Installez les roles avec `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Observons le projet (demo / commentaire).

- Lancez le playbook `site.yml`

- Ajoutez `hello.test` dans /etc/hosts pointant vers l'ip de `balancer1`

- Chargez la page de l'app.
- Éteignez le noeud qui vient de vous servir la page puis rechargez la.
- Allez regarder la configuration de haproxy dans le noeud balancer.

- Clonez l'application flask_app_ansible, modifiez une partie du message affiché dans `app.py`.
- Idéalement committer puis poussez ce code sur `github` ou `gitlab` en mode public.
- Faite pointer l'une de vos application vers ce nouveau dépot (dans group_vars/appservers.yml) puis utilisez le playbook `upgrade_apps.yml` pour mettre à jour l'application. 

## Ajoutons un conteneur AWX

- Vérifiez que vous avez au moins 4Go de ram disponible et 17G de disque à l'intérieur du pool de conteneur. Pour cela connectez vous en ssh à un conteneur et lancez `df -ah` la première ligne vous indique le disque occupé et restant dans le pool. Si votre pool est trop petit vous pouvez arrêter vos conteneurs détruire le profil `default` de `lxd` puis le storage `default` et enfin relancez `lxd init` en choisissant au moins 25G d'espace à la création du pool.

- Choisissez un mot de passe et chiffrez le avec `ansible-vault encrypt_string <votre_mot_de_passe>`.

- Copiez le résultat et collez le comme valeur d'une variable `awx_admin_password` dans un fichier `group_vars/awx.yml` comme suit:

```yml
awx_admin_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    37336233643131373366333466313564303764383339353764383939353265616466633761613264
    3862663431343163353639313038623037343261363036310a366434386635333734356638353439
    34636338623866353363643232646539366532343061633037666636383136653932306563633538
    6532343239636637340a366661373039373138303737373837343639376532393962323763343139
    6538
```

- Assurez vous que seul les 7 conteneurs de l'infra en cours sont allumés.

- Lancez le playbook `install_awx.yml` avec la commande: `ansible-playbook --ask-vault-pass playbooks/install_awx.yml`. Déverrouillez votre vault avec le mdp précédent.

 - L'installation prend un certain temps à se terminer.  Relancez la si nécessaire.

- Ajoutez `awx.exemple` dans `/etc/hosts` pointant vers l'ip de `awx1`

- Visitez `awx.exemple` dans un navigateur.


# Explorer AWX

- Poussez sur **github** ou **gitlab** votre version complétée du projet (en mode public pour faciliter la configuration).

- Dans la section modèle de projet, importez votre projet. Un job d'import se lance. Si vous avez mis le fichier `requirements.yml` dans  `roles` les roles devraient être automatiquement installés.

- Dans la section crédentials, créez un crédential de type machine. Dans la section clé privée copiez le contenu du fichier `~/.ssh/id_ed25519` que nous avons configurer comme clé ssh de nos machines. Ajoutez également la passphrase que vous avez configuré au moment de la création de cette clé.

- Créez une ressource inventaire. Créez simplement l'inventaire avec un nom au départ. Une fois créé vous pouvez aller dans la section `source` et choisir de l'importer depuis le `projet`, sélectionnez `inventory.cfg` que nous avons configurer précédemment. Bien que nous utilisions AWX les ip n'ont pas changé car AWX est en local et peut donc se connecter au reste de notre infrastructure LXD.

- Pour tester tout cela vous pouvez lancez une tâche ad-hoc `ping` depuis la section inventaire en sélectionnant une machine et en cliquant sur le bouton `executer`.

- Allez dans la section modèle de job et créez un job en sélectionnant le playbook `site.yml`.

- Exécutez ensuite le job en cliquant sur la fusée. Vous vous retrouvez sur la page de job de AWX. La sortie ressemble à celle de la commande mais vous pouvez en plus explorer les taches exécutées en cliquant dessus.

- Modifiez votre job, dans la section `Plannifier` configurer l'exécution du playbook site.yml toutes les 15 minutes.

- Allez dans la section plannification. Puis visitez l'historique des Jobs.


## Déploiement dans le cloud avec Terraform

Vous pouvez suivre ce TP pour expérimenter l'intégration de Ansible et Terraform: [https://eliegavoty.fr/devops/09%20-%20cycle%20devops%20cybermaker&co%20juin2019/ansible/tp1%20-%20terraform%20ansible%20swarm%20-%20the%20scaling](https://eliegavoty.fr/devops/09%20-%20cycle%20devops%20cybermaker&co%20juin2019/ansible/tp1%20-%20terraform%20ansible%20swarm%20-%20the%20scaling)