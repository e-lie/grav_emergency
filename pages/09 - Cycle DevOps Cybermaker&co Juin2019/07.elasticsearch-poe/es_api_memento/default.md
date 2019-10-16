---
title: 'Memento Elasticsearch API (version 6.x)'
---

Ce memento fournit quelques méthodes CRUD basiques pour l'usage de l'API Elasticsearch. Il est très limité

La docuementation officielle de l'API se trouve ici: []()

## Gérer les documents

#### Créer un document:
```json
PUT /<index>/<type>/1
{
  "champ1": "value1",
  "champ2": "value2"
}
```
ou
```
POST /<index>/<type>
{
  "champ1": "value1",
  "champ2": "value2"
}
```

#### Afficher un document:
```json
GET /<index>/<type>/<num>/
```

#### Lister tous les documents:
```json
GET /<index>/<type>/_search
```

#### Mettre à jour un document (ajouter modifier un champ)
```json
POST /<index>/<type>/<num>/_update
{
    "doc": {
        "field": "value"
    }
}
```

#### Supprimer un document
```json
DELETE /<index>/<type>/<_id>
```

## Gérer les index

#### List Indices
```json
GET /_cat/indices
```
avec le nom des colonnes
```
GET /_cat/indices?v
```

#### Create index
```json
PUT /<index>
{
    "settings": {
        "number_of_shards": 1, // default 5
        "number_of_replicas": 0 // default 1
    }
}
```
Avec un mapping directement
```json
PUT /<index>
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  },
  "mappings": {
    "<mapping>": {
      "properties": {
        "<property>": {
          "type": "<datatype>"
        },
        ...
      }
    }
  }
}
```

#### Supprimer un index
```
DELETE /<index>
```

## Gérer les mappings

#### Lister les mappings
```
GET /<index>/_mapping
```

#### Ajouter un champ à un mapping:
```json
PUT /<index>/_mapping/<type>
{
  "properties": {
    "<new_fieldname>": {
      "type": "<datatype>"
    }
  }
}
```

#### Réindexer des données
Dupliquer un champ et réindexer
```json
POST /<index>/_update_by_query
{
  "script": {
    "inline": "ctx._source.<fieldname> = ctx._source.<fieldname>"
  }
}
```