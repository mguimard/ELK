```
# Création d'un index avec un type ip
PUT requests
{
  "mappings": {
    "properties": {
      "ip" : {
        "type": "ip"
      }
    }
  }
}

# 45.2849° N, 5.8830° E
# envoi de donnée avec ip et user agent
POST requests/_doc?pipeline=recup-geoloc
{
  "ip" : "164.129.114.5",
  "user" : "morgan",
  "ua" : "Mozilla/5.0 (X11; Linux x86_64; rv:137.0) Gecko/20100101 Firefox/137.0" 
}
# check le document enrichi
GET requests/_source/PPLdXJkBjRoxz5IMV2N-

# enregistre une pipeline
PUT _ingest/pipeline/recup-geoloc
{
  "description": "Récupere la loc d'une ip, et check le matos utilisé",
  "processors": [
    {
      "geoip": {
        "field": "ip"
      }
    },
    {
      "user_agent": {
        "field": "ua"
      }
    }
  ]
}




GET requests/_source/OPLYXJkBjRoxz5IMvWNT

#    "data": "max;12;chien"


# %{IP:mon-ip}%{SPACE}-%{SPACE}-%{SPACE}\[%{HTTPDATE:my-date}\]
# 46.72.177.4 - - [12/Dec/2015:18:31:08 +0100] "GET /administrator/ HTTP/1.1" 200 4263 "-" "Mozilla/5.0 (Windows NT 6.0; rv:34.0) Gecko/20100101 Firefox/34.0" "-"

# exemples de simulations de pipelines (mode développement)
POST _ingest/pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "message" : "46.72.177.4 - - [12/Dec/2015:18:31:08 +0100] \"GET /administrator/ HTTP/1.1\" 200 4263 \"-\" \"Mozilla/5.0 (Windows NT 6.0; rv:34.0) Gecko/20100101 Firefox/34.0\" \"-\""
      }
    },
    {
      "_source": {
        "message" : "46.72.177.4 GET"
      }
    }
  ],
  "pipeline": {
    "processors": [
      {
        "grok" : {
          "field" : "message",
          "patterns" : [
            "%{IP:mon-ip}%{SPACE}-%{SPACE}-%{SPACE}\\[%{HTTPDATE:my-date}\\]",
            "%{IP:mon-ip}%{SPACE}%{WORD:verb}"
          ]
        }
      },
      {
        "set" : {
          "field" : "tag",
          "value" : "request",
          "if" : "ctx?.verb == \"GET\""
        }
      }
    ]
  }
}





POST _ingest/pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "a": 1,
        "b" : 2
      }
    }
  ],
  "pipeline": {
    "processors": [
      {
        "script": {
          "source": """ 
            ctx['c'] = ctx['a'] + ctx['b']
            """,
            "if" : "ctx?.b == 2"
        }
      }
    ],
    "on_failure": [
      {
        "set": {
          "field": "_index",
          "value": "erreurs-{{ _index }}"
        }
      }
    ]
  }
}


POST _ingest/pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "a": 1,
        "b" : 2
      }
    }
  ],
  "pipeline": {
    "processors": [
      {
        "script": {
          "source": """ 
            ctx['c'] = ctx['a'] + ctx['b']
            """
        }
      }
    ],
    "on_failure": [
      {
        "set": {
          "field": "_index",
          "value": "erreurs-{{ _index }}"
        }
      }
    ]
  }
}



POST _ingest/pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "data": "ABC;DEF;BLABLA"
      }
    },
    {
      "_source": {
        "data": "max;12;chien"
      }
    }
  ],
  "pipeline": {
    "processors": [
      {
        "csv": {
          "field": "data",
          "separator": ";",
          "target_fields": [
            "name",
            "age",
            "type"
          ]
        }
      },
      {
        "rename": {
          "field": "data",
          "target_field": "event.original"
        }
      },
      {
        "convert": {
          "field": "age",
          "type": "integer"
        }
      }
    ],
    "on_failure": [
      {
        "set": {
          "field": "_index",
          "value": "erreurs-{{ _index }}"
        }
      }
    ]
  }
}


PUT _ingest/pipeline/mon-parser-custom
{
  "description" : "Parse une chaine CSV qui décrit un animal",
  "processors": [
    {
      "csv": {
        "field": "data",
        "separator": ";",
        "target_fields": [
          "name" , "age" , "type"
        ]
      }
    },
    {
      "rename": {
        "field": "data",
        "target_field": "event.original"
      }
    }
  ]
}
```
