title: Puppet
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
-->

---

class: impact

# {{title}}
## *Un gestionnaire de configuration historique*

---

class: impact

# Puppet Language

---

# Un DSL déclaratif

- Une syntaxe qui décrit toutes les ressources concernées pour la lisibilité. On lit le code on connaît le système.
- 
- Un langage de haut niveau: évite les spécificités de l'OS et des commandes unix.

- Pas de `if` ou de `for` mais des dépendances: "faire ça une fois que cette resource a été configurée comme ça".

---

# Basé sur des resources

- Chaque élément du système à configurer est une resource (fichier, service, user, groupe, paquet)
- Chaque ressource puppet a une étiquette (nom) unique et un état que puppet change pour correspondre à la description
  - utilisateur etat: `user{ 'elie', ensure => present }`

---

# Syntaxe des ressources

```puppet
type_ressource { 'nom_ressource':
  param1 => value,
  param2 => fonction($variable),
  ...
}
```

---

### Exemple

```
file { '/tmp/latest.tar.gz':
    ensure => present,
    source => "puppet:///modules/wordpress/latest.tar.gz",
    owner => elie
}
```

```
file { '/var/www/wp-config.php':
    ensure => present,
    content => template("wordpress/wp-config.php.erb"),
    owner => www-data
}
```

---

## Dépendances entre resources avec `require`

```
    file { '/tmp/latest.tar.gz':
        ensure => present,
        source => "puppet:///modules/wordpress/latest.tar.gz"
    }

    exec { 'extract':
        cwd => "/tmp",
        command => "tar -xvzf <archive_wordpress>",
        require => File['<archive_wordpress>'],
        path => ['/bin'],
    }

    exec { 'copy':
        command => "cp -r /tmp/wordpress/* <chemin_installation>",
        require => Exec['extract'],
        path => ['/bin'],
    }
```

---

# Des variables...

- `$variable = value`

- réutilisable un peu partout dans les manifeste (il faut préciser le contexte souvent)
- Un système assez proche de la POO et des packages : `$wordpress:conf:mavariable`

# des templates

- Un template est comme avec Ansible un modèle de fichier à construire dynamiquement
  - avec des trous pour les variables
  - avec des `if` et des `for`
  - généralement pour les fichiers de configuration.

---

# Deux formats de template

Puppet désormais deux formats de template: ERB (ruby) et EPP (puppet)

- Dans un template `erb` une variable s'écrit `<%= $nom_variable %>`
- Dans un te

- la fonction pour erb est `template(fichier.erb)`
- la fonction pour epp est `epp(fichier.erb)`

template(File())


---

# Un écosystème de modules

 - Les modules puppet sont très proches des libraires en programmation:
   - des outils dédiés à des tâches assez courantes
   - des classes du langage qu'on peut instancier comme ressources
   - les modules peuvent être combinés pour créer des modules de plus haut niveau.
     - exemple: module pour installer wordpress avec les modules apache et mysql
     - ex2: un module pour installer MySQL

---

# R10k et la forge

- Un outil ruby à installer avec `gem`.
- Installe les modules décrit dans un `Puppetfile`
- ne gère pas les dépendance automatiquement
- Permet d'installer des modules personnalisé

- La forge contient les modules officiels et leur documentation mais peu également accueillir les votre s'il respecte la norme de développement.

---

# Les environnements

- La configuration puppet est regroupée en environnements: `production`, `staging`, `dev`, `mon_env`, etc.

- Un environnement décrit normalement un contexte d'infrastructure.

- Une machine agent est configurée pour suivre une ou plusieurs configurations. ex: on créé une machine et on l'ajoute à l'environnement staging: elle se configure en respectant la config correspondante.

- L'environnement par défaut est `production`.
  - `/etc/puppetlabs/code/production/` pour le code
  - `/etc/puppetlabs/puppet/production/` pour la configuration de puppet
  - etc.