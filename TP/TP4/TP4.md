# TP 4 - Elastic Stack – Filebeat + Apache Access Logs

## Objectif

Mettre en place une chaîne complète de collecte et visualisation de logs Apache :  

```
access.log → Filebeat → Elasticsearch → Kibana
```

Deux approches seront étudiées :  
1. **Filestream simple** (direct sans parsing avancé)  
2. **Module Apache** (avec parsing automatique et dashboards préconfigurés)  

---

## Phase 1 – Filestream + DataStream par défaut

### 1. Installation de Filebeat

#### Windows
- Télécharger Filebeat depuis [Elastic.co](https://www.elastic.co/downloads/beats/filebeat)
- Extraire l’archive dans `C:\Filebeat`
- Ouvrir une invite PowerShell en administrateur

#### macOS / Linux
```bash
wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-9.1.3-darwin-aarch64.tar.gz
tar -xvf filebeat-9.1.3-darwin-aarch64.tar.gz
cd filebeat-9.1.3-darwin-aarch64
```

---

### 2. Configuration Filebeat (filestream)

Configuration dans le fichier `filebeat.yml` un input `filestream` pour lire le fichier `apache1.log`.  

```yaml
filebeat.inputs:
- type: filestream
  id: apache-filestream
  enabled: true
  paths:
    - /Users/etudiant/Documents/logs/apache1.log   # macOS/Linux
    #- C:\Users\etudiant\Documents\logs\apache1.log   # Windows

setup.ilm.enabled: false

output.elasticsearch:
  hosts: ["https://localhost:9200"]
  username: "elastic"
  password: "changeme"
  ssl.verification_mode: none
  #ssl.certificate_authorities: ["/Users/etudiant/certs/ca.crt"]
  # ssl.certificate_authorities: ["C:\Users\etudiant\certs\ca.crt"]

setup.kibana:
  host: "http://localhost:5601"
  username: "elastic"
  password: "changeme"
```
---

### 3. Lancement et ingestion du fichier

#### macOS / Linux
```bash
./filebeat setup dashboards
./filebeat -e
```

#### Windows
```powershell
.\filebeat setup dashboards
.\filebeat.exe -e
```

Déposer le fichier `apache1.log` dans le répertoire configuré.

---

### 4. Vérification dans Kibana

- **Stack Management > Data Streams** : vérifier `.ds-filebeat-*`  
- **Discover** : vérifier la présence des logs dans le Data View  

---

## Phase 2 – Module Apache (parsing et dashboards)

Stopper le processus FileBeat (CTRL-C)

### 1. Activer le module Apache

#### macOS / Linux
```bash
./filebeat modules enable apache
```

#### Windows
```powershell
.\filebeat.exe modules enable apache
```

---

### 2. Configuration du module

Éditer `modules.d/apache.yml` et activer le fileset **access**.

```yaml
filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: true

  # Period on which files under path should be checked for changes
  #reload.period: 10s

filebeat.modules:
- module: apache
  access:
    enabled: true
    var.paths:
      - /Users/etudiant/Documents/Formations/ELK/cluster/logs/*.log
      #- C:\Users\etudiant\Documents\logs\apache1.log   # Windows
```

---

### 3. Configuration globale (filebeat.yml)

S’assurer que la sortie est configurée pour Elasticsearch (https) et Kibana (cf partie 1).  

---

### 4. Lancement et ingestion

#### macOS / Linux
```bash
./filebeat setup --modules apache
./filebeat -e
```

#### Windows
```powershell
.\filebeat.exe setup --modules apache
.\filebeat.exe -e
```

Puis déposer `apache2.log` dans le répertoire configuré.

---

### 5. Vérification et Dashboards

- Dans Kibana > **Dashboard**, ouvrir `Filebeat Apache] Access and error logs ECS`  
- Observer les métriques enrichies (status codes, geoip, user agents)  
- Dans **Discover**, vérifier que les logs sont correctement parsés avec ECS  

---

## Résultat attendu

- **Phase 1 (filestream)** : logs bruts visibles dans Kibana via `.ds-filebeat-*`  
- **Phase 2 (module Apache)** : logs enrichis, champs ECS renseignés et dashboards prêts à l’emploi  

