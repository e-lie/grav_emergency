---
title: "TP4 Loadbalancer, AWX et Cloud" 
visible: true
---

## Prérequis : Digitalocean token et clé SSH

- Pour louer les machines dans le cloud pour ce TP vous aurez besoin d'un compte digitalocean. récupérez sur une clé usb:
    - un token d'API digitalocean fourni pour la formation.
    - une fingerprint digitalocean de clé ssh.
    - une paire de clé privée et publique correspondant.

- mettez la paire de clé `id_ssh_tp` et `id_ssh_tp.pub` dans le dossier `~/.ssh/`. La passphrase de cette clé est `trucmuch42`.
- faites `ssh-add` pour vérifier que vous pouvez déverrouiller deux clés (l'ancienne avec votre passphrase et la nouvelle paire que vous venez d'ajouter)

<!-- - Si vous utilisez votre propre compte, vous aurez besoin d'un token personnel. Pour en crée allez dans API > Personal access tokens et créez un nouveau token. Copiez bien ce token et collez le dans un fichier par exemple `~/Bureau/compte_digitalocean.txt`. (important détruisez ce token à la fin du TP par sécurité).

- Copiez votre clé ssh (à créer sur nécessaire): `cat ~/.ssh/id_ed25519.pub`
- Aller sur digital ocean dans la section `account` en haut à droite puis `security` et ajoutez un nouvelle clé ssh. Notez sa fingerprint dans le fichier précédent. -->


## Installer terraform et le provider ansible

Terraform est un outils pour décrire une infrastructure de machines virtuelles et ressources IaaS (infrastructure as a service) et les créer (commander). Il s'intègre en particulier avec AWS, DigitalOcean mais peut également créer des machines dans un cluster VMWare en interne (on premise) pour créer par exemple un cloud mixte.

Terraform n'est pas disponible sous forme de dépôt ubuntu/debian. Pour l'installer il faut le télécharger et le copier manuellement:

```bash
cp /tmp
wget https://releases.hashicorp.com/terraform/0.12.6/terraform_0.12.10_linux_amd64.zip
sudo unzip ./terraform_0.12.10_linux_amd64.zip -d /usr/local/bin/
```
- Testez l'installation avec `terraform --version`

Pour pouvoir se connecter à nos VPS, ansible doit connaître les adresses IP et le mode de connexion ssh de chaque VPS. Il a donc besoin d'un inventaire.

Jusqu'ici nous avons créé un inventaire statique c'est à dire un fichier qui contenait la liste des machines. Nous allons maintenant utiliser un inventaire dynamique c'est à dire un programme qui permet de récupérer dynamiquement la liste des machines et leurs adresses en contactant une API.

