```
DELETE sncf
PUT sncf
{
  "mappings": {
    "properties": {
      "Fin_controle": {
        "type": "date"
      },
      "Gare": {
        "type": "keyword"
      },
      "Gare_Code_UIC": {
        "type": "keyword"
      },
      "Nombre_Non_Conformites": {
        "type": "long"
      },
      "Nombre_Observations": {
        "type": "long"
      }
    }
  }
}



GET sncf/_count
GET sncf/_search

# Bonus
# Runtime field

# => suppression de tous les documents avec des trous
POST sncf/_delete_by_query
{
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "Fin_controle"
          }
        }
      ]
    }
  }
}


# => Ajout champ virtuel
PUT sncf/_mapping
{
  "runtime": {
    "day_of_week": {
      "type": "keyword",
      "script": {
        "source": "emit(doc['Fin_controle'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.FRENCH))"
      }
    }
  }
}

# Exemple d'aggrégation
GET sncf/_search
{
    "size": 0,
    "aggs": {
      "per_week_of_day": {
        "terms": {
            "field": "day_of_week"
        }
      }
    }
}

GET sncf/_count
{
  "query": {
    "term": {
      "day_of_week": {
        "value": "lundi"
      }
    }
  }
}

# => faire un graphique pour montrer quel jour il y a le plus de problèmes.









POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "filebeat-9.1.3",
        "alias": "mes-logs-apache",
        "filter": {
          "term": {
            "event.module": "apache"
          }
        }
      }
    }
  ]
}


GET filebeat-9.1.3/_search

GET montparnasse/_search
GET sncf

POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "sncf",
        "alias": "montparnasse",
        "filter": {
          "term": {
            "Gare_Code_UIC": "0087391003"
          }
        }
      }
    }
  ]
}

PUT index1
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "index1",
        "alias": "production"
      }
    }
  ]
}
PUT index2

POST _aliases
{
  "actions": [
    {
      "remove": {
        "index": "index1",
        "alias": "production"
      }
    },
    {
      "add": {
        "index": "index2",
        "alias" : "production"
      }
    }
  ]
}


GET _cat/nodes?v




DELETE animaux
PUT animaux 
{
  "settings": {
    "index.number_of_shards": 8,
    "number_of_replicas": 0
  }
}

GET _cat/shards/animaux?v&h=shard,docs

GET _refresh
POST animaux/_doc?refresh=true&routing=alice
{
  "name" : "faya",
  "age" : 12
}

GET animaux/_count?routing=chats,alice,chien

POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "animaux",
        "alias": "animaux-de-alice",
        "routing": "alice"
      }
    }
  ]
}

GET animaux-de-alice/_count

# numero shard = hash(_id) % numbre shards
# numero shard = hash(_routing) % numbre shards

```
