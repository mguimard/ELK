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














```
