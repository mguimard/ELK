# TP5 Utilisation de Logstash et graphiques Kibana

Le but de ce TP est d'apprendre à utiliser Logstash pour peupler un index Elasticsearch, d'executer des requêtes avancées via la console DevTools et créer des visualisations avancées dans Kibana

Fichiers utilisés (fournis) :

* Données `sncf-non-conformites.csv`
* Mapping de l'index : `sncf-mappings.txt`
* Configuration Logstash : `sncf.conf`

## Création des mappings

Analyser le fichier `sncf-mappings.txt` et le jouer dans la console DevTools.

Apporter des modifications si besoin (type de champs, etc...)

## Insertion de données

Récupérer le fichier de configuration Logstash. Ouvrir et éditer le fichier si besoin (chemin absolu du fichier de données, nom de l'index...)

Lancer l'import des données 

`bin/logstash -f /chemin/complet/vers/sncf.conf`

Vérifier que la base de données est bien remplie en explorant les données avec Kibana ou en utilisant la commande :

```
GET sncf/_count
```

## Graphiques Kibana

Dans Kibana :

Afficher l'évolution du nombre de non conformités
 pour la gare montparnasse, mois par mois pour l'année 2016

Sur le même graphique :
Afficher l'évolution du nombre d'observations 
 pour la gare montparnasse, mois par mois pour l'année 2016

* Imaginer des vues ayant de la pertinence en fonction des données
* Créer un graphique de chaque type ayant du sens
* Modifier les options des graphiques pour influer sur les échelles et couleurs des graphiques

## Alias filtré

Créer un alias nommé "montparnasse", qui pointe sur l'index précédement créé, et qui ne présente que les données de cette gare.









