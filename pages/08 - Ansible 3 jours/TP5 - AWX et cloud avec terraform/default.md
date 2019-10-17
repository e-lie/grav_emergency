---
title: "TP5 Terraform, Cloud et AWX" 
visible: false
---

## Prérequis : Digitalocean token et clé SSH

- Pour louer les machines dans le cloud pour ce TP vous aurez besoin d'un compte digitalocean : créez en un le cas échéant si un token d'API ne vous est pas fourni pour la formation.

- Si vous utilisez votre compte, vous aurez besoin d'un token personne. Pour en crée allez dans API > Personal access tokens et créez un nouveau token. Copiez bien ce token et collez le dans un fichier par exemple `~/Bureau/compte_digitalocean.txt`. (important détruisez ce token à la fin du TP par sécurité).

- Copiez votre clé ssh (à créer sur nécessaire): `cat ~/.ssh/id_ed25519.pub`
- Aller sur digital ocean dans la section `account` en haut à droite puis `security` et ajoutez un nouvelle clé ssh. Notez sa fingerprint dans le fichier précédent.

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

- Dans ce projet nous allons générer l'arborescence suivante:

```bash
tp1_terraform_swarm
├── ansible_roles
│   ├── docker-engine
│   │   ├── defaults

TODO

```

- Pour simplifier le démarrage, clonez le dépôt de base à l'adresse [TODO](https://gitlab.com/e-lie/ansible_tp5_terraform).

- Ouvrez le projet `ansible_tp5_terraform` avec VSCode.


## Terraform digital ocean

Nous pouvons maintenant compléter les éléments manquant dans ce projet d'exemple. Ouvrez le terminal dans VSCode et naviguez 

- Le fichier qui décrit les VPS et ressources à créer est `main.tf`. Nous allons complétez ce fichier:
  
- D'abord le provider de cloud à utiliser il s'agit pour nous de DigitalOcean:

```terraform
provider "digitalocean" {
    token = "${var.do_api_token}"
}
```

- La documentation pour utiliser terraform avec digitalocean se trouve ici [https://www.terraform.io/docs/providers/do/index.html](https://www.terraform.io/docs/providers/do/index.html)

- Nous pouvons maintenant configurer les VPS à créer. Complétez le nom de l'image linux à utiliser pour créer le VPS (droplet): `ubuntu-18-04-x64` (slug très pénible à trouver).


- Enfin précisons le nombre de noeuds manager et worker que nous voulons créez: mettez 1 de chaque pour le moment.


- Passons maintenant à `terraform.tfvars`:
  - copiez le fichier `terraform.tfvars.dist` et renommez le en enlevant le `.dist`
  - rendez vous sur votre compte digital ocean pour créer un token et copiez immédiatement le hash du token.
  - collez le token dans le fichier de variables `terraform.tfvars`
  - ajoutez également votre clé ssh à votre compte DigitalOcean et ajoutez sa signature au fichier précédent.

- Maintenant que ces deux fichiers sont complétés (quoi créer et comment s'identifier auprès du provider) nous pouvons lancer la création de nos VPS:
  - `terraform init` permet à terraform de télécharger les "driver" nécessaire pour s'interfacer avec notre provider. Cette commande crée un dossier .terraform
  - `terraform plan` est facultative et permet de calculer et récapituler les créations modifications de ressources à partir de la description de `main.tf`
  - `terraform apply` permet de déclencher la création des ressources.

- La création prend quelques secondes (max 1 minute ?). Vous pouvez ensuite allez voir l'interface de digital ocean pour constater la création des VPS.


## Terraform provider ansible

- Le meilleur inventaire dynamique pour terraform est [https://github.com/nbering/terraform-provider-ansible/](https://github.com/nbering/terraform-provider-ansible/). Suivez les instructions d'installation.

- Téléchargez également ce script qui servira effectivement d'inventaire: `wget https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py`

[https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py](https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py)

Une bonne intégration entre Ansible et Terraform permet de décrire précisément les liens entre resource terraform et hote ansible ainsi que les groupes de machines ansible. Pour cela notre binder propose de dupliquer les ressources dans `main.tf` pour créer explicitement les hotes ansible à partir des données dynamiques de terraform.

- Complétez le `main.tf` avec le code suivant:

```terraform
## Ansible mirroring hosts section
# Using https://github.com/nbering/terraform-provider-ansible/ to be installed manually (third party provider)
# Copy binary to ~/.terraform.d/plugins/

resource "ansible_host" "ansible_managers" {
  count = "${local.swarm_manager_count}"
  inventory_hostname = "manager_${count.index}"
  groups = ["swarm_nodes"]
  vars = {
    ansible_host = "${element(digitalocean_droplet.managers.*.ipv4_address, count.index)}"
  }
}

resource "ansible_host" "ansible_workers" {
  count = "${local.swarm_worker_count}"
  inventory_hostname = "worker_${count.index}"
  groups = ["swarm_nodes"]
  vars = {
    ansible_host = "${element(digitalocean_droplet.workers.*.ipv4_address, count.index)}"
  }
}

resource "ansible_group" "all" {
  inventory_group_name = "all"
  vars = {
    ansible_python_interpreter = "/usr/bin/python3"
  }
}
```

- Complétez le `ansible.cfg` avec:
  - le chemin de l'inventaire dynamique précédemment installé
  - le chemin du dossier de roles ansible de notre projet. Créez le cas échéant (`ansible_roles`)


- Créer les nouvelles ressources virtuelles ansibles avec `terraform apply`


- Testez l'inventaire dynamique :  `chmod +x terraform.py && ./terraform.py`. Vous devriez avoir du texte JSON en retour de ce programme. Interprétons le : les différentes machines avec leurs ip et groupes au format standard inventaire json ansible.

- Nous pouvons maintenant tester la connexion avec ansible directement: `ansible swarm_nodes -m ping`. Il s'agit d'une commande ad-hoc. Ces commandes sont très pratiques pour.
