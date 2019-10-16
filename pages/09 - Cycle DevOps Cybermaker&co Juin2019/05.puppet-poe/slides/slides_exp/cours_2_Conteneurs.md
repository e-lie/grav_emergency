title: Automatisation DevOps
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
idée : **ansible est un gestionnaire de configuration**
mutable VS immutable

elastic = scaling out = grandir en largeur => orchestration

test de la boite noire => pouvoir avoir facilement des boîtes pour tester

tests de boîtes noires en parallèle pour gagner du temps (5 boites)

flexibilité

Structure d'un pileline gitlab: image avec l'organisation du kubernetes (un cont runner, un cont revserse, un cont monitoring et des cont pour les applications de test) 



-->

---

class: impact

# {{title}}
## *Conteneurs*

---

# Types de technologies / mouvements

- Cloud
--

- InfraAsCode
--

- **Conteneurs**
--

- **CI / CD**
---

# Conteneurs

Les conteneurs sont des boîtes pour ranger des logiciels.

## Un peu comme des machines virtuelles

- basées sur linux
--

- facilement multipliables
--



- basée sur une technologie de séparation noyau
--

## Mais

- Immutables



---

# Comparaison avec ansible

- Ansible amène de la fiabilité en proposant une approche :

--

- **Descriptive** : on peut lire facilement l'**état actuel** de l'infra
--

- **Idempotente** : on peut rejouer le playbook **plusieurs fois** pour s'assurer de l'état
--

- Du coup: playbook == état actuel de l'infra
--

- On contrôle ce qui se passe
--

- Assez différent de l'administration système "adhoc" (= improvisation). Dès que l'infrastructure grossit un outil comme Ansible devient indispensable pour organiser de façon rationnelle les opérations.

---

# L'approche d'infrastructure avec docker 

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

.col-9[]

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

# TP !

---