---
title: 'Introduction aux réseaux avec Yunohost'
visible: 'yes'
---

Pourquoi utiliser Yunohost ici ? Parce qu'il permet de déployer rapidement un serveur avec diverses applications utilisant des protocoles réseaux différents.

## Adresses et architectures du réseau virtuel

### Installer Yunohost dans une VM
- Téléchargez l'image Yunohost 64bits pour Virtualbox sur le site officiel.
- Créez une nouvelle machine virtuelle Debian 64 bits ...
	- ... avec 1024 MB de mémoire RAM ...
	- ... avec comme paramètre réseau `NAT` (paramètre par défaut) (et non pas réseau NAT)
	- ... ajoutez l'image iso de yunohost dans la partie stockage (petite icone de CD en heut à droite)

- Lancez la machine, puis installez YunoHost.
- La machine redémarre,
	- récupérez l'adresse ip de la machine affichée au démarrage (10.0.2.x)
	- **ne pas lancer** la post-installation pour le moment : répondre `n` à la question.

- Pour faire la post-installation nous aimerions accéder au serveur depuis un navigateur web (protocole HTTPS)
	- Essayez d'y accéder directement depuis votre navigateur sur windows.
	- Essayez d'y accéder directement depuis votre navigateur sur ubuntu.
	- Trouvez une solution simple pour rendre le serveur joignable depuis ubuntu ? (indice : comme dans les TPs précédents).
	- Ne faites toujours pas la post-installation.

### Scanner le réseau

