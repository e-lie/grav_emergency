---
title: 'Installer le JDK Java 8 sur Ubuntu'
visible: true
---

Pour notre environnement sur Ubuntu nous allons utiliser la version 8 (kinda LTS) de Java car **groovy** et **gradle** semblent partiellement **incompatibles** avec la version 11.

- Lancez  `sudo apt install openjdk-8-jdk`
- puis `sudo  update-alternatives --list java`.

Si la deuxième commande affiche plus qu'un ligne exécutez
- `sudo  update-alternatives --config java`
- puis `sudo  update-alternatives --config javac`

Sélectionnez java 1.8 à chaque fois.