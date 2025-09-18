```
# APIS

# Création d'index
PUT camions
GET camions
DELETE animaux
GET _cat/shards/animaux?v
GET _cat/shards?v&s=store:desc
PUT animaux
{
  "settings": {
    "index.number_of_shards": 4,
    "number_of_replicas": 1
  }
}
# verif - DESCRIBE TABLE
GET animaux

# Insertion de données
# POST pas d'id
POST animaux/_doc
{
  "name" : "felix"
}

# PUT maitrise l'id
PUT animaux/_doc/12345
{
  "age" : 132
}

# Lecture
HEAD animaux/_doc/12345
GET animaux/_doc/12345
GET animaux/_source/12345

# Suppression
DELETE animaux/_doc/12345

# PATCH
POST animaux/_update/12345
{
  "doc": {
    "name" : "coucou"
  }
}
GET animaux/_source/12345

POST animaux/_update/12345
{
  "script": {
    "source": """
      if(ctx._source.age < 135)
      ctx._source.age = ctx._source.age +1;
      """
  }
}




# Mapping

POST pizzas/_doc
{
  "name" : "Reine",
  "prix": 11
}
POST pizzas/_doc
{
  "name" : "Chorizo",
  "prix": "pas renseigné"
}
POST pizzas/_doc
{
  "name" : "Saumon",
  "prix": 11.5
}

GET pizzas/_search

GET pizzas/_search?q=name:saumon
GET fruits,pizzas/_search
GET p*/_search
GET _all/_search

GET pizzas/_search?q=name:saumon
GET pizzas/_search
{
  "query": {
    "match": {
      "name": "je suis un saumon"
    }
  }
}

GET pizzas/_search
{
  "query": {
    "term": {
      "name": {
        "value": "saumon"
      }
    }
  }
}

GET pizzas


# &explain=true
# dynamic mapping

# Analyse
GET _analyze?format=yaml&filter_path=**.token
{
  "text" : "J'aime le saumon."
}

GET _analyze?format=yaml&filter_path=**.token
{
  "text" : "FACT-1234-CLIENT-A"
}

# "FACT-1234-CLIENT-A"
# "FACT-1234-CLIENT-B"
# "FACT-1234-CLIENT-C"
# "FACT-4321-CLIENT-A"

GET bank

# text : searchable utile pour des phrases
# keyword: searchable, sortable, aggregatable, dédié aux codes, ids, etc...

# code (text, analysé): [fact, 1234, client, a]
# code.keyword (keyword, non analysé): FACT-1234-CLIENT-A
# json.code: FACT-1234-CLIENT-A

GET factures

GET factures/_search
{
  "query": {
    "term": {
      "code.keyword": {
        "value": "FACT"
      }
    }
  }
}

GET factures/_search
{
  "query": {
    "match": {
      "code": "J'adore aller a la plage"
    }
  }
}

GET factures/_search
{
  "query": {
    "term": {
      "code": {
        "value": "1234"
      }
    }
  }
}

GET factures/_search
{
  "query": {
    "term": {
      "code": {
        "value": "FACT"
      }
    }
  }
}



POST factures/_doc
{
  "code": "FACT-4321-CLIENT-A"
}







GET bank/_search
{
  "query": {
    "term": {
      "gender": {
        "value": "F"
      }
    }
  }
}

GET bank/_search
{
  "query": {
    "match": {
      "gender": "F et M ou G et peut etre X"
    }
  }
}


GET bank
GET bank/_search



GET pizzas/_search
{
  "query": {
    "fuzzy": {
      "name.keyword": {
        "value": "saupmon"
      }
    }
  }
}

```
