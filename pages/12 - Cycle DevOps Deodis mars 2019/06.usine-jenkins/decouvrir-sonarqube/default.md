---
title: 'Découvrir SonarQube'
visible: true
---

SonarQube est un outil de **continuous code quality**.
Il permet de scanner un projet logiciel et de détecter:
	- les bugs
	- certaines failles de sécurité par une analyse statique
	- les problèmes de formatages et oublis (import inutile, commentaire oublié, fonction mal formatée)
	- les "mauvaises odeurs" de code (code smells) c'est à dire des mauvaises pratiques qui sont le signe de problème plus profond par exemple dans l'architecture du logiciel
		- code dupliqué
	- la couverture de test de chaque partie du programme et donc le manque de test dans le cas d'un développement dirigé par les tests.

# Installer SonarQube

Sonarqube est disponible sous forme d'une image docker autonome.
- Pour l'installer lancez la commande `docker run -d --name <nom_conteneur> -p <port_mapping> <id_image>`
	- pour manipuler facilement le conteneur on prendra `sonarqube` comme nom
	- le port standard de sonarqube est  9000 donc notre mapping sera simplement `9000:9000`
	- cherchez l'id de l'image officielle sur [hub.docker.com](hub.docker.com)

- Visitez [http://localhost:9000](http://localhost:9000) Le login par défaut est `admin:admin`

Sonarqube est le serveur qui recueille, organise et affiche les informations de qualité de code.
Pour utiliser le serveur il est nécessaire de scanner un projet logiciel.
- soit par l'intermédiaire d'un système d'intégration continue comme `Jenkins`
- soit avec un système de build comme `gradle`
- soit manuellement avec le `sonar-scanner`

Nous choisirons la 3e option pour découvrir sonar.

# Installer le scanner (sur ubuntu 18.04)

Nous allons installer manuellement le scanner dans le dossier `/opt/sonarscanner`

- Créez le dossier avec `mkdir -p <dossier>`
- Déplacez vous dedans avec `cd`
- Télécharger le sonar scanner avec `sudo wget` à cette adresse https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-3.2.0.1227-linux.zip
- décompressez l'archive avec `sudo unzip <nom archive>`
- vérifiez la décompression avec `ls` et supprimez l'archive : `sudo rm sonar-scanner-cli-3.2.0.1227-linux.zip`
- editez la configuration de base du scanner avec `sudo gedit sonar-scanner-3.2.0.1227-linux/conf/sonar-scanner.properties`
- ajoutez la ligne: `sonar.host.url=<url de sonar>` avec l'url de sonar indiqué dans la partie précédente.
- rendre le fichier executable: `sudo chmod +x sonar-scanner-3.2.0.1227-linux/bin/sonar-scanner`
- ajouter le programme aux programme du systeme avec un lien: `sudo ln -s /opt/sonarscanner/sonar-scanner-3.2.0.1227-linux/bin/sonar-scanner /usr/local/bin/sonar-scanner`
- vérifiez que le programme est disponible en tapant `sonar-sca` puis `TAB TAB`

# Analyser un programme

- récupérez un programme java existant : `git clone <url>` par exemple [https://github.com/spring-projects/spring-petclinic](https://github.com/spring-projects/spring-petclinic)

- Lancez le serveur du programme avec `./mvnw spring-boot:run`
- Vous pouvez visiter l'application : [http://localhost:8080](http://localhost:8080)
- stoppez le serveur (CRTL+C)

- Avant de lancez le scan nous devons configurer un peu le scanner pour le projet:
	- ouvrez le projet dans VSCode
	- ajoutez `.scannerwork` au `.gitignore`
	- identifiez le dossier des sources du projet et le dossier des binaires du projet.
	- à la racine du projet créez un nouveau fichier `sonar-project.properties` avec à l'intérieur (remplacez les valeurs <>):

```bash
# must be unique in a given SonarQube instance
sonar.projectKey=petclinic
# this is the name and version displayed in the SonarQube UI. Was mandatory prior to SonarQube 6.1.
sonar.projectName=<nom projet>
sonar.projectVersion=1.0
 
# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
# This property is optional if sonar.modules is set. 
sonar.sources=<dossier des sources>
sonar.java.source=1.8
sonar.java.binaries=<dossier des binaires>
 
# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8
```

- Lancez le scanner avec la commande: `sonar-scanner`

- Visitez le serveur sonarqube pour explorer le rapport de qualité de code.
	- Identifiez un code smell
	- Trouvez le code coverage
	- Explorez les remarques sur le code Java et essayer de comprendre de quoi il s'agit.