- Le meilleur inventaire dynamique pour terraform est [https://github.com/nbering/terraform-inventory/](https://github.com/nbering/terraform-inventory/).

Cependant cet inventaire dépend d'un provider spécifique à installer manuellement pour cela lancez les commandes suivantes:

```
wget https://github.com/nbering/terraform-provider-ansible/releases/download/v1.0.3/terraform-provider-ansible-linux_amd64.zip
mkdir -p ~/.terraform.d/plugins
unzip ./terraform-provider-ansible-linux_amd64.zip -d ~/.terraform.d/plugins/
```

## Cloner le projet modèle


- Pour simplifier le démarrage, clonez le dépôt de base à l'adresse [https://github.com/e-lie/ansible_tp4_v2](https://github.com/e-lie/ansible_tp4_v2).

- Ouvrez le projet `ansible_tp4_v2` avec VSCode.

## Terraform digital ocean


- Le fichier qui décrit les VPS et ressources à créer avec terraform est `provisionner/terraform/main.tf`. Nous allons commentez ensemble ce fichier:

!! La documentation pour utiliser terraform avec digitalocean se trouve ici [https://www.terraform.io/docs/providers/do/index.html](https://www.terraform.io/docs/providers/do/index.html)

Pour terraform puisse s'identifier auprès de digitalocean nous devons renseigner le token et la fingerprint de clé ssh. Pour cela:

- copiez le fichier `terraform.tfvars.dist` et renommez le en enlevant le `.dist`
- collez le token récupéré précédemment dans le fichier de variables `terraform.tfvars`
- ajoutez également la fingerprint de clé ssh à ce fichier.

- Maintenant que ces deux fichiers sont complétés (quoi créer et comment s'identifier auprès du provider) nous pouvons lancer la création de nos VPS:
  - `terraform init` permet à terraform de télécharger les "driver" nécessaire pour s'interfacer avec notre provider. Cette commande crée un dossier .terraform
  - `terraform plan` est facultative et permet de calculer et récapituler les créations modifications de ressources à partir de la description de `main.tf`
  - `terraform apply` permet de déclencher la création des ressources.

- La création prend environ 1 minute.

Maintenant que nous avons des machines dans le cloud nous devons fournir leurs IP à Ansible pour pouvoir les configurer. Pour cela nous allons utiliser un inventaire dynamique.

## Terraform dynamic inventory

- Le meilleur inventaire dynamique pour terraform est [https://github.com/nbering/terraform-provider-ansible/](https://github.com/nbering/terraform-inventory/). 


Une bonne intégration entre Ansible et Terraform permet de décrire précisément les liens entre resource terraform et hote ansible ainsi que les groupes de machines ansible. Pour cela notre binder propose de dupliquer les ressources dans `main.tf` pour créer explicitement les hotes ansible à partir des données dynamiques de terraform.

- Ouvrons à nouveau le fichier `main.tf` pour étudier le mapping entre les ressources digitalocean et leur duplicat ansible.

- Pour vérifier le fonctionnement de notre inventaire dynamique lancez:

```
source .env
./inventory_terraform.py
```

- La seconde appelle l'inventaire dynamique et vous renvoie un résultat en json décrivant les groupes, variables et adresses IP des machines crées avec terraform.

- Complétez le `ansible.cfg` avec le chemin de l'inventaire dynamique: `./inventory_terraform.py`

- Nous pouvons maintenant tester la connexion avec ansible directement: `ansible all -m ping`.


## Infrastructure multi-tiers avec load balancer

Pour configurer notre infrastructure simple:

- Installez les roles avec `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Lancez le playbook `site.yml`

- Ajoutez `hello.test` et `hello2.test` dans /etc/hosts pointant vers l'ip de `balancer1`

- Chargez la page de l'app.

- Observons ensemble l'organisation du code Ansible de notre projet.
    - Nous avons rajouté à notre infrastructure un loadbalancer installé à l'aide du fichier `balancers.yml`
    - Le playbook `upgrade_apps.yml` permet de mettre à jour l'application en respectant sa haute disponibilité. Il s'agit d'une opération d'orchestration simple en les 3 serveurs de notre infrastructure.
    - Cette opération utilise en particulier `serial` qui permet de d'exécuter séquentiellement un play sur un fraction des serveurs d'un groupe (ici 1 à la fois parmis les 2).
    - Notez également l'usage de `delegate` qui permet d'exécuter une tache sur une autre machine que le groupe initialement ciblé. Cette directive est au coeur des possibilités d'orchestration Ansible en ce qu'elle permet de contacter un autre serveur ( déplacement latéral et non pas master -> node ) pour récupérer son état ou effectuer une modification avant de continuer l'exécution et donc de coordonner des opérations.
    - notez également le playbook `exclude_backend.yml` qui permet de sortir un backend applicatif du pool. Il s'utilise avec des variables en ligne de commande


- Désactivez le noeud qui vient de vous servir la page en utilisant le playbook `exclude_backend.yml`:

```
ansible-playbook --extra-vars="backend_name=<noeud a desactiver> backend_state=disabled" playbooks/exclude_backend.yml
```

- Rechargez la page: vous constatez que c'est l'autre backend qui a pris le relais.

- Nous allons maintenant mettre à jour

## Ajoutons un conteneur AWX

- Choisissez un mot de passe et chiffrez le avec `ansible-vault encrypt_string <votre_mot_de_passe>`.

- Copiez le résultat et collez le comme valeur d'une variable `awx_admin_password` à la place de `unsecurepass` dans le fichier `group_vars/awxservers.yml` comme suit:

```yml
awx_admin_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    37336233643131373366333466313564303764383339353764383939353265616466633761613264
    3862663431343163353639313038623037343261363036310a366434386635333734356638353439
    34636338623866353363643232646539366532343061633037666636383136653932306563633538
    6532343239636637340a366661373039373138303737373837343639376532393962323763343139
    6538
```

- Lancez le playbook `install_awx.yml` avec la commande: `ansible-playbook --ask-vault-pass playbooks/install_awx.yml`. Déverrouillez votre vault avec le mdp précédent.

- L'installation prend un certain temps à se terminer.  Relancez la si nécessaire.

- Lancez l'inventaire dynamique pour récupérer l'ip de `awx0` : `source .env && ./inventory_terraform.py`

- Visitez cette dans un navigateur: AWX devrait démarrer.

- Complétez l'inventaire statique `inventory.cfg` avec les ip de l'inventaire dynamique (pour que la config soit plus simple une fois dans AWX).

- Poussez sur **github** ou **gitlab** votre version complétée du projet (en mode public la encore pour faciliter la configuration).

# Explorer AWX

- Identifiez vous sur awx avec le login `admin` et le mot de passe précédemment configuré.

- Dans la section modèle de projet, importez votre projet. Un job d'import se lance. Si vous avez mis le fichier `requirements.yml` dans  `roles` les roles devraient être automatiquement installés.

- Dans la section crédentials, créez un crédential de type machine. Dans la section clé privée copiez le contenu du fichier `~/.ssh/id_ssh_tp` que nous avons configuré comme clé ssh de nos machines. Ajoutez également la passphrase que vous avez configuré au moment de la création de cette clé.

- Créez une ressource inventaire. Créez simplement l'inventaire avec un nom au départ. Une fois créé vous pouvez aller dans la section `source` et choisir de l'importer depuis le `projet`, sélectionnez `inventory.cfg` que nous avons configurer précédemment. Bien que nous utilisions AWX les ip n'ont pas changé car AWX est en local et peut donc se connecter au reste de notre infrastructure LXD.

- Pour tester tout cela vous pouvez lancez une tâche ad-hoc `ping` depuis la section inventaire en sélectionnant une machine et en cliquant sur le bouton `executer`.

- Allez dans la section modèle de job et créez un job en sélectionnant le playbook `site.yml`.

- Exécutez ensuite le job en cliquant sur la fusée. Vous vous retrouvez sur la page de job de AWX. La sortie ressemble à celle de la commande mais vous pouvez en plus explorer les taches exécutées en cliquant dessus.

- Modifiez votre job, dans la section `Plannifier` configurer l'exécution du playbook site.yml toutes les 15 minutes.

- Allez dans la section plannification. Puis visitez l'historique des Jobs.
