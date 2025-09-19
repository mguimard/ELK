
# Bonus

```
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

# => faire un graphique pour montrer quel jour il y a le plus de problèmes.
```
