# Ecriture d'une pipeline Elastic

Le but de ce TP est de créer une pipeline d'ingestion pour parser de la donnée text.

## Exemple pour simuler une pipeline :

```
POST _ingest/pipeline/_simulate?filter_path=**._source
{
  "docs": [
    {"_source":{"message":"Ligne de log ici ..."}},
    {"_source":{"message":"Autre ligne de log ici ..."}}
  ],
  "pipeline": {
    "description":"Cette pipeline blablabla....",
    "processors": [
      {
        "set": {
          "field": "tag",
          "value": "urgent"
        }
      }
    ]
  }
}
```

## Les données

Echantillon de log :

```
2023-01-24 10:38:48.987396+00:00 [info] <0.229.0> Running boot step pre_boot defined by app rabbit
2023-01-24 10:38:49.001012+00:00 [info] <0.229.0> Running boot step code_server_cache defined by app rabbit
2023-01-24 10:38:49.001076+00:00 [info] <0.229.0> Running boot step file_handle_cache defined by app rabbit
2023-01-24 10:38:49.002188+00:00 [info] <0.229.0> Running boot step worker_pool defined by app rabbit
2023-01-24 10:38:49.002241+00:00 [info] <0.286.0> Will use 5 processes for default worker pool
2023-01-24 10:38:49.002287+00:00 [info] <0.286.0> Starting worker pool 'worker_pool' with 5 processes in it
2023-01-24 10:38:49.002531+00:00 [info] <0.229.0> Running boot step database defined by app rabbit
2023-01-24 10:38:49.003858+00:00 [info] <0.229.0> Node database directory at /var/lib/rabbitmq/mnesia/rabbit@af6809c8510d is empty. Assuming we need to join an existing cluster or initialise from scratch...
2023-01-24 10:38:49.003915+00:00 [info] <0.229.0> Configured peer discovery backend: rabbit_peer_discovery_classic_config
2023-01-24 10:38:49.003932+00:00 [info] <0.229.0> Will try to lock with peer discovery backend rabbit_peer_discovery_classic_config
2023-01-24 10:38:49.003976+00:00 [info] <0.229.0> All discovered existing cluster peers:
2023-01-24 10:38:49.003989+00:00 [info] <0.229.0> Discovered no peer nodes to cluster with. Some discovery backends can filter nodes out based on a readiness criteria. Enabling debug logging might help troubleshoot.
2023-01-24 10:38:49.119439+00:00 [info] <0.229.0> Waiting for Mnesia tables for 30000 ms, 9 retries left
2023-01-24 10:38:49.119725+00:00 [info] <0.229.0> Successfully synced tables from a peer
2023-01-24 10:38:49.126213+00:00 [info] <0.229.0> Feature flags: `feature_flags_v2`: supported, attempt to enable...
2023-01-24 10:38:49.220869+00:00 [info] <0.499.0> Waiting for Mnesia tables for 30000 ms, 9 retries left
2023-01-24 10:38:49.220981+00:00 [info] <0.499.0> Successfully synced tables from a peer
2023-01-24 10:38:49.429984+00:00 [info] <0.229.0> Waiting for Mnesia tables for 30000 ms, 9 retries left
2023-01-24 10:38:49.430060+00:00 [info] <0.229.0> Successfully synced tables from a peer
2023-01-24 10:38:49.437978+00:00 [info] <0.229.0> Waiting for Mnesia tables for 30000 ms, 9 retries left
2023-01-24 10:38:49.438072+00:00 [info] <0.229.0> Successfully synced tables from a peer
2023-01-24 10:38:49.438095+00:00 [info] <0.229.0> Peer discovery backend rabbit_peer_discovery_classic_config does not support registration, skipping registration.
2023-01-24 10:38:49.438118+00:00 [info] <0.229.0> Will try to unlock with peer discovery backend rabbit_peer_discovery_classic_config
2023-01-24 10:38:49.438156+00:00 [info] <0.229.0> Running boot step tracking_metadata_store defined by app rabbit
2023-01-24 10:38:49.438205+00:00 [info] <0.621.0> Setting up a table for connection tracking on this node: tracked_connection
2023-01-24 10:38:49.438234+00:00 [info] <0.621.0> Setting up a table for per-vhost connection counting on this node: tracked_connection_per_vhost
2023-01-24 10:38:49.438258+00:00 [info] <0.621.0> Setting up a table for per-user connection counting on this node: tracked_connection_per_user
2023-01-24 10:38:49.438277+00:00 [info] <0.621.0> Setting up a table for channel tracking on this node: tracked_channel
2023-01-24 10:38:49.438295+00:00 [info] <0.621.0> Setting up a table for channel tracking on this node: tracked_channel_per_user
2023-01-24 10:38:49.439229+00:00 [info] <0.229.0> Priority queues enabled, real BQ is rabbit_variable_queue
2023-01-24 10:38:49.457064+00:00 [info] <0.229.0> Management plugin: using rates mode 'basic'
2023-01-24 10:38:49.459815+00:00 [info] <0.229.0> Will seed default virtual host and user...
2023-01-24 10:38:49.459877+00:00 [info] <0.229.0> Adding vhost '/' (description: 'Default virtual host', tags: [])
2023-01-24 10:38:49.462284+00:00 [info] <0.229.0> Applying default limits to vhost '<<"/">>': []
2023-01-24 10:38:49.475207+00:00 [info] <0.676.0> Making sure data directory '/var/lib/rabbitmq/mnesia/rabbit@af6809c8510d/msg_stores/vhosts/628WB79CIFDYO9LJI6DKMI09L' for vhost '/' exists
2023-01-24 10:38:49.477540+00:00 [info] <0.676.0> Setting segment_entry_count for vhost '/' with 0 queues to '2048'
2023-01-24 10:38:49.480811+00:00 [info] <0.676.0> Starting message stores for vhost '/'
2023-01-24 10:38:49.481068+00:00 [info] <0.681.0> Message store "628WB79CIFDYO9LJI6DKMI09L/msg_store_transient": using rabbit_msg_store_ets_index to provide index
2023-01-24 10:38:49.482916+00:00 [info] <0.676.0> Started message store of type transient for vhost '/'
2023-01-24 10:38:49.485324+00:00 [info] <0.676.0> Started message store of type persistent for vhost '/'
2023-01-24 10:38:49.485389+00:00 [info] <0.676.0> Recovering 0 queues of type rabbit_classic_queue took 7ms
2023-01-24 10:38:49.485419+00:00 [info] <0.676.0> Recovering 0 queues of type rabbit_quorum_queue took 0ms
2023-01-24 10:38:49.485436+00:00 [info] <0.676.0> Recovering 0 queues of type rabbit_stream_queue took 0ms
2023-01-24 10:38:49.487133+00:00 [info] <0.229.0> Created user 'guest'
2023-01-24 10:38:49.488641+00:00 [info] <0.229.0> Successfully set user tags for user 'guest' to [administrator]
2023-01-24 10:38:49.490051+00:00 [info] <0.229.0> Successfully set permissions for 'guest' in virtual host '/' to '.*', '.*', '.*'
2023-01-24 10:38:49.490500+00:00 [info] <0.286.0> Starting worker pool 'definition_import_pool' with 5 processes in it
2023-01-24 10:38:49.490717+00:00 [info] <0.229.0> Running boot step cluster_name defined by app rabbit
2023-01-24 10:38:49.490758+00:00 [info] <0.229.0> Initialising internal cluster ID to 'rabbitmq-cluster-id-nZJPoEIR_-4jZYWewYYOZQ'
2023-01-24 10:38:49.519212+00:00 [info] <0.809.0> Starting worker pool 'management_worker_pool' with 3 processes in it
2023-01-24 10:38:49.524893+00:00 [info] <0.824.0> Prometheus metrics: HTTP (non-TLS) listener started on port 15692
2023-01-24 10:38:49.525012+00:00 [info] <0.723.0> Ready to start client connection listeners
2023-01-24 10:38:49.525875+00:00 [info] <0.868.0> started TCP listener on [::]:5672
```

## Préparation de l'index et exploitation

Créer la pipeline (my-pipeline) pour extraire les données, les champs à extraire sont les suivants (exemple pour la dernière ligne de log):

- @timestamp (type date): 2023-01-24 10:38:49.525875+00:00
- log.level (keyword) : info
- pid (keyword): <0.868.0>
- message (text): "started TCP listener on [::]:5672"
- `original_message` (non indexé) : toute la ligne originelle

Exemple pour la dernière ligne :

```json
{
  "@timestamp" : "2023-01-24 10:38:49.525875+00:00",
  "log" : {
    "level" : "info"
  },
  "pid" : "<0.868.0>",
  "message" : "started TCP listener on [::]:5672",
  "original" : {
    "message" :  "2023-01-24 10:38:49.525875+00:00 [info] <0.868.0> started TCP listener on [::]:5672"
  }
}
```

Créer l'index (my-index) avec les mappings correspondant à la pipeline, et insérer les données

```
curl -ku 'elastic' 'https://localhost:9200/my-index/_bulk' -H"Content-Type: application/json" --data-binary @data.ndjson
```

Ecrire une requête bool qui filtre sur le niveau de logs (info).

Ecrire une aggrégation qui compte le nombre de PID différents.



