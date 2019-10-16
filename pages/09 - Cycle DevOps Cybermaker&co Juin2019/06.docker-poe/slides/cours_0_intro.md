title: Conteneurs Docker
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
## *Modularisez et maîtrisez vos applications*

---



class: impact

# Introduction

---

# Des conteneurs


.col-6[.center[![](img/docker/docker.png)]]
.col-5[

- La métaphore docker : "box it, ship it"
--

- Une abstraction qui ouvre de nouvelles possibilités pour la manipulation logicielle.
- Permet de standardiser et de contrôler la livraison et le déploiement.

]

---

# Retour sur les technologies de virtualisation


On compare souvent les conteneur aux machines virtuelles

.col-8[.center[![](img/devops/windows-server-virtual-machines-vs-containers.png)]]
.col-4[
- **VM** : une abstraction complète pour simuler des machines

- => un processeur, mémoire, appels systèmes, réseau, virtuels (etc.)

- **conteneur** : un découpage dans linux pour séparer des ressources.
]

---

# Origine : LXC (LinuX Containers)

- Originellement Docker était basé sur le projet **lxc**.
--

- Les conteneurs sont un vieux concept qui se rapproche de `chroot`, présent dans les systèmes unix depuis longtemps : "comme tout est fichier, changer la racine c'est comme changer de système".
--

- `jail` introduit par FreeBSD pour compléter chroot en isolant les processus (pour des raisons de sécurité)
--

- En 2005 Google commence le développement des **cgroups** : une façon de tagger les demandes de processeur et les appels systèmes pour pour les grouper et les isoler.

- En 2008 démarre le projet LXC qui chercher à rassembler, les **cgroups**, le **chroot** et les **namespaces**
--

- **namespaces**: une façon de compartimenter la mémoire et le systeme de fichier

---

# Origine : LXC (LinuX Containers)

- En 2013: docker Commence à proposer une meilleure finition et une interface simple qui facilite l'utilisation des conteneurs **lxc**. Puis propose aussi son cloud **Docker hub** pour faciliter la gestion e
--

- Au fur et à mesure Docker abandonne le code de **lxc** (mais continue d'utiliser les **cgroups** et **namespaces**)
--

- Le code de base par Docker (**runC**) est néanmoins open source : il s'agit autour de l'**Open Container Initiative** de mutualiser le travail spécifique pour qui soit solide et spécifique.

---

# Bénéfices par rapport aux machines virtuelles

Docker permet de faire des "quasi machines" avec des performances proches du natif. 

- Vitesse d'exécution.
- Flexibilité sur les ressources (mémoire partagée).
- Moins **complexe** que la virtualisation
- Plus **standard** que les multiples hyperviseurs
- notamment => Moins de bugs d'interaction entre l'hyperviseur et le noyau
- Pas besoin de spécialiste

---

# Bénéfices par rapport aux machines virtuelles
VM et conteneurs proposent une flexibilité de manipulation des ressources de calculs mais les machines virtuelles était trop lourdes pour être multipliées librement :
- elle n'ont pas pu être utilisées pour isoler **chaque application**
- elles ne permettent pas la transformation profonde que permettent les conteneurs :
  - le passage à une architecture **microservices**
  - et donc la **scalabilité horizontale** à l'heure du cloud

---

# Pourquoi utiliser Docker ?

Docker est pensé dès le départ pour faire des conteneurs applicatifs:

- **Isoler** les modules applicatifs.
--

- Gérer les **dépendances** en les embarquants dans le conteneur.
--

- **Immutabilité**.
--

- **Cycle de vie court** -> pas de persistance (DevOps).

---

# Pourquoi utiliser Docker ?

Docker transforme littéralement la **"logistique"** applicative.

- **Uniformisation** face aux divers langages de programmation
--

- **Installation sans accrocs** donc **automatisation** beaucoup plus facile.
--

- Permet de démocratiser l'**intégration continue** et la **livraison continue** voire le **déploiement continu**.
--

- **Rapprocher les développeurs** des **opérations** (tout le monde a la même technologie).
  
- Adoption plus large de la philosophie DevOps.

---

# Positionnement sur le marché

- Docker est la technologie ultra-dominante sur le marché de la conteneurisation
  - La simplicité d'usage et le travail de standardisation (OCI) lui on donné légitimité et fiabilité
  - La métaphore du conteneur de transport et la bonne documentation on 
--

- **LXC** existe toujours et avec **LXD** il est devenu très agréable à utiliser.
- Il a cependant un positionnement différent = faire des conteneurs pour faire tourner un OS linux complet. 
--

- **Apache Mesos**: Un logiciel de gestion de cluster qui permet de se passer de docker mais finalement propose un support pour docker depuis 2016.

---

# Terminologie et concepts fondamentaux

Ne pas confondre :

- Une **image** : un modèle pour créer un conteneur.
- Un **conteneur** : l'instance qui tourne sur la machine.
- Un **volume** : un espace virtuel pour gérer le stockage d'un conteneur et le partage entre conteneurs.

Autres concepts centraux:

- un **registry**: un serveur ou stocker des artefacts docker c'est à dire des images versionnées.
- un **orchestrateur**: un outil qui gère automatiquement le cycle de vie des conteneurs (création/suppression).

---

# Visualiser l'architecture Docker

## Daemon - Client - images - registry

![](img/docker/archi1.png)


---

# L'écosystème Docker

- **Docker Compose** : Un outil pour décrire des applications multiconteneurs.
--

- **Docker Machine** : Un outil pour gérer des hôtes de déploiement docker
--

- **Docker Hub** : Le service d'hébergement universel d'images proposé par Docker Inc. (le registry officiel)
--

- (Docker **cloud** et Docker **store** ont fusionné avec **Docker Hub**)

---

