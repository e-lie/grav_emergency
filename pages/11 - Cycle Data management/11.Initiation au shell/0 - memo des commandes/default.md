---
title: '0 - Mémento des commandes shell'
visible: 'yes'
---

On suppose qu'il existe l'arborescence suivante dans votre répertoire
personnel (`home`) :

```
                                   /home/padawan/
                                  ___________|____________________________
                                  |                    |        |        |
                                 dev/               music/     pics/    docs/
                           _______|             _______|________
                           |      |             |      |       |
                     teaching/  yunohost/     trap/  poprock/  electro/
                           |      |
                           |      |_______________________________________________
                           |      |      |        |         |          |         |
                    linux.html   en.json  app.py  domain.py   bin/     helpers/  hooks/
```


Vous vous trouvez dans le répertoire `music/electro/` (on nomme ce répertoire le *répertoire courant*). Depuis ce répertoire, tous les chemins suivants sont équivalents :

- (absolu) `/home/padawan/dev/yunohost/app.py`
- (absolu) `$HOME/dev/yunohost/app.py`
- (absolu) `~/dev/yunohost/app.py`
- (relatif) `../../dev/yunohost/app.py`
- (relatif) `../../pics/././../dev/teaching/../yunohost/app.py`
- et aussi: `/var/cache/../../home/./padawan/dev/yunohost/app.py`

### Commandes indispensables

|commande                        |description                                                 |
|--------------------------------|------------------------------------------------------------|
|  `man <commande>`              |  (**man**ual) Obtenir des informations sur une commande    |
|  `passwd`                      |    (**passw**or**d**) Changer son mot de passe |
|  `cd <destination>`            |(**c**hange **d**irectory) Changer de répertoire (si pas de destination, va dans `$HOME`) |
|  `ls <repertoire>`             |(**l**i**s**t) Lister le contenu du répertoire              | 
|  avec option `-l`              |Ajoute des détails (permissions, date de modification, taille) | 
|  avec option `-a`              | Liste aussi les fichiers cachés (commençant par .)  | 
|  avec option `-t`              | Liste en triant suivant la date de modification  | 
|  avec option `-h`              |     Affiche les tailles de fichiers en un format lisible facilement | 
|  `mkdir <repertoire>`          |   (**m**a**k**e **dir**ectory) Créer un répertoire |
|  `mv <source(s)> <destination>`|(**m**o**v**e) Renommer un fichier (ou déplacer plusieurs fichiers dans un répertoire) |
|  `cp <fichier> <destination>`  |  (**c**o**p**y) Copier un fichier |
|  avec option `-r`              |  Copier récursivement un dossier |
|  `rm <fichier(s)>`             |      (**r**e**m**ove) Supprimer **définitivement** des fichiers
| avec l'option   `-r`           |               Option pour suppression récursive (et **définitive**) de répertoires |
|   avec l'option    `-i`        |            Demande confirmation avant les suppressions |

### Commandes de base

|commande                           |description                                                 |
|-----------------------------------|------------------------------------------------------------|
| `echo <chaîne>`               |Afficher une chaîne de caractère dans la sortie |
| `touch <fichier>`              |Créer un fichier (ou changer sa date de modification sans rien changer) |
| `pwd`                             |(**p**rint **w**orking **d**irectory) Obtenir le nom du répertoire courant |
| `whoami`                          |(**who am I**) Obtenir votre nom d'utilisateur |
| `date`                            |(**date**) Afficher la date |
| `chmod <permissions> <fichier>`   |(**ch**ange **mod**e) Changer les permissions d'un fichier |
| `chown <utilisateur> <fichier>`   |(**ch**ange **own**er) Changer le propriétaire d'un fichier |
| `chgrp <groupe> <fichier>`        |(**ch**ange **gr**ou**p**e) Changer le groupe d'un fichier |
| `du <fichier>`                    |(**d**isk **u**sage) Connaître l'espace disque utilisé par un fichier |
| avec l'option `-h`                   |Donne les tailles dans un format plus facilement lisible |
| avec l'option `-s`                   |Donne uniquement le total pour les dossiers |
| avec l'option `-c`                   |Donne le total de la liste |
| `ln -s <destination> <lien>`      |(**l**i**n**k) Créer un lien dur |
| sans l'option  `-s`           |Créer un lien dur plutôt qu'un lien symbolique (rare) |

### Raccourcis de la ligne de commande

|commande                |description                                                 |
|------------------------|------------------------------------------------------------|
| Flèches du haut/bas    |  Parcourir les commandes de l'historique  |
| TAB                    |  Auto-compléter une commande ou un nom de fichier |
| Ctrl + R               |  Rechercher une commande dans l'historique |
| Ctrl + A/E             |  Aller au début/à la fin de la ligne de commande  |
| Ctrl + U/K             |  Supprimer tous les caractères à gauche/droite du curseur |
| Ctrl + W               |  Supprimer le mot à gauche du curseur |

### Caractères speciaux

| caractère        |  description   |
|------------------|----------------|
|  `;`   | Sépare des commandes à exécuter à la suite |
|  `||` | Sépare des commandes à exécuter à la suite (s'arrête si il y a eu des erreurs) |
|  `~` | Désigne le chemin vers votre répertoire personnel (`$HOME`) |
|  `.` | Désigne le répertoire courant |
|  `..` | Désigne le répertoire parent\|
|  `?` | Symbole "joker", interprété comme n'importe quel caractère |
|  `*` | Symbole "joker", interprété comme n'importe quelle chaîne de caractère |
|  `\` | Inhibe l'interprétation du caractère spécial suivant |
|  `'` et `"` | Délimite des chaînes de caractère |
|  `` ` `` | Délimite une commande à interpréter dans les arguments d'une autre commande |
|  `$VAR` | Désigne le contenu de la variable shell `VAR` |

| Exemples |        |
|----------|--------|
| `echo "Je suis dans"; pwd` |  Affiche du texte suivi du chemin absolu du répertoire courant |
| `compile prog.c || ./prog.exe` |  Compile `prog.c` et exécute `prog.exe`, sauf si la compilation a échoué |
| `touch ~/toto` |  Créé un fichier `toto` dans votre répertoire personnel |
| `touch ./toto` |  Créé un fichier `toto` dans le répertoire courant (équivalent à `touch toto`) |
| `cd ..` |  Va dans le répertoire parent au répertoire courant |
| `ls ../../rep2/file?` |  Liste les fichiers `fileb` et `filec` dans `rep2` |
| `ls prog.*` |  Liste les fichiers `prog.c` et `prog.exe` dans `rep1/sousrep1/` |
| `echo Une étoile : \*` |  Affiche le texte `Une étoile : *` |
| `echo "Une étoile : *` |  Affiche le texte `Une étoile : *` |
| ``echo "Je suis dans `pwd`"`` |  Affiche du texte suivi du chemin absolu du répertoire courant |
| `echo "J'habite dans $HOME"` |  Affiche du texte suivi du chemin vers votre espace personnel |


### Gestion des entrées-sorties
```
                           +--------------+
                  stdin    |              |   stdout
              ------------>+   Commande   +----------->
                  Entrée   |              |   Sortie
                 standard  +------+-------+  standard
                                  |
                                  |  stderr
                                  +---------->
                                     Erreur
                                    standard
```

| Syntaxe                               |         description         |
|---------------------------------------|-----------------------------|
|`<` | Utilise un fichier comme entrée standard |
|`<<<` | Utiliser une chaîne de caractère comme entrée standard |
|`>` | Redirige la sortie standard vers un fichier (écrase l'ancien fichier) |
|`>>` | Redirige la sortie standard vers un fichier (à la suite du fichier) |
|`2>` | Redirige l'erreur standard vers un fichier |
|`|>` | Redirige la sortie et l'erreur standard vers un fichier |
|`|` | Redirige la sortie standard d'une commande vers l'entrée standard d'une autre commande |



| Exemples                                   |                             |
|--------------------------------------------|-----------------------------|
| `./prog.exe < inputFile > outputFile`      |Exécute `prog.exe` avec `inputFile` en entrée et met la sortie dans `outputFile` après l'avoir écrasé            |
| `ls | ./prog.exe >> outputFile`       |Exécute `prog.exe` avec la sortie de `ls` en entrée et met la sortie à la suite de `outputFile`                 |

### Gestion des processus

| Syntaxe                               |         description         |
|---------------------------------------|-----------------------------|
| `<commande> &` | Arrêter l'exécution de la commande en cours |
| `<commande> |` | Exécute `commande` en tâche de fond |
| Ctrl + Z puis `bg` | Passe la commande en cours d'exécution en tâche de fond |
| `jobs` | Lister les processus de votre shell qui sont en tâche de fond |
| `ps -ef` | Lister **tous** les process en cours d'exécution sur la machine |
| `kill     <PID>` | Tuer un processus en cours (via son identifiant `PID`) |
| `pkill    <processName>` | Tuer un processus en cours (via son nom `processName`) |
| Avec l'option `-9` | Tuer brutalement le processus |
| `top` | Visualiser l'utilisation de la mémoire et du CPU par les processus |
| Avec l'option `-i ` | Seulement les processus actifs |
| Avec l'option ` -p<PID>` | Seulement le(s) processus correspondant(s) au(x) PID(s) donné(s) |
| Avec l'option ` -u<USER>` | Seulement les processus d'un utilisateur donné |
| Avec l'option `screen` | Ouvre une session screen qui permet de lancer des commandes dans un terminal et qui continuera d'exister même si l'on quitte le terminal initial. |

### Lecture et édition de fichiers

|  Commandes       |  Description                                      |
|------------------|---------------------------------------------------|
| `cat`     | Affiche le contenu des fichiers dans la sortie standard |
| `less`    | Lire et naviguer dans un fichier |
| `nano`    | Edite un fichier avec `nano` (éditeur minimaliste) |
| `vim`     | Edite un fichier avec `vim` (pour les ninjas) |
| `xemacs`  | Edite un fichier avec `xemacs` (pour les pirates) |
| `nedit`   | Edite un fichier avec `nedit` (en interface graphique) |
| `evince`  | Lire un fichier pdf (en interface graphique) |

### Fichiers de configuration, variables d'environnement

|  Commandes       |  Description                                      |
|------------------|---------------------------------------------------|
| `~/.bashrc`             | Fichier de configuration qui est exécuté à chaque connexion |
| `env`                   | Commande qui liste toutes les variables d'environnement définies et leurs valeurs |
| `VAR=3.14`              | Changer la valeur de `VAR` (pas d'espaces autour de `=` !) |
| `export VAR`            | Rend la valeur de `VAR` disponible pour tous les process fils de ce shell |
| `echo "VAR vaut $VAR"`  | Affiche la valeur de `VAR` |
| `$USER`                 | Nom d'utilisateur |
| `$HOSTNAME`             | Nom de la machine |
| `$HOME`                 | Répertoire personnel |
| `$PATH`                 | Liste des répertoires où les commandes sont recherchées |
| `$LD_LIBRARY_PATH`      | Liste des répertoires où les librairies sont recherchées |
| `$PS1`                  | Décrit la forme de l'invite de commande en bash |

### Filtres et commandes avancées

Note : les filtres peuvent généralement être utilisés aussi bien sur un
fichier que sur l'entrée standard (via `|` )

|  Commandes       |  Description                                      |
|------------------|---------------------------------------------------|
| `grep`           | Permet de filtrer ligne par ligne suivant un mot ou un motif |
| `sed`            | Permet de remplacer une expression par une autre |
| `diff`           | Afficher les différences entre deux fichiers |
| `cut`            | Manipuler les colonnes d'une entrée |
| `tr`             | Remplacer ou enlever des caractères |
| `find`           | Rechercher des fichiers suivant des critères |
| `wc`             | Permet de compter des mots, des lignes, .. |
| `bc`             | Effectuer des opérations arithmétiques basiques |
| `ssh`            | Se connecter de façon sécurisée à une autre machine |
| `scp`            | Copier des fichiers entre deux machines |
| `tar`            | Compresser ou décompresser des fichiers (format `tar`) |
| `gzip`, `gunzip` | Compresser ou décompresser des fichiers (format `gz`) |
| `alias`          | Créer des alias |

|  Exemples        |                                                   |
|------------------|---------------------------------------------------|
| `grep -nr "warning" ./`               | Cherche récursivement les occurences de `warning` dans les fichiers du répertoire courant |
| `./prog.exe | grep -nr "warning" ./`         | Cherche récursivement les occurences de `warning` dans la sortie du programme `prog.exe`   |
| `sed "s/search/replace/g" inputFile`    | Remplace toutes les occurences de `search` par `replace` |
| `diff file1 file2`               | Compare les fichiers `file1` et `file2` |
| `cut -d " " -f 2,3 inputFile`    | Affiche les colonnes 2 et 3 (`-f 2,3`) du fichier `inputFile`, par rapport aux espaces (`-d " "`). |
| `cat inputFile | tr " " ":"`     | Remplace les espaces par des `:` dans `inputFile` |
| `find -name "*.cpp" ../` |  Trouver tous les fichiers se finissant par `.cpp` dans le répertoire parent |
| `wc -l *`               | Compte le nombre de ligne pour chaque fichier du répertoire |
| `bc <<< "1+2"`           | Effectue l'opération 1+2 et affiche le résultat |
| `scp user@host:/home/user/file ./` | Copie le fichier `/home/user/file` depuis la machine `host` dans le répertoire courant |
| `ssh user@host`               | Se connecter à la machine `host` en tant que `user` |
| `lpr -Pr2d2 outputFile`       | Imprime le fichier `outputFile` avec l'imprimante nommée `r2d2` |
| `tar -xvf archive.tar`         | Décompresse le fichier `archive.tar` |
| `gzip inputFile`               | Compresse `inputFile` en un fichier `inputFile.gz` |
| `alias ll='ls -l'`             | Créer un alias `ll` qui correspond à la commande `ls -l` |