- Scanner le réseau:
	- Installez `arp-scan`
	- Lancez `sudo arp-scan --interface=<interface> <CIDR>`
		- pour trouver le nom de l'interface utilisez `ip addr` (ou `ifconfig` mais ifconfig est déprécié depuis quelques années)
		- pour trouver le bon [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) (*Classless Inter-Domain Routing*, en gros une plage d'adresses IP)
			- rélféchissez à la forme probable de l'adresse à scanner (par exemple 10.0.2.x ou 192.168.x.y)
			- remplacez les inconnues par des 0
			- ajoutez `/24` si une seule inconnue et `/16` si deux inconnues
			- le résultat devrait ressembler à `172.5.0.0/16` par exemple
	- Cette commande affiche l'adresse IP (à gauche) et l'adresse MAC (hardware, au centre) de toutes les machines accessible dans le range d'adresse indiqué
		- Quelle est l'adresse MAC de votre **Ubuntu** et de votre serveur **Yunohost** ?

### Configurer le DHCP VirtualBox

- Changons les adresses des machines. Les adresses IP des machines sont pour le moment définies non pas par les machines elles-même mais par un serveur d'adresse dynamique nommé [DHCP](https://fr.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)(Dynamic Host Configuration Protocol) qui calcule et distribue des IP.
	- Pour le réseau NAT de nos machines, le DHCP est un DHCP "virtuel" proposé par le logiciel Virtualbox.
	- Pour le réseau de la salle le DHCP est un service configuré par l'administrateur système pour fournir des adresses IP à chaque machine.
		- Dans une invite de commande windows utilisez la commande `ipconfig` pour trouver l'adresse de votre machine physique windows.
	- Les deux serveurs ne donne pas des adresses dans le même range (CIDR).

- Créez un nouveau réseau NAT `yuNAT` en configurant le CIDR du DHCP pour choisir des adresses du type `10.0.3.x`.

- Mettez à l'intérieur du NAT la machine ubuntu (et pas yunohost)

- Comment faire (en modifiant le paramètre DHCP) pour dire à virtualbox de donner à coup sur l'adresse `192.168.7.77` à la machine ?
  - indice: la fin du CIDR `/24` indique qu'on a 256 adresses IP disponibles car:
  $ 2^{32-24} = 256 $
  - nous voulons avoir seulement **1** IP dans le CIDR.
  - n'hésitez pas à demander ou réfléchir à plusieurs

---
## *Cours sur le réseau et le protocole IP*
---


## Exercices sur les adresses IPv4

- Calculez le nombre d'adresses IPv4 disponibles dans les CIDR suivants et précisez l'adresse minimale et maximale du range.
	- 10.0.1.0/24 -> 256 adresses allant de 10.0.1.0 à 10.0.1.255
	- 10.0.2.0/24
	- 10.0.3.0/24
	- 10.0.2.0/2
	- 10.0.3.128/25
	- 78.0.0.0/8
	- 78.191.0.0/10

- Indiquer comment diviser les CIDR suivant en quatre CIDR équitables en nombre d'adresses IP.
	- 10.0.3.0/24
	- 192.168.1.128/25
	- 192.168.0.0/23


## Réseau avec **Bridge** et configuration DNS du serveur.

### Réseau Bridge

- Modifiez le paramètre réseau des machines Ubuntu et Yunohost pour faire un réseau de type accès par pont (Bridge Network).
- Cherchez la nouvelle adresse IP de votre machine Ubuntu et Yunohost (en redémarrant le serveur ou avec le login `root:yunohost`)
  - notez qu'avec l'accès par pont vous récupérez une adresse du réseau local de la salle.
- Lancez un scan ARP comme à la partie précédente. pour trouvez le bon CIDR il faut se reporter à la nouvelle adresse.
  - Avec le bon CIDR, vous devriez voir les machines de tous les autres apparaître dans le scan.
  - Notez les adresses MAC de vos deux machines (utilisez `| grep <votreip>` après l'arp-scan)

- Essayez d'accéder à l'interface de Yunohost depuis votre navigateur sur **Windows**.
	- Le warning est normal car nous n'avons pas encore configuré de certificat HTTPS.
	- Ajoutez une exception.
- Lancez la post installation avec le nom de domaine de votre choix. exp monserver.com
- Essayez de d'accéder à votre nom de domaine (monserver.com) depuis le navigateur.
- Après la post installation vous pouvez vous connecter au serveur soit depuis l'interface d'administration `https://<votre_domaine>/yunohost/admin` soit en ssh avec le login `admin:<votre_mdp_postinstallation>` (le login root:yunohost ne fonctionne plus désormais !)


### Faux DNS avec `/etc/hosts`

- Il manque une configuration **DNS**(Domain Name Server) pour pouvoir accéder à votre serveur
- Pour faire fonctionner un DNS publiquement il faut être propriétaire du nom de domaine.
- Pour simuler le nom de domaine localement il est possible de modifier le fichiers `hosts` de sa machine locale pour lui indiquer manuellement quel nom de domaine correspond à quelle adresse IP.
  - Récupérez quelques adresses IP et domaines des autres en plus des votres.
  - Éditez le fichier `/etc/hosts` pour les y ajouter.
  - Ajoutez un utilisateur et installez l'application Wordpress dans Yunohost.
  - Installez Rainloop et essayez d'envoyer un mail. Pourquoi ce ne fonctionne-t-il pas ?

---
## *Cours sur les protocoles d'application* 
---

## Migration dans le cloud, découverte des protocoles d'application

### Migration en SSH

Nous allons maintenant migrer le serveur dans le cloud. Pour cela nous allons utilisez la fonction de backup de Yunohost.
- Demandez l'IP d'un serveur déployé pour le TP dans le cloud.
- Nous voulons faire communiquer les deux serveurs en ssh. Depuis votre ubuntu ouvrez un terminal avec deux onglets.
	- Dans l'un d'eux connectez vous en ssh à la VM yunohost, lancez la commande avec l'option `-v`.
	- Dans l'autre connectez-vous en ssh au serveur de cloud également avec l'option `-v`.
	- A quoi sert cette option ? Quelle information utile peut-elle nous fournir ?

- SSHFS est un logiciel qui permet de monter un disque dur virtuel par dessus le protocole SSH.
	- Nous aimerions monter le dossier `/home/yunohost.backup` du serveur de cloud dans la VM.
	- créer le dossier **des deux cotés**: `sudo mkdir -p /home/yunohost.backup`
	- du côté de la VM lancez: `sudo rm -Rf /home/yunohost.backup/*` attention avec cette commande à bien mettre le bon chemin
	- A quel utilisateur appartient ce dossier (utilisez par exemple `ls -l`) ?
	- Il nous faut pouvoir nous connecter en root au serveur de cloud depuis la VM. Pour cela nous avons besoin de créer une nouvelle clé SSH sur la VM. (`sudo ssh-keygen -t ed25519`)
	- Puis copiez la clé publique dans `/root/.ssh/id_ed25519.pub` et ajoutez la dans le fichier `/root/.ssh/authorized_keys` du côté du serveur de cloud (s'il n'existe pas créez le fichier).
	- Il est considéré comme une bonne pratique pour la sécurité de changer le port de connexion ssh et, lorsqu'on a besoin de se connecter en root de le faire à l'aide d'une clé ssh:
      	- Sur le serveur de vloud éditez le fichier `/etc/ssh/sshd_config` avec nano
      	- Remplacez `Port 22` par `Port 2223` ou autre port
      	- Vérifiez que la ligne `PermitRootLogin prohibit-password` est présente.
    	- Enregistrez le fichier
    	- rechargez la configuration du service `sshd` avec `systemctl reload <service>`
    - Installez SSHFS sur la VM.
	- sa syntaxe fonctionne comme suit:
	```
	sshfs -p <port_ssh> <dossier_local> <user>@<ip>:<dossier_distant>
	```
	- montez le dossier backup `/home/yunohost.backup` à la place du dossier backup du serveur de cloud `/home/yunohost.backup`.
	- Pour vérifier que le dossier réseau est bien monté, créez un fichier sur la VM avec `touch /home/yunohost.backup/truc` et vérifiez que ce fichier est bien présent également sur le serveur de cloud.

	- Allez dans l'interface d'administration du yunohost de la VM dans la section backup.
	- Effectuez un backup de l'intégralité du serveur et des applications (cases à cocher)
	- vérifiez dans le dossier de backup de la VM que deux fichiers ont bien été ajoutés.
	- Allez dans le dossier de backup du serveur de cloud pour constater que les fichiers de backup sont bien présents.
	- Pour restaurer le serveur lancez la commande : `yunohost backup restore <nom_d'archive>` ou le nom est celui de l'archive de backup `.tar.gz` mais **sans le `.tar.gz`**.
	- Éditer le fichier hosts pour que le domaine de votre VM pointe désormais vers l'IP du serveur de cloud.
	- Vous pouvez maintenant vous connectez à l'interface d'administration depuis un navigateur.

### Configurer les entrées DNS d'un véritable nom de domaine.

#### Acheter un domaine (facultatif)
- Pour configurer soi-même un nomde domaine il faut le posséder. Si vous voulez vous pouvez acheter un nom de domaine chez ovh.com (entre 3 et 9€ par an). Une fois le domaine acheté:
- Allez dans la section `Domaines` du panneau d'admistration de Yunohost
  - Ajoutez un nouveau domaine : celui que vous venez d'acheter.
  - Affichez la configuration DNS (bouton dans la section du nouveau domaine)
- Dans l'[interface d'administration d'ovh](https://www.ovh.com/managerv3/) trouvez l'onglet `Zone DNS`
 - Ajoutez les différentes entrées en mode expert/texte en copiant et collant la configuration depuis l'interface d'administration de Yunohost.

#### Aternative (prendre un domaine autoconfiguré yunohost)
- Créer un nouveau nom de domaine sur l'admin yunohost en choisissant l'option: je n'ai pas de nom de domaine.
- Les DNS de ce nom de domaine sont configurés automatiquement.

#### Suite

Dans l'interface d'admin de Yunohost:
- Changez le domaine principal pour utiliser le nouveau domaine (section domaines)
- Changez le domain d'installation des deux applications (section applications)
- Installez le certificat SSL Lets Encrypt pour votre serveur (section domaines)
- Supprimez l'entrez du domaine dans votre fichier `hosts`
    - Votre site maintenant accessible grâce à un véritable DNS.
    - Normalement, vous n'avez plus de warning SSL dans le navigateur.

## Tester la stack mail et XMPP

Grâce à la [documentation de yunohost](https://yunohost.org/#/docs), trouvez comment :

- Configurez le client mail thunderbird sur ubuntu.
- Essayer d'envoyer des emails.
- (facultatif) Testez la confiance dans votre serveur mail grace à (http://mail-tester.com/)[http://mail-tester.com/]

- Lisez [la page yunohost](https://yunohost.org/#/XMPP_fr) sur le protocole XMPP. Il s'agit d'un protocole de chat décentralisé.
- Installez `gajim`. Ajoutez le compte XMPP de votre serveur (votre adresse email et le mot de passe de votre utlisateur) ([documentation]())
- Essayer de chatter avec les autres participants.

- Dans l'interface d'administration de yunohost (dans le cloud), allez dans la sections `services`. Vous pouvez voir tous les services qui tournent sur la machine.
  - `nginx` est le serveur web (HTTP) qui sert les pages. si vous l'arrêtez, l'interface web cessera de fonctionner mais le ssh sera toujours fonctionnel.
  - `postfix` est le serveur mail (SMTP) qui permet d'envoyer et recevoir des emails.
  - `dovecot` est le serveur IMAP qui permet aux clients mails de communiquer avec le serveur.
  - `metronome` est le serveur XMPP.
  - `ssh`
  - `php-7.0` qui permet aux applications php de fonctionner.
  - `mysql` la base de données pour les applications installées sur Yunohost.
  - etc.
- Avec Yunohost vous avez déjà un serveur plutôt complexe et complet.

- En ssh sur le serveur de cloud, avec la commande `yunohost firewall list` affichez les ports ouverts.
  - quel port correspond à quelle application ? 

## Wireshark pour observer les paquets réseau

Wireshark est un logiciel pour "renifler" le réseau.
- Installez wireshark sur ubuntu.
- Ajoutez votre utilisateur au groupe `wireshark ` avec la commande `usermod -aG wireshark <username>`
- Déconnectez vous et reconnectez vous.
- Vous pouvez maintenant observez les différents paquets qui transitent sur le réseau local en cliquant sur le bouton aleron de requin bleu.
- Regardez ce [tutoriel quebécois bien expliqué](https://hooktube.com/watch?v=LkTuYaWZsrs)
- Vous pouvez aussi filtrer par type de protocole quelques idées:
	- dns
	- arp
	- tcp
	- http (sans chiffrement, visitez par exemple 1libertaire.free.fr qui n'a pas de HTTPS pour)