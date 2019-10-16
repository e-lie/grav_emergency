---
title: 'Puppet TP2 - Langage puppet, installer wordpress et créer un module '
visible: true
---

### Installer les modules Apache et MySQL

Les modules puppet sont comme des librairies pour le développement, ce sont des regroupement de code, autonomes et utilisables pour des tâches communes. La gestion d'Apache et de MySQL sont des activités courantes, Puppet a ses propres modules pour cela.

- Connectez vous en ssh sur le master avec la commande: `Vagrant ssh puppet`

- Lister tous les modules Puppet installés sur votre système en utilisant la commande suivante :

```
sudo puppet module list
```

Pour le moment aucun module n'est installé. Pour gérer les modules d'un environnement nous allons utilisez l'outil dédié `r10k` qui permet de télécharger des modules depuis la forge puppet.

- Visitez la forge puppet : [https://forge.puppet.com/](https://forge.puppet.com/)
- cherchez le module `apache` officiel de puppetlabs.


- Installons `r10k`:

```
sudo apt install ruby
sudo gem install r10k
```

L'installation des modules utilise un fichier nommé `Puppetfile` qui, un peu comme `requirements.txt` en python, permet de lister l'ensemble des modules nécessaires (modules officiel et d'explicité) c'est à dire les dépendances de notre installation puppet.

- Installons les modules officiels Apache et MySQL dans l'environnement `production` avec `r10k`:

```
cd /etc/puppetlabs/code/environments/production
rm -rf modules
```

- Créez le `Puppetfile` avec nano avec à l'intérieur:

```
mod 'puppetlabs/apache'
mod 'puppetlabs/mysql'
```

- Lancez l'installation des modules du Puppetfile avec :

```
sudo r10k puppetfile install --verbose
```

- Verifiez l'installation en listant les modules à nouveau:

```
sudo puppet module list
```

- La commande précédente affiche des warnings car les dépendances des modules ne sont pas satisfaites. Ajoutez les dépendances manquantes au Puppetfile:

```
mod 'puppetlabs/stdlib'
mod 'puppetlabs/concat'
mod 'puppetlabs/translate'
```

- Relancez l'installation avec `r10k`

- Relancez `sudo puppet module list`. Les warnings ont disparu. Vous devriez voir les modules installés.

```
/etc/puppetlabs/code/environments/production/modules
├── puppetlabs-apache (v5.0.0)
├── puppetlabs-concat (v6.1.0)
├── puppetlabs-mysql (v10.1.0)
├── puppetlabs-stdlib (v6.0.0)
└── puppetlabs-translate (v2.0.0)
/etc/puppetlabs/code/modules (no modules installed)
/opt/puppetlabs/puppet/modules (no modules installed)
```

### Utilisons le module apache

- Modifiez le manifeste `site.pp` du TP1. Remplacez son contenu par le code suivant

```
node 'puppet-node1', 'puppet-node2' {

  package { 'nginx':
    ensure => absent,
  }

  file { '/var/www/html/index.nginx-debian.html':
    ensure => absent,
  }

}
```

- En ssh sur le puppet_node2, vérifiez dans `syslog` que le manifeste a été appliqué et que `/var/www/html/index.nginx-debian.html` est bien absent.

Pour faciliter le test de notre code nous allons desactiver le service `puppet agent` pour le moment.

- Sur le node2 lancez: `sudo systemctl stop puppet`


- Ajoutez dans `site.pp` avant la dernière accolade:

```
include apache

apache::vhost { 'cat-pictures.com':
  port => '8080',
  docroot => '/var/www/cat-pictures',
  docroot_owner => 'www-data',
  docroot_group => 'www-data',
}

file { '/var/www/cat-pictures/index.html':
  content => "<img src='http://bitfieldconsulting.com/files/happycat.jpg'>",
  owner => 'www-data',
  group => 'www-data',
}
```

- Sur le node2 lancez: `sudo puppet agent --test`. Vous devriez voir la configuration de site.pp s'appliquer sur le serveur.

- Vérifiez que le vhost apache a bien été créé avec `sudo apachectl -S`

- S'il n'y a pas d'erreur vous devriez voir une photo de chat apparaître en visitant l'ip de chacun des noeuds sur le port 8080.

### Utiliser un module personnalisé pour installer wordpress

Nous allons utilisez un module personnalisé pour installer wordpress avec apache et mysql (classique). La bonne façon de créer un tel module est de faire un dépôt git et de l'installer avec r10k.


- Récupérez mon template de module avec git à l'adresse : `https://framagit.org/e-lie/puppet_module_wordpress`

- Observons le contenu du template:

```
.
├── files
│   └── latest.tar.gz
├── manifests
│   ├── conf.pp
│   ├── db.pp
│   ├── init.pp
│   ├── web.pp
│   └── wp.pp
├── metadata.json
└── templates
    └── wp-config.php.erb
```

- Le fichier `metadata.json` contient la description du module (mainteneur, licence, version, dépendances, etc)
- Le dossier `files` contient les fichiers nécessaires à l'installation: ici ce sont les fichiers de wordpress que nous allons déployer.
- Le dossier `templates` contient les fichiers de configuration que nous allons utiliser pour wordpress (wp-config.php). Comme avec ansible ils contiennent des "trou" complétés automatiquement lors de l'installation.
- Enfin le dossier `manifests` contient tous les manifests puppet décrivant l'installation et la configuration de notre module (apache, mysql, wordpress).

- Ces noms sont conventionnels (vous n'avez pas la possibilité de les changer dans vos modules).

- Éditez d'abord `conf.pp` qui définit les variables de notre installation. Complétez les trous pour configurer les paramètres de connexion de la base de donnée.
- Ensuite observez le contenu de `db.pp` `${wordpress::conf::db_name}` désigne la variable définie dans le fichier `conf.pp`. En résumé ce fichier sert à créer une base de données à l'aide des informations précisées précédemment.
- Éditez enfin `wp.pp` qui décrit les étapes d'installation de wordpress. Observons ensemble ce fichier. Complétez `<archive_wordpress>` avec `latest.tar.gz`, `<chemin_installation>` avec `/var/www/` et `<nom_template>` avec `wp-config.php.erb` (erb = format de templating ruby).

- Le fichier `init.pp` est le fichier de base d'un module. Il initialise les différentes partie de l'installation.

- Ajoutez et commitez les modifications.
- Poussez ce dépot sur un remote `puppet_module_wordpress` sur framagit

### Installer le module avec r10k

- En ssh sur le master dans `/etc/puppetlabs/code/environments/production` modifier le `Puppetfile`. Ajoutez la ligne:

```
mod 'wordpress', :git => 'https://framagit.org/e-lie/puppet_module_wordpress'
```

- Installez maintenant ce module personnalisé avec : `sudo r10k puppetfile install --verbose`

- Nous allons maintenant utiliser notre manifeste principal `site.pp` pour installer wordpress. Remplacez son contenu par:

```
node 'puppet-node1', 'puppet-node2' {

  class { 'wordpress':
  }

  apache::vhost { 'puppet-wordpress':
    port => '8080',
    docroot => '/var/www',
    docroot_owner => 'www-data',
    docroot_group => 'www-data',
  }

}
```

- Allez sur le node2 en ssh et lancez `sudo puppet agent --test` pour appliquer cette nouvelle configuration.

- S'il n'y a pas d'erreur, vous devriez pouvoir accéder à wordpress à l'adresse `192.168.2.3:8080`.






