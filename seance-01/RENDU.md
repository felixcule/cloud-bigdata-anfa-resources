# Rendu Séance 1

**Nom et prénom :** YEYE Koffi Gagnon

## Résumé de la séance

La séance 01 introduit les fondamentaux du Cloud Computing à travers le projet Anfa, une plateforme de données pour le transport urbain. Le cloud permet de résoudre les problèmes de déploiement, de scalabilité, de disponibilité et de gestion des environnements grâce à cinq caractéristiques clés : libre-service, accès réseau, mutualisation, élasticité et facturation à l'usage. Les modèles de service (IaaS, PaaS, SaaS et FaaS) ainsi que les modèles de déploiement (public, privé, hybride et multi-cloud) sont présentés. 
Une attention particulière est portée au risque de dépendance fournisseur (vendor lock-in) et aux stratégies de portabilité basées sur l'open source et la conteneurisation. 
En TP, nous avons installé Docker, lancé un serveur MinIO dans un conteneur, créé un bucket de stockage objet et généré des clés applicatives. 
Enfin, nous avons développé un script Python utilisant l'API S3 pour déposer automatiquement les fichiers du référentiel Anfa dans MinIO, première brique de la plateforme data.

## Étapes principales

1. Installation et vérification de l'environnement (docker, github)
2. Forker le dépôt du cours et préparer votre branche de travail
3. Récupérer l'image MinIO et la lancer
4. Administrer MinIO en ligne de commande avec mc
5. Déposer le référentiel d'Anfa via Python
6. Aperçu : la même chose avec docker-compose.yml
7. Rédiger le RENDU et soumettre

## Capture d'écran

[def]: bucket-anfa-raw.png

## Difficultés rencontrées

Pas de difficultés tout s'est exécuté correctement !

## Exercices d'application

Exercice 1 : QCM conceptuel

1.1 D. Open source obligatoire
L'open source n'est pas une caractéristique essentielle du cloud selon le NIST puisque un cloud peut être basé sur des technologies propriétaires.

1.2 C. SaaS
Gmail est une application prête à l'emploi accessible via internet sans gestion de l'infrastructure par l'utilisateur.

1.3 D. FaaS
Le FaaS permet d'exécuter une fonction uniquement lorsqu'un événement survient, sans maintenir un serveur actif en permanence.

1.4 C. Cloud hybride
Les données sensibles peuvent rester dans un environnement privé tandis que les traitements moins sensibles profitent de l'élasticité du cloud public.

1.5 B. La situation où une entreprise ne peut plus changer de fournisseur sans coûts ou risques majeurs
Le vendor lock-in crée une dépendance technique ou économique à un fournisseur.

1.6 C. Un service open source est forcément moins performant qu'un service managé propriétaire
Cette affirmation est fausse car de nombreuses solutions open source offrent des performances comparables aux solutions propriétaires.



Exercice 2 : Classification de services

------------------------------------------------------------------------------------------------------
| Service                | Modèle| Justification                                                      |
|------------------------|------|------------------------------------------------------------------   |
| Google Compute Engine  | IaaS | Fournit des machines virtuelles que l'utilisateur administre.       |
| AWS Lambda             | FaaS | Exécute du code à la demande en réponse à des événements.           |
| Snowflake              | SaaS | Service complet d'entrepôt de données accessible en ligne.          |
| Heroku                 | PaaS | Fournit une plateforme de déploiement d'applications sans gérer les |
                         |      |serveurs.                                                            |
| Microsoft 365          | SaaS | Applications bureautiques accessibles via Internet.                 |
| Databricks             | PaaS | Plateforme managée pour l'analyse de données et Spark.              |
| Microsoft Azure Functions | FaaS | Exécution de fonctions déclenchées par événements.               |
| Tableau Online         | SaaS | Solution d'analyse et de visualisation disponible via le web.       |
-------------------------------------------------------------------------------------------------------

Exercice 3 : Lecture et interprétation (moyen)

3.1 Commande Docker

docker run -d --name analyse-anfa -p 8888:8888 -v /home/koffi/notebooks:/notebooks \
-e JUPYTER_TOKEN=anfa-token \
jupyter/pyspark-notebook

-d : Lance le conteneur en arrière-plan.

--name analyse-anfa : Donne le nom analyse-anfa au conteneur.

-p 8888:8888 : Associe le port 8888 du conteneur au port 8888 de la machine hôte.

-v /home/koffi/notebooks:/notebooks : Monte le dossier local dans le conteneur pour partager les fichiers.

-e JUPYTER_TOKEN=anfa-token : Définit une variable d'environnement servant de mot de passe d'accès.

jupyter/pyspark-notebook : Image Docker contenant Jupyter Notebook et PySpark.


Cette commande entiere lance un conteneur Jupyter/PySpark en arrière-plan. Les notebooks sont stockés dans un dossier local persistant et l'accès au service est sécurisé par un jeton.

3.2 Docker Compose

a) Le service est accessible depuis :
  http://localhost:9000 (API S3)
  http://localhost:9001 (console web MinIO)

b) Les données ne sont pas perdues. Elles sont stockées dans le volume nommé minio-data, qui subsiste même après la suppression du conteneur. Lors du redémarrage, Docker réutilise ce volume.

c) Le mot de passe administrateur apparaît en clair :
  MINIO_ROOT_PASSWORD: secret

  En production, il faudrait utiliser un gestionnaire de secrets ou des variables d'environnement sécurisées.


Exercice 4 : Diagnostic

a) L'erreur provient de l'utilisation des mauvais identifiants dans le script. 
L'étudiant utilise :
aws_access_key_id="anfa-admin"
aws_secret_access_key="anfa-password-2026"
alors que MinIO attend les clés applicatives créées avec : mc admin user svcacct add

b) Le code doit être corrigé ainsi :

import boto3
s3 = boto3.client(
    "s3",
    endpoint_url="http://localhost:9000",
    aws_access_key_id="anfa-app-key",
    aws_secret_access_key="anfa-app-secret-2026",
    region_name="us-east-1",
)
s3.upload_file("trajets.csv", "anfa-raw", "trajets.csv")

c) Les identifiants anfa-admin / anfa-password-2026 servent à l'administration et à la connexion à la console web. Pour accéder à l'API S3 depuis une application, MinIO exige des clés d'accès compatibles S3 (service accounts), ce qui améliore la sécurité et permet de limiter les permissions.

Exercice 5 : Mini-cas d'architecture

a) Deux limites de l'architecture actuelle
  - Les prédictions ne sont mises à jour qu'une fois par mois à partir d'un fichier CSV.
  - Le traitement dépend d'un seul ordinateur, ce qui limite la disponibilité et la puissance de calcul.

b) Caractéristiques du cloud adaptées aux besoins

---------------------------------------------------------------------------------------------------------
| Besoin               | Caractéristique NIST | Explication                                             |
|----------------------|----------------------|---------------------------------------------------------|
| Prédictions chaque heure | Élasticité rapide | Les ressources peuvent être ajustées automatiquement selon la charge. |
| Tableau de bord partagé  | Accès réseau étendu | Les utilisateurs accèdent au service via Internet.   |
| Augmenter la capacité lors des pics | Élasticité rapide | Les ressources sont allouées dynamiquement. |
| Maîtriser les coûts                 | Service mesuré    | Paiement selon l'usage réel.                |
| Conserver les données sensibles     | Mutualisation contrôlée / Cloud privé | Les données restent dans un environnement maîtrisé. |
---------------------------------------------------------------------------------------------------------


c) Les Modèles de service : 

  (i) Tableau de bord partagé : SaaS
      Les utilisateurs accèdent simplement à l'application via un navigateur.
  (ii) Calcul des prédictions à l'heure : FaaS
      Une fonction peut être déclenchée automatiquement toutes les heures.
  (iii) Stockage des données clients : IaaS
      L'entreprise garde davantage de contrôle sur l'infrastructure et les données sensibles.

d) Modèle de déploiement recommandé est : Cloud hybride.

  Parce que Les données clients sensibles peuvent être stockées dans un cloud privé ou sur une infrastructure contrôlée, tandis que les traitements analytiques et les montées en charge utilisent le cloud public. Cette approche combine conformité, sécurité et élasticité.

e) Je cite trois stratégies contre le vendor lock-in :
  - Utiliser des technologies open source (MinIO, PostgreSQL, Kafka).
  - Conteneuriser les applications avec Docker.
  - Stocker les données dans des formats standards et portables (CSV, Parquet, JSON).
