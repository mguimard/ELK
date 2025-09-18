```
# 1ere partie

## analyse de bank et création bank2
GET bank

PUT bank2
{
  "mappings": {
    "properties": {
      "account_number" : {
        "type" : "unsigned_long"
      },
      "address" : {
        "type" : "text"
      },
      "age" : {
        "type" : "short"
      },
      "balance" : {
        "type": "long"
      },
      "city" : {
        "type": "keyword"
      },
      "email" : {
        "type" : "keyword",
        "index": false
      },
      "employer" : {
        "type" : "keyword"
      },
      "firstname" : {
        "type" : "keyword"
      },
      "lastname" : {
        "type" : "keyword"
      },
      "gender" : {
        "type" : "keyword"
      },
      "state" : {
        "type" : "keyword"
      }
    }
  }
}

## Réindexation
POST _reindex?refresh=true
{
  "source": {"index": "bank"},
  "dest": {"index": "bank2"}
}

## Analyse de la taille disque
GET _cat/indices/bank*?v&h=index,store.size

# ------------------------------
# multi indexation d'un champ
# message
# message.en_allemand
# message.keyword
PUT blog
{
  "mappings": {
    "properties": {
      "title" : {
        "type" : "keyword"
      },
      "message" : {
        "type": "text",
        "fields": {
          "en_allemand" : {
            "type" : "text",
            "analyzer" : "german"
          },
          "en_keyword" : {
            "type" : "keyword"
          }
        }
      }
    }
  }
}



# 2eme partie

## Comptes ayant "street" dans le champ adresse (match ou term)
GET bank2/_search
{
  "query": {
    "term": {
      "address": {
        "value": "Street",
        "case_insensitive" : true
      }
    }
  }
}
GET bank2/_search
{
  "query": {
    "match": {
      "address": "Salut la StrEEt"
    }
  }
}

## Comptes ayant plus de 25 ans (range)
GET bank2/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 25
      }
    }
  }
}
## Comptes dont le prénom est "Rose" à quelques fautes près (fuzzy)
GET bank2/_search
{
  "query": {
    "fuzzy": {
      "firstname": {
        "value" : "Roze"
      }
    }
  }
}

## Comptes dont le prénom commence par "R" (prefix)
GET bank2/_search
{
  "query": {
    "prefix": {
      "firstname": {
        "value": "r",
        "case_insensitive": true
      }
    }
  }
}


## Comptes dont le prénom contient un "E" (wildcard)
GET bank2/_search
{
  "query": {
    "wildcard": {
      "firstname": {
        "value": "*e*",
        "case_insensitive" : true
      }
    }
  }
}
## Comptes dont l'age est inférieur à 25 ans et le compte en banque supérieur à 25000, booster le score des comptes dont le genre est féminin.

GET bank2/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "age": {
              "gte": 25
            }
          }
        },
        {
          "range": {
            "balance": {
              "gte": 25000
            }
          }
        }
      ],
      "minimum_should_match": 0,
      "should": [
        {
          "term": {
            "gender": {
              "value": "F",
              "boost": 56
            }
          }
        }
      ]
    }
  }
}



















```
