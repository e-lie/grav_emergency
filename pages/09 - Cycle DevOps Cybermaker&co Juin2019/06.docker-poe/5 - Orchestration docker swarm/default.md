---
title: 'Docker 5 - Orchestration avec Docker Swarm'
visible: true
---

## Orchestration avec Docker Swarm

- Cloner l'application exemple ici : [https://gitlab.com/e-lie/getstarted_docker.git](https://gitlab.com/e-lie/getstarted_docker.git)

- Suivez le [guide Docker de découverte de Swarm à partir de la partie 3](https://docs.docker.com/get-started/part3/) 

- Une fois le tutoriel terminé ajoutez un noeud worker supplémentaire.
- Scalez la stack en modifiant les replicats.
- Trouvez comment promouvoir(promote) un worker en manager
- Puis déchoir(demote) le manager pour le sortir du cluster (drain) : `docker node update --availability drain <node-name>`

- Comment faire en production pour gérer la configuration de base des machines finement ? Déployer et provisionner les machines avec terraform et ansible par exemple puis utiliser le driver generic pour les ajouter à docker-machine.
  
- Comment ne pas exposer les ports de tous nos hôtes à tous l'internet ?

## Installons portainer

Portainer est une interface web de base pour gérer un cluster docker.

```bash
docker service create \
      --name portainer \
      --publish 9000:9000 \
      --constraint 'node.role == manager' \
      --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
      portainer/portainer \
      -H unix:///var/run/docker.sock
```

- Listez les services
- Inspectez le service portainer avec l'option --pretty
- Ouvrez la page avec `firefox http://$(docker-machine ip <machine_manager>):9000`