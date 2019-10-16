---
title: 'Cours Puppet'
visible: false
---

# Puppet - Décrire et Stabiliser une infrastructure

## Architecture master / agent

## Sécurité avec les certificats

signer les agents

ouvrir le port 8140 sur le master pour gérer les signatures.

`puppetserver ca list --all` -> à la fin pour tester si les agents sont signés

pour signer se connecte à : https://puppet:8140/puppet-ca/v1/certificate_statuses/any_key

`puppet agent -t` pour lancer l'agent manuellement

### manifest

node default {
appliqué sur tous les noeuds master compris
}

node node1, node2{
appliqué seulement sur certains noeuds
}
