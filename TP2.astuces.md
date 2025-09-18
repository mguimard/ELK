1. Récupérer le mapping exitant

```
GET bank
```

2. Créer index bank2, avec mapping custom
```
PUT bank2
{
    "mappings" : {
        "properties" : {
            "account_number" : {
                "type" : "number"
            },
            # ....
        }
    }
}
```

3. réindexation bank -> bank2

```
POST _reindex 
{
    "source" : {"index": "bank"},
    "dest" : {"index": "bank2"}
}
```

4. Analyse taille disque

```
GET _cat/indices/bank*
```

