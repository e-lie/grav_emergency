title: Introduction au DevOps
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
## *Le meilleurs de deux mondes ?*

---

class: impact

# `ping:`

---

# Puppet ? c'était bien ? 

---

# Logistique ?

- Les cours sont toujours mis à dispositions sur : **https://ptych.net/documents**
- Vous allez continuer à sauvegarder les exercices sur un dépot git en ligne...

---

## Pour coder durant cette formation

- **VSCode** : un éditeur puissant que vous connaissez un peu
- **Git et Framagit** : créer un dépôt, des commits et pousser ça sur internet !

---

class: impact

# Ansible

---

# Ansible

## Un logiciel pour configurer des machines
--
  
- Installer des logiciels (apt install)
--

- Modifier des fichier de configuration (vim /etc)
--

- Contrôler les services qui tournent (systemctl...)
--

- Gérer les utilisateurs et les permissions sur les fichiers
--

- Etc.

---

# Ansible en image

.col-9[![](img/ansible_overview.jpg)]

---

 # Instrastructure As Code

## Du code qui contrôle l'état d'un serveur

Un peu comme un script bash mais:
--

- **Descriptif** : on peut lire facilement l'**état actuel** de l'infra
--

- **Idempotent** : on peut rejouer le playbook **plusieurs fois** pour s'assurer de l'état
--

- Du coup: playbook == état actuel de l'infra
--

- On contrôle ce qui se passe
--

- Assez différent de l'administration système "adhoc" (= improvisation)

---

# Infrastructure As Code

## Avantages

- On peut multiplier les machines (une machine ou 100 machines identiques c'est pareil).
--

- Git ! gérer les version de l'infrastructure et collaborer facilement comme avec du code.
--

- Tests fonctionnels (pour éviter les régressions/bugs)
--

- Pas de surprise = possibilité d'agrandir les clusters sans soucis !
---

# Prérequis pour utiliser Ansible (minimal)

 1. Pouvoir se connecter en **ssh** sur la machine : **obligatoire** pour démarrer !!!
 1. **Python** disponible sur la machine à configurer : **facultatif** car on peut l'installer avec ansible

---

# Ansible en image

.col-9[![](img/ansible_overview.jpg)]

---

# L'inventaire des machines

- Une liste de toutes les machines
- Classées par catégories
- Avec des variables spécifiques pour leur configuration


```
[nodes]
node1 ansible_host=10.0.2.4 node_number=1
node2 ansible_host=10.0.2.5 node_number=2
node3 ansible_host=10.0.2.7 node_number=3
node4 ansible_host=10.0.2.8 node_number=4

[nodes:vars]
ansible_user=elk-admin
ansible_password=el4stic
ansible_become_password=el4stic% 
```

---

# A quoi ça ressemble un playbook

.col-8[.col-10[
```yaml
--- 
- hosts: serveur_web
  vars:
    - logfile_path: "/var/log/monlog"
  tasks:
    - name: installer le serveur nginx
      apt:
        name: nginx
        state: present
    - name: créer un fichier de log
      file:
        path: {{ logfile_path }}
        mode: 755
  handlers:
    - systemd:
        name: nginx
        state: "reloaded"
```
]]

## Quatre parties (ou plus)
--

- `hosts:` sur quelle machine on applique la configuration
--

- `vars:` des valeurs pour la configuration
--

- `tasks:` les taches de configuration
--

- `handlers:` des postraitements
--

- Des **modules python** !
  - *file*, *apt*, *systemd*

---

class: impact

# TP1 !

---