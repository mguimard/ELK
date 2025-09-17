```
# Création d'index
PUT fruits
PUT legumes

# Infos générales
GET /
GET _cluster/health

# Vues détaillées sur les noeuds, indices, shards, et répartition de charge

GET _cat/nodes?v
GET _cat/nodes?v&h=ip,cpu,name&s=cpu:desc
GET _cat/indices?v&h=index
GET _cat/shards?v
GET _cat/allocation?v

GET _cat/allocation?v&h=node,shards
```
