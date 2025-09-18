```
# TP3
# 1. Création index / mappings
PUT my-index
{
  "mappings": {
    "properties": {
      "@timestamp" : {
        "type" :"date"
      },
      "log" : {
        "properties": {
          "level" : {
            "type" : "keyword"
          }
        }
      },
      "pid" : {
        "type" : "keyword"
      },
      "message" : {
        "type" : "text"
      },
      "event" : {
        "properties": {
          "original" : {
            "type" : "keyword",
            "index" : false
          }
        }
      }
      
    }
  }
}

# 2. Création pipeline
# 2023-01-24 10:38:48.987396+00:00 [info] <0.229.0> Running boot step pre_boot defined by app rabbit
#%{TIMESTAMP_ISO8601:@timestamp}%{SPACE}\[%{WORD:log.level}\]%{SPACE}<%{DATA:pid}>%{SPACE}%{GREEDYDATA:message}

PUT _ingest/pipeline/my-pipeline
{
  "description" : "Pipeline pour logs rabbitmq",
  "processors": [
    {
      "rename": {
        "field": "message",
        "target_field": "event.original"
      }
    },
    {
      "grok": {
        "field": "event.original",
        "patterns": [
          "%{TIMESTAMP_ISO8601:tmp_date}%{SPACE}\\[%{WORD:log.level}\\]%{SPACE}<%{DATA:pid}>%{SPACE}%{GREEDYDATA:message}"
        ]
      }
    },
    {
      "date": {
        "field": "tmp_date",
        "formats": [
          "yyyy-MM-dd HH:mm:ss.SSSSSSZZZZZ"
        ]
      }
    },
    {
      "remove": {
        "field": "tmp_date"
      }
    }
  ]
}

# 3. Injection de données
#  curl -ku 'elastic:u_kpabtRsbF7=RuEGu4P' https://localhost:9200/my-index/_bulk -H"Content-Type: application/json" --data-binary @data.ndjson

# 4. Vérification données
GET my-index/_search

# Compter les pids différents
GET my-index/_search
{
  "aggs": {
    "par_pid": {
      "terms": {
        "field": "pid"
      }
    }
  }
}

```
