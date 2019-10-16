---
title: 'Installer Jenkins'
visible: true
---

## Créer une machine pour le Jenkins Master

- Importez avec virtualbox trois noeud ubuntu `elk_node_py2` (à récupérer par exemple dans le partage réseau)

- Utilisez le nom `Jenkins Master`

- Configurer les machines avec `1500Mo` de RAM

- Créez un nouveau réseau NAT JenkinsNat et mettez votre machine ubuntu ainsi le le noeud jenkins à l'intérieur.

- Démarrez les machines

- Récupérez l'ip des noeud jenkins

- Connectez vous en ssh à l'aide du login: `elk-admin` mdp: `el4stic`

## Installer Docker et Jenkins avec Ansible

### Setup Ansible

- Créez un nouveau dossier de projet `Jenkins_infra` avec à l'intérieur un dossier `Ansible_provisionning`
- Initialisez le dépôt git
- Ouvrez le dossier avec VSCode
- Dans `Ansible_provisionning` ajoutez un fichier `ansible.cfg` avec à l'intérieur:
```cfg
[defaults]
inventory = hosts.cfg
host_key_checking = False
```
- Créez le fichier d'inventaire correspondant avec à l'intérieur:

```cfg
[jenkins_nodes]
jenkins_master ansible_host=<ip>
runner1 ansible_host=<ip2>
runner2 ansible_host=<ip3>


[jenkins_nodes:vars]
ansible_user=<login>
ansible_password=<password>
ansible_become_password=<password>
```

- Créez un playbook `setup_docker_java.yml`:

```yaml
---
- hosts: <nom_groupe_jenkins>
  become: yes
  become_method: sudo
  tasks:
    - ping:
```

- Lancez le playbook pour tester le setup d'ansible (`ansible-playbook <monplaybook>`)

### Installer Docker et Java

- Ajoutez les tâches suivantes à compléter au playbook (complétez les valeurs en vous aidant des instructions d'installation de docker : https://docs.docker.com/install/linux/docker-ce/ubuntu/)

```yaml
   - name: install basic requirements
      apt: 
        name: "{{ pkgs }}"
        update_cache: yes
      vars:
        pkgs:
          <liste des packages preinstall>
          
    - name: Add docker developers key
      apt_key:
        url: <url key docker>
        state: present

    - name: Add docker apt repository
      apt_repository:
        repo: <dépot_docker_ubuntu_bionic>
        state: present
      
    - name: Add universe apt repository
      apt_repository:
        repo: "deb http://archive.ubuntu.com/ubuntu bionic universe"
        state: present

    - name: install Docker and Java
      apt: 
        name: "{{ pkgs }}"
        update_cache: yes
      vars:
        pkgs:
          - openjdk-8-jdk
          <docker_packages>
          
    - name: create /var/jenkins
      file:
        path: /var/jenkins
        state: directory
        owner: elk-admin
```

- Lancez à nouveau le playbook pour installer les requirements de base.



## Installer Jenkins

En SSH sur le noeud jenkins master:

- Ajoutez l'utilisateur `elk-admin` au groupe docker grâce à la commande:
```
sudo usermod -aG <groupe> <user>
```
- Redémarrez votre jenkins master

- Cherchez sur [hub.docker.com](hub.docker.com) l'identifiant de l'image officielle jenkins latest (demander confirmation au formateur)

- Créez un dossier `jenkins_home` dans le dossier personnel de elk-admin.

-  Créez un conteneur en mode daemon grâce à la commande : 
```
docker run -d -p <port_hote>:<port_jenkins> -v "$HOME/jenkins_home:/var/jenkins_home" --name jenkins <image_id>
```
	- le paramètre `-d` indique de démarrer le conteneur en mode daemon.
	- `-p` sert pour le mapping de port choissez `80` pour le port hote (le port http par défaut voir cours sur le réseau). Le port de jenkins est `8080`.
	- `-v` permet de monter un volume docker : les données de jenkins se retrouverons dans le dossier ~/jenkins_home et survivrons à la destruction du conteneur.
	- notre conteneur aura simplement le nom`jenkins` dans docker.

- Pour vérifier que le conteneur est correctement démarré utilisez la commande `docker logs <nom_conteneur>`

- Quand le logiciel est prêt la commande précédent affiche des triple lignes d'étoiles `*`. Entre les deux est affiché un token du type: `05992d0046434653bd253e85643bae12`. Copiez ce token.

- Coté ubuntu (pas en ssh) éditez le fichier `/etc/hosts` en root avec `nano`. Ajoutez la ligne suivante à la fin:
```
<ip_du_noeud_jenkins_master>	jenkins.local
```
- Sur ubuntu visitez l'adresse http://jenkins.local. Vous devriez voir une page jenkins s'afficher. Activez le compte administrateur avec le token précédemment récupéré

- Cliquez sur `Installer les plugins recommandés`

- Créez un utilisateur à votre convenance, mettez l'url précédente pour Jenkins

## Créer un premier pipeline

- Cliquez sur créer un nouvel item, sélectionnez le type `pipeline`
- donnez lui le nom `hello` puis collez dans la section `script`
```
pipeline {
    agent any
    stages {
        stage("Hello") {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```
- Sauvegardez. Retournez sur la page d'accueil de Jenkins et lancez votre tache.
- Cliquez le sur le job qui se lance pour voir les logs de la tâche.

## Configurer un noeud Jenkins esclave

Nous allons configurer un noeud esclave pour exécuter les jobs Jenkins. L'architecture master/slave de jenkins permet d'éviter que le serveur soit surchagé lorsque trop de builds sont lancés par une équipe de développeurs. Elle permet également de scaler Jenkins plus facilement.

- Vérifiez que vous pouvez vous connecter à vos runner en ssh depuis le noeud jenkins master.

- Sur l'interface de Jenkins, allez dans `Administrer Jenkins` > `Gérer les noeuds` > `Créez un noeud`. Ajoutez un noeud de type permanent avec le nom `runner1`.
	- 5 executors
	- répertoire de travail `/var/jenkins`
	- Selectionnez la méthode `launch agent via SSH`.
	- mettez l'ip du runner 1
	- Créez de nouveau credentials `globaux` de type `Nom d'utilisateur et mot de passe` et l'ID node_ssh
	- Validez la configuration puis connecter l'agent.

- Faites de même pour le runner2

- configurez votre noeud master (configurer le système) pour mettre son nb d'executor à 0

- Relancez un build de notre pipeline hello et cherchez dans les logs s'il s'est bien lancé par le runner 1 ou 2.

