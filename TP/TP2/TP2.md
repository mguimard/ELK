# TP2 : Optimisation de l'index, requêtes avancées

Le but de ce TP est d'apprendre à 

* Optimiser un index
* Configurer logstash pour améliorer les performances de l'indexation

---

## 1ère partie : Mapping des documents

* Analyser les mappgins créés automatiquement par Elasticsearch pour l'index bank
* Créer un nouvel index (bank2) avec des mappings optimisés
* Re-insérer les données bancaires dans ce nouvel index
* Analyser la taille occupée sur le disque pour bank et bank2, noter le pourcentage d'espace disque gagné/perdu.

## 2eme partie : Requêtes avancées

Ecrire à l'aide des devtools les requêtes suivantes sur le nouvel index bank2 :

* Comptes ayant "street" dans le champ adresse (match ou term)
* Comptes ayant plus de 25 ans (range)
* Comptes dont le prénom est "Rose" à quelques fautes près (fuzzy)
* Comptes dont le prénom commence par "R" (prefix)
* Comptes dont le prénom contient un "E" (wildcard)
* Comptes dont l'age est inférieur à 25 ans et le compte en banque supérieur à 25000, booster le score des comptes dont le genre est féminin.

## 3eme partie : utilisation de KQL et filters

Dans l'onglet discover, ré-écrire les requêtes de la partie 2 à l'aide de KQL et/ou des filtres. Sauvegarder ces filtres.

## 4eme partie : découverte des maps

Créer une visualisation de type map, pour afficher la somme du champ "balance" par état (state) sur la carte des Etats-Unis.
