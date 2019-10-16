---
title: "TP4 Loadbalancer, AWX et Cloud" 
visible: false
---


## Infrastructure multi-tiers avec load balancer

- Clonez le projet exemple : [https://github.com/e-lie/ansible_tp3_et_4_correction](https://github.com/e-lie/ansible_tp3_et_4_correction)

- Complétez l'inventaire avec
    - le nom de l'utilisateur login de vos conteneurs
    - les `x` et `y` de votre range d'ip lxd
    - le nom de vos images lxd ubuntu et centos

- Lancez le playbook `provisionner/provision_lxd_infra.yml` avec `sudo`

- Lancez `ansible all -m ping` pour tester les machines.

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

- Lancez le playbook `install_awx.yml` avec la commande: `ansible-playbook --ask-vault-pass playbooks/install_awx.yml`

- L'installation prend un certain temps à se terminer.

- Ajoutez `awx.exemple` dans `/etc/hosts` pointant vers l'ip de `awx1`

- Visitez `awx.exemple` dans un navigateur.


# Explorer AWX

- Poussez sur github ou gitlab votre version complétée du projet

- Importez votre projet

- Lancez une tâche ad-hoc

- Lancez le playbook site.yml

- Plannifier site.yml toutes les 15 minutes

- Lancez le playbook


## Déploiement dans le cloud avec Terraform

Vous pouvez suivre ce TP pour expérimenter [https://eliegavoty.fr/devops/09%20-%20cycle%20devops%20cybermaker&co%20juin2019/ansible/tp1%20-%20terraform%20ansible%20swarm%20-%20the%20scaling](https://eliegavoty.fr/devops/09%20-%20cycle%20devops%20cybermaker&co%20juin2019/ansible/tp1%20-%20terraform%20ansible%20swarm%20-%20the%20scaling)