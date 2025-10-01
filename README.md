# Projet_data_flow


# Objectifs
    •  Récupérer les données météo des 14 régions du Sénégal via:
      -Créer un script Python pour générer automatiquement des données 
      -Scrapper de site pour récupérer des données
      -Utiliser des API(l’API Tomorrow.io) pour récupérer des données
      -Générer ces données dans différents formats : CSV, JSON, Excel, XML, YAML 
    •  Ingestion en temps réel via Kafka
    •  Traitement et nettoyage des flux avec Python
    •  Sauvegarde dans une base PostgreSQL
    •  Visualisation avec Streamlit ou BI (optionnel)

# Architecture du Projet

weather_producer.py → Kafka (topic_meteo) → weather_consumer.py → PostgreSQL
                                   
                               
# Structure du projet
    • docker-compose.yml             # Orchestration des services
    • weather_producer.py            # Producteur Kafka (API météo)
    • weather_consumer.py            # Consommateur Kafka → PostgreSQL
    •  sql/                           # Contient init_postgres.sql
    •  notebooks/                     # Analyses & visualisations 
    • data/                          # Sauvegarde JSON des données météo
    •  requirements.txt               # Dépendances Python
    • README.md                      # Documentation projet

# Lancement rapide

    • Cloner le projet : git clone https://github.com/jamdong58/Projet_DataFlow.git
    • Configurer votre clé API dans weather_producer.py
    • Lancer les services : docker-compose up -d
    • Installer les dépendances : pip install -r requirements.txt
    • Lancer le producteur météo : python weather_producer.py
    • Lancer le consommateur météo : python weather_consumer.py
 
# Technologies utilisées

    • Python
    • Apache Kafka
    • PostgreSQL
    • Tomorrow.io API
    • Jupyter Notebooks 
    • Docker

# À venir

    • Visualisation temps réel avec Streamlit ou Power BI
    • Monitoring avec ELK 
    • Ajout de modèles de prédiction météo

# Résumé global


En résumé, j’ai monté un gros pipeline temps réel de données météo de Dakar qui combine plusieurs briques :
    • Airflow dans Docker pour orchestrer tes tâches.
    • Kafka pour gérer le streaming des données.
    • HDFS/Hadoop + YARN pour le stockage distribué et l’exécution des jobs.
    • PySpark qui tourne dans un conteneur dédié, connecté à YARN, et qui lance ton script /opt/pyspark/streaming.py.
    • ELK (Elasticsearch, Logstash, Kibana) pour l’indexation et la visualisation des données.
    • Redis probablement comme cache/message broker pour certaines étapes.
Donc, concrètement :
    1. Airflow planifie/automatise tes DAGs, qui peuvent déclencher le spark-submit (soit via le conteneur PySpark, soit en appelant YARN directement).
    2. Kafka sert de source de données en continu pour PySpark Streaming.
    3. PySpark lit depuis Kafka, transforme les données, puis écrit soit dans Elasticsearch (pour Kibana), soit dans HDFS, soit ailleurs.
    4. HDFS est ton stockage distribué, géré par NameNode/DataNode.
    5. YARN (ResourceManager + NodeManager) orchestre les ressources pour Spark.
    6. ELK visualise et surveille les données et logs.
Alors pyspark/streaming.py doit donc :
    • Se connecter à Kafka (KAFKA_BROKER_URL: kafka:9092).
    • Lire les messages.
    • Faire les transformations .
    • Envoyer les résultats dans  HDFS.