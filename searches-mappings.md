```
# Création pizza v2
PUT pizzas-v2 
{
  "mappings": {
    "properties": {
      "name" : {
        "type" : "keyword"
      },
      "prix" : {
        "type": "long"
      }
    }
  }
}

# Réindexation
POST _reindex
{
  "source": {"index" : "pizzas"},
  "dest": {"index" : "pizzas-v2"}
}


# Requetes

GET bank/_search
{
  "query": {
    "match": {
      "address": "Salut la Street"
    }
  }
}

GET bank/_search
{
  "query": {
    "term": {
      "firstname": {
        "value": "Rose"
      }
    }
  }
}

GET bank/_search
{
  "query": {
    "term": {
      "firstname.keyword": {
        "value": "rose",
        "case_insensitive": true
      }
    }
  }
}


GET bank/_search
{
  "query": {
    "prefix": {
      "firstname": {
        "value": "R"
      }
    }
  }
}


GET bank/_search
{
  "query": {
    "fuzzy": {
      "firstname": {
        "value": "rase"
      }
    }
  }
}


GET bank/_search
{
  "query": {
    "wildcard": {
      "address": {
        "value": "*z*",
        "case_insensitive": true
      }
    }
  }
}

GET bank/_search
{
  "query": {
    "regexp": {
      "address": "Street|Plaza"
    }
  }
}


GET bank/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "address": {
              "value": "street"
            }
          }
        },
        {
          "term": {
            "gender.keyword": {
              "value": "F"
            }
          }
        }
      ],
      "should": [
        {
          "prefix": {
            "firstname": {
              "value": "d",
              "boost": 1
            }
          }
        }
      ],
      "filter": [
        {
          "range": {
            "age": {
              "gt": 33
            }
          }
        }
      ]
    }
  }
}



# Maps


POST likes/_doc
{
  "dep" : "24",
  "likes" : 7000
}


# aggregations
GET likes/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "par_departement" : {
      "terms": {
        "field": "dep.keyword"
      },
      "aggs": {
        "likes": {
          "avg": {
            "field": "likes"
          }
        }
      }
    }
  }
}
```
