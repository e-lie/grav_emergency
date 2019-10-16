---
title: 'Configurer un cluster derrière un proxy'
visible: yes
---


## Installons Ubuntu Server

Nous voulons créer un serveur VM ubuntu avec
- un réseau en accès par pont
- au moins 5000MB de RAM et trois processeurs

Au choix:
- Version rapide : importez une machine `elk-node` vierge depuis le partage
  - Connectez vous avec l'utilisateur `elk-admin:el4stic`

- Version plus longue et réaliste:
  - Téléchargez ubuntu server 18.04
  - Insérez le CD et lancez la machine
  - Créez une partition **btrfs** pour la partition racine `/`
  - Créez un utilisateur avec votre nom (ce sera un utilisateur admin/sudo)
  - Serveur éteint, ajoutez un deuxième disque de 10GB dans à votre machine dans la section stockage
  - Allumez et connectez vous en SSH au serveur. passez en mode `sudo -i`.
  - Utilisez `lsblk` pour trouver le nom du disque n°2. normalement il s'agit de `/dev/sdb`.
  - Formattez le disque avec la commande `fdisk /dev/<disque>` comme indiqué dans [ce post](https://superuser.com/questions/643765/creating-ext4-partition-from-console)
  - Relancez `lsblk` pour vérifier qu'une partition sdb1 ou sdc1 a bien été créée
  - Ajoutez le nouveau disque au filesystem btrfs avec `btrfs device add /dev/<partition> /` (exp `/dev/sdb1` mais **PAS** /dev/sdb tout court)
  - `btrfs fi show permet ensuite de constater`
  - Passez le filesystem en raid1 avec `sudo btrfs balance start -v -mconvert=raid1 -dconvert=raid1 /`. Les données sont maintenant dupliquées pour plus de sécurité.

## Configurer LXD

LXD/LXC est une technologie de container pour monter des conteneurs système linux (et non pas des conteneurs d'application comme docker). Nous allons l'utiliser pour créer un cluster de Yunohost puis ajouter un loadbalancer.

- lancez un update et un upgrade du système avec `apt`
- lancez `lxd init`
- Si vous avec fait la version rapide répondez à toutes les questions avec la valeur par défaut en faisant <entrer>
    - mais essayer de comprendre de quoi il est question et demandez
- Sinon appelez le formateur.

- Lancez `lxc list` pour afficher la liste des conteneurs. Il n'y en pas pour le moment.
- Lisez [cette page d'introduction à LXD](https://stgraber.org/2016/03/19/lxd-2-0-your-first-lxd-container-312/) pour trouver comment créer un conteneur ubuntu 18.04.
- Trouvez comment détruire le conteneur.

## Créer un premier conteneur Yunohost et faire une image

- Créez un conteneur debian stretch (`lxc launch images:"debian/stretch/amd64" yuno1`)
- connectez vous dedans avec `lxc exec <conteneur> /bin/bash`
- Dans le conteneur:
    - installez `htop`
    - installez yunohost avec [cette doc](https://yunohost.org/#/install_manually)
    - **NE PAS FAIRE LA POST INSTALL**
    - stoppez le conteneur (`poweroff`)
    - vérifiez que le conteneur est bien arrêté avec `lxc list`
- créez une image à partir de ce conteneur avec [cette doc](https://stgraber.org/2016/03/30/lxd-2-0-image-management-512/) (`lxc publish`)
- Lancez `lxc image list` pour voir si votre image est bien créé et récupérer son *alias*
- À partir de l'image vous pouvez maintenant créer un deuxième conteneur (`lxc launch <alias> yuno2g`)
- Relancez le premier conteneur avec `lxc start <conteneur>`
- Lancez `htop` dans le serveur et dans chacun des conteneurs. Que remarque-t-on ?

## Post installation Yunohost

- Dans chaque yunohost faites la post_installation en ligne de commande avec `yunohost tools postinstall`
- Récupérez l'adresse IP de chacun des conteneurs en lançant `ip a` à l'intérieur ou avec `lxc list`.
- Depuis le serveur ubuntu connectez vous en ssh à vos conteneurs (login admin:<mdp_postinstall>)
- Depuis le serveur ubuntu lancez `wget <ip>`.
- Depuis votre ubuntu de travail (pas le serveur) essayez de:
  - Vous connecter en ssh.
  - Charger la page yunohost dans un navigateur. Qu'est ce qui coince ?

## Ajoutons une règle iptable

Pour que les conteneurs à l'intérieur de notre infra virtuelle soient accessibles depuis le réseau de la salle (adresse du type 192.168.8.x) nous devons créer des ponts réseau entre chaque conteneur et l'interface du serveur. Pour cela on peut créer une **règle iptable**. Iptable est un composant logiciel du noyau linux qui permet de rediriger ou filtrer les paquets réseau à l'intérieur de la machine. Une des principales utilisations de iptable est de configurer un firewall (la plupart des firewall linux sont basés sur iptables).

- iptable fonctionne avec des listes de règles. Pour simplifier on peut dire que ces règles du type :
  "les paquets qui arrivent depuis cette adresse ip doivent être supprimés / ou redirigés vers cette autre adresse".

- Nous allons utiliser l'outil de firewall ubuntu **UFW**(micro firewall) pour ajouter nos règles iptables spécifiques:
  - Éditez le fichier `/etc/ufw/before.rules`
  - Ajoutez ces lignes **à la fin du fichier**:
  
```
#NAT prerouting port forwarding rules to containers
*nat
:POSTROUTING ACCEPT [0:0]
:PREROUTING ACCEPT [0:0]
#Rules
-A PREROUTING -d <ip_entree>/32 -i <interface_entree> -p tcp -m comment --comment yuno1_ssh -m tcp --dport <port_entree> -j DNAT --to-destination <ip_arrivee>:<port_arrivee>
#One commit per table needed (here for nat table)
COMMIT
```

- Complétez avec ces informations (à chercher par le raisonnement ou demandez)
  - L'ip d'entrée est l'ip du paquet lorsqu'il arrive dans votre serveur ubuntu
  - L'interface d'entree est l'interface par laquelle le paquet entre dans le serveur (utilisez `ip a` pour trouver son nom)
  - Le port d'entrée est le port par lequel le paquet est arrivé sur le serveur ubuntu (mettez par exemple `22222`)
  - L'ip d'arrivee est l'ip vers laquelle vous voulez envoyer le paquet.
  - Le port d'arrivée est le port par lequel doit rentrer le paquet à sa destination. (ici nous voulons utiliser le port normal de SSH)
- Sauvegardez le fichier.

- Activez le firewall ufw : `ufw enable` (à chaque modification du fichier `before.rules` relancez `iptables -t nat -F && ufw reload`)
- Autorisons le ssh: `ufw allow ssh`
- Pour vérifier que notre règle est bien chargée lançons `iptable -t nat -L`. Vous devriez voir une ligne avec `\* yuno1_ssh *\` s'afficher. Demandez une vérification de la règle.

- Vous pouvez maintenant vous connecter au premier yunohost depuis votre ubuntu de travail (pas serveur) `ssh -p 22222 admin@192.168.8.<??>`.

Que se passe-t-il ici ? la connexion ssh qui arrive sur le port 22222 est redirigée à l'intérieur du serveur vers le conteneur yunohost numéro 1. Il s'agit d'une règle NAT (network adress translation) car l'IP et le port de chaque paquet sont réécrit à la voléé par iptable pour aller vers la bonne destination.

- Retournez dans le fichier `/etc/ufw/before.rules et copier la ligne de règle en changeant:
  - le port d'entrée par exemple `22223`
  - l'ip d'arrivee pour que les paquets ailles vers le deuxième conteneur
  - le commentaire (changez yuno1 en yuno2)

- Enregistrez et rechargez avec `iptables -t nat -F && ufw reload`.
- Pour vérifier que notre règle est bien chargée lançons `iptable -t nat -L`. Vous devriez voir une ligne avec `\* yuno2_ssh *\` s'afficher. 
- Depuis votre ubuntu de travail connectez vous ssh au deuxième yunohost en utilisant le port 22223.

- Comment faire de même pour le HTTPS ?

Bilan. Nous avons configuré un nat virtuel pour cacher plusieurs serveurs derrière une seule adresse IP. Le port est utilisé pour choisir le bon backend (le serveur qui nous repondra). C'est parfaitement fonctionnel car il est facile de préciser le port lorsqu'on se connecte en SSH.

- En HTTPS cependant, configurer un port personnalisé n'est pas adapté pour héberger un site ! Nous ne voulons pas que les utilisateurs aient à taper une url du type `monsite.com:4443` pour pouvoir trouver le bon site !
Pour pouvoir héberger des sites sur les deux yunohosts avec une seule adresse nous allons devoir configurer un reverse proxy à l'intérieur de notre infra virtuelle.

## Installer et configurer HAProxy

Nous pouvons utiliser pour le proxy un logiciel très répendu comme frontend (facade réseau) des infrastructures d'entreprise : HAProxy. Il sert généralement de loadbalancer pour rediriger le trafic selon des critère dynamique, par exemple la santé des différents backend.

- Installez `haproxy` sur le ubuntu serveur
- Éditez le fichier de configuration `/etc/haproxy/haproxy.cfg`, ajoutez à la fin :

```
# For https traffic, don't decipher use pasthrough tcp mode and route in backend by domain
frontend https_sni_balancing
    mode tcp
    bind *:443

    # active SSL handshake inspection to get domain name info while SSL tcp mode
    tcp-request inspect-delay 5s
    tcp-request content accept if { req_ssl_hello_type 1 }

    default_backend ssl_sni_balancing


frontend http_balancing
	mode http
	bind *:80
    acl <nom_yuno1>_acl hdr(host) -i <yuno1_domain>
    acl <nom_yuno2>_acl hdr(host) -i <yuno2_domain>

    use_backend <nom_yuno1>_backend if <nom_yuno1>_acl
    use_backend <nom_yuno2>_backend if <nom_yuno2>_acl


# For https traffic, use SNI domain info to take routing decision
backend ssl_sni_balancing
    mode tcp
    acl <nom_yuno1>_acl req_ssl_sni -i <yuno1_domain>
    acl <nom_yuno2>_acl req_ssl_sni -i <yuno2_domain>

    use-server <nom_yuno1>_backend if <nom_yuno1>_acl
    use-server <nom_yuno2>_backend if <nom_yuno2>_acl
 
    # default backend
    use-server <nom_yuno1>_backend if !<nom_yuno2>_acl

    option ssl-hello-chk

    server <nom_yuno1>_backend <ip_yuno1>:443 check
    server <nom_yuno2>_backend <ip_yuno2>:443 check

backend <nom_yuno1>_backend
    option httpclose
    option forwardfor
    cookie JSESSIONID prefix
    server <nom_yuno1>_backend <ip_yuno1>:80 cookie A check

backend <nom_yuno2>_backend
    option httpclose
    option forwardfor
    cookie JSESSIONID prefix
    server <nom_yuno2>_backend <ip_yuno2>:80 cookie A check
```

- Redémarrez le service `haproxy` avec `sudo systemctl restart <service>`
- Sur votre ubuntu de travail, modifiez `/etc/hosts` pour faire pointer les domaines de chaque yunohost sur l'ip de votre serveur ubuntu.
- Essayer de charger chacun des yunohost dans un navigateur. 

## Bonus : Faire un tunnel ssh pour exposer notre serveur local sur l'internet

Pour exposer notre serveur virtuel du réseau de la salle sur internet nous pouvons créer un tunnel ssh entre le serveur scaleway utilisé hier et notre machine virtuelle.

- Lisez [cet article sur les tunnels ssh](https://openclassrooms.com/fr/courses/530030-mise-en-place-dun-tunnel-tcp-ip-via-ssh)
- Essayer de décrire comment vous pourriez vous y prendre pour faire un tunnel entre le réseau local et le serveur (faire un dessin).
- Construisez la commande à partir des exemple de l'article.