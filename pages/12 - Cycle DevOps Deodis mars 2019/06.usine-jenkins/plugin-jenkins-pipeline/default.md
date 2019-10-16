---
title: 'Pipeline de test avec Jenkins'
visible: true
---

## I - Comprendre la syntaxe Jenkins: un helloworld plus complexe

Créez un pipeline à partir du code suivant en complétant les trous.

```bash
pipeline {
    agent any
    triggers { <déclencheurs>  }
    options { <options> }
    parameters {
        booleanParam(name: 'DEBUG_BUILD', defaultValue: true,
        description: 'Is it the debug build?')
    }
    stages {
        stage('<nom_de_votre_stage>') {
            environment { NAME = '<your_name_here' }
            when { expression { return params.DEBUG_BUILD } }
            steps {
                echo "Hello from $NAME"
                script {
					<un_script_groovy_custom>
                }
            }
        }
    }
    post { always { echo 'I will always say Hello again!' } }
}
```
- la directive trigger sert à préciser ce qui déclenche le build : Comme déclencheur pour le pipeline utilisez : `cron('* * * * *')`. Le cron est un programme unix basique qui permet de lancer une tâche périodiquement : ici les 5 étoiles indiquent de lancer la tâche chaque minute.
- Ajoutez l'option `timeout(time: 5)` qui indique que le pipeline doit s'interrompre après au plus 5 minutes d'exécution.
- Compléter ensuite le de votre stage parce qui vous convient, par exemple `hello world`
- La tache script dans le pipeline permet d'exécuter un script personnalisé en Groovy :
```groovy
def browsers = ['chrome', 'firefox']
for (int i = 0; i < browsers.size(); ++i) {
      echo "Testing the ${browsers[i]} browser."
}
```
Ce script créé un tableau avec des noms de navigateurs et affiche un message hello pour chacun d'eux (boucle for.)

- Au final cet exemple contient la plupart des instructions disponibles dans le langage des Jenkinsfile ; un déclencheur, des options de pipeline, des paramètres d'environnement et bien sur le principal la description d'étapes rangées dans des stages.
- Sauvegarder le pipeline et lancez le.


## Hackons le helloworld

- Changeons la fréquence de trigger:
	- Lisez cet article [https://www.linuxtricks.fr/wiki/cron-et-crontab-le-planificateur-de-taches](https://www.linuxtricks.fr/wiki/cron-et-crontab-le-planificateur-de-taches)
	- Trouvez comment modifier la fréquence du trigger pour que le pipeline soit déclenché toutes les 3 minutes. Indice: Il faut modifier la partie avec les 5 étoiles : `* * * * *`
- Ajoutez un deuxième `stage` avec un nouveau nom en dupliquant le bloc adéquat.
- Modifiez le code groovy pour afficher un table de multiplication : le groovy ressemble beaucoup au Java :), essayez de déduire en regardant l'exemple.

## Préparer l'application à tester

- Reprendre l'application calculator, avec votre fonction substract.

- Assurez vous d'avoir deux tests unitaires pour les fonctions sum et substract. Lancez les.

- Observez le `Jenkinsfile` de votre branche substract

- Créez un nouveau projet gitlab public pour accueillir l'application.
	- Créez un projet vide.
	- Ajoutez un remote git `git remote add depot <url_de_votre_projet>`.
	- Poussez votre code sur le dépot `git push depot --all`


## Créer un pipeline

- Rendez vous dans jenkins > administrer Jenkins > Plugins

- Installez la suite de plugins `Blue Ocean`
- Retournez sur la page d'accueil et cliquez sur `Open Blue Ocean `. Vous arrivez sur une nouvelle interface de Jenkins moins austère.
- Cliquez sur `nouveau pipeline`
- Sélectionnez l'option `git` simple pour la source du code.
- Rendez vous sur la page de votre projet gitlab.
- Cliquez sur le petit bouton `clone` bleu et opiez l'adresse SSH
- De retour dans votre pipeline blue ocean coller l'adresse
- Jenkins vous suggère automatiquement une clé publique pour s'identifier en ssh sur gitlab. Copier le texte dans le champ en cliquant sur `copy to clipboard`.
- Retournez dans gitlab section settings > SSH Key et ajoutez cette clé jenkins.
- Une fois la clé ajoutée vous pouvez créer le pipeline dans jenkins

## Observer le pipeline

- La création du pipeline lance automatiquement un build sur chacune des branches du dépot : normalement vous devez avoir une branche master et une autre branche avec votre code substract (par exemple nommée substract)
- Cliquez sur le build de la branche substract. Vous pouvez suivre en temps réel l'execution du pipeline.
- Le pileline sur substract comporte deux stages, `Build` et Unit `Test` qui normalement devraient bien se dérouler et vous renvoyer un résultat positif.
- Tout cela est un peu magique. Comment Jenkins-a-t-il fait pour savoir comment tester notre code ?
- Il s'agit de `pipeline as code` Jenkins n'a plus besoin d'être configuré manuellement car il autoconfigure ses pipelines à partir du Jenkinsfile à la racine d'un projet git.
- Visitez le build de la branche master. Il echoue à l'étape 3 car l'outil Jacoco trouve un code coverage de 0. Comparez l'aspect visuel du pipeline avec le contenu du Jenkinsfile de la branche master : chaque stage correspond à un point sur la représentation du pipeline dans Blue Ocean.

## Modifier le Jenkinsfile

- Dans le projet calculator, créez une nouvelle branche `build_ci` à partir de substract.
- Lancez `./gradlew build` -> cette commande créé un fichier .jar dans `/build/libs/`
- Lancez l'application construite par build avec `java -jar build/libs/<nom_du_.jar>`
- Éditez le Jenkinsfile pour ajouter un `stage` `Build`, qui déclenche `./gradlew build` (Copier le stage unit test en le modifiant légèrement)
- Commitez et poussez les modifications.
- Retournez sur l'interface de Jenkins Blue Ocean. Lancez un build sur la nouvelle branche. Il devrait passer sans soucis.

## Visiter un runner, lancer l'application construite

- Dans les logs de votre pipeline, cherchez sur quel runner il a été exécuté.
- Connectez vous en SSH au runner
- Visitez `/var/jenkins/workspace`
- Chez ou se trouve le .jar de votre dernier pipeline
- exécutez le avec `java -jar <fichier.jar>`
- visitez l'adresse dans le navigateur [http://<ip_du_runner>:8080/sum?a=5&b=7](http://<ip_du_runner>:8080/sum?a=5&b=7). Vous devriez pourvoir accéder à l'application
- Stoppez ensuite l'application sur le runner (CTRL+C)

## Packagez l'application avec docker

- Copiez le Dockerfile de la branche `master` dans la branche `build_ci`
- Copiez le stage `Docker Build` du Jenkinsfile de master pour l'ajouter au Jenkinsfile de `build_ci`. Remplacez `leszko/calculator` par `<votre_nom>/calculator` et supprimez `${BUILD_TIMESTAMP}`
- Commitez et poussez votre code.
- Lancez un build sur Jenkins pour la branche `build_ci`
- Visitez votre runner en SSH. Lancez `docker image ls` Une image `<votre_nom>/calculator`
- Lancez l'image avec `docker run -p 8080:8080 <nom_image>`
- Visitez l'adresse dans le navigateur [http://<ip_du_runner>:8080/sum?a=5&b=7](http://<ip_du_runner>:8080/sum?a=5&b=7). Vous devriez pourvoir accéder à l'application.

## Poussez l'applicationsur docker hub

- Créer un compte docker hub.
- Suivre les instructions [ici](https://www.linuxnix.com/how-to-push-docker-images-to-docker-hub-repository/) pour pousser l'image sur votre compte docker hub.
- Une fois que vous pouvez pousser votre image avec `docker push`, ajoutez une étape à votre Jenkinsfile:
```
stage("Docker push") {
	steps {
		sh "docker push <votre_nom>/calculator"
	}
}
```
- Ajoutez une route multiply à votre programme (et un test correspondant).
- Poussez ces modifications. Exécutez le pipeline sur Jenkins.
- L'image devrait être disponible sur docker hub avec la nouvelle fonction multiply. Pour le vérifier lancez sur votre ubuntu : `docker run -p 8080:8080 <votre_nom>/calculator
- Visitez l'adresse dans le navigateur [http://localhost:8080/multiply?a=5&b=7](http://localhost:8080/multiply?a=5&b=7). Vous devriez pourvoir accéder à la fonction de multiplication \o/.

## Avancé : Créer un déploiement staging avec Jenkins

- Ajoutez au dépot un inventaire et un playbook qui configure une machine avec docker et lance le container calculator depuis docker hub.
- Ajoutez un stage au Jenkinsfile qui lance la commande `ansible-playbook` pour configurer la machine et lancer l'application sur le serveur.
- Ajoutez un test d'acceptance minimal (fonctionnel) qui vérifie si l'application est accessible depuis le serveur staging et le résultat renvoyé  (vous pouvez consulter le livre à la page 152 et suivantes)

## Bonus plus difficile : décencher un scan sonarqube depuis Jenkins.

- Monter un conteneur sonarqube sur le noeud Jenkins Master (ou créer un nouveau noeud dédié à provisionné avec ansible, cf TP Installer Jenkins). Notez son adresse IP.

- Pour déclencher le scan depuis Jenkins la documentation officielle de sonarqube . Plusieurs options sont possible, essayer `Analyzing in a Jenkins pipeline` en priorité.
https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Jenkins#AnalyzingwithSonarQubeScannerforJenkins-AnalyzinginaJenkinspipeline