title: Ansible 
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
## *Infra as Code, devops for real !*

---

# Ansible VS Bash

- Des **modules** Python plutôt que des commandes unix
    - une autre méthode de contrôle
    - plus adaptée à la configuration dynamique

.col-6[
 ```yaml
  - name: installer les paquets nécessaires
    apt:
      name:
        - apache
        - php
        - mysql
      state: present
      update_cache: yes
    become: yes
 ```
]

.col-6[
  ```bash
  $ sudo apt update
  $ sudo apt install -y apache php mysql
  ```
]

---

# A quoi ça ressemble : Yaml

.col-8[.col-10[
```yaml
- marché:
    lieu: Crimée Curial
    jour: dimanche
    horaire:
      unité: "heure"
      min: 9


      max: 14
    fruits:
      - nom: pomme
        couleur: "verte"
        pesticide: avec

      - nom: poires
        couleur: jaune
        pesticide: sans
    légumes:
      - courgettes
      - salade

      - potiron
```
]]

## Syntaxe
--

- Alignement ! (**2 espaces** !!)
--

- ALIGNEMENT !! (comme en python)
--

- **ALIGNEMENT !!!** (vous allez quand même vous planter :p)
--

- des listes (tirets)
--

- des paires **clé: valeur**
--

- Un peu comme du JSON


---

# Exemple de playbook

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
