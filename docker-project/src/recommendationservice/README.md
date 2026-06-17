# RecommendationService

**RecommendationService** est un microservice Python qui fournit des recommandations de produits basées sur les articles actuellement consultés ou présents dans le panier.
Il interagit avec le **ProductCatalogService** via gRPC pour récupérer les produits disponibles.

---

## 📦 Fonctionnalités

* Recommande jusqu’à 5 produits aléatoires qui ne sont pas déjà présents dans la requête.
* Expose une API **gRPC**.
* Intègre **OpenTelemetry** pour la traçabilité distribuée.
* Supporte l’intégration avec **Google Cloud Profiler**.

---

## 🛠️ Prérequis

* Python 3.9 ou supérieur
* `pip` pour installer les dépendances
* Le fichier `requirements.txt` avec les bibliothèques nécessaires (ou installation manuelle)

---

## 🔧 Installation & Exécution

1. **Installer les dépendances :**

```bash
pip install -r requirements.txt
```

Si vous n'avez pas de fichier `requirements.txt`, vous pouvez installer manuellement :

```bash
pip install grpcio grpcio-tools google-cloud-profiler opentelemetry-sdk opentelemetry-exporter-otlp protobuf
```

2. **Configurer les variables d’environnement :**

```bash
export PORT=8080
export PRODUCT_CATALOG_SERVICE_ADDR=localhost:3550
export ENABLE_TRACING=1
export COLLECTOR_SERVICE_ADDR=localhost:4317
```

3. **Lancer le service :**

```bash
python recommendation_server.py
```

Le serveur gRPC démarrera sur le port spécifié (`8080` par défaut).

---

## ⚙️ Variables d’Environnement

| Variable                       | Description                                                            | Obligatoire             | Valeur par défaut |
| ------------------------------ | ---------------------------------------------------------------------- | ----------------------- | ----------------- |
| `PORT`                         | Port sur lequel écouter pour les requêtes gRPC                         | ❌                       | `8080`            |
| `PRODUCT_CATALOG_SERVICE_ADDR` | Adresse gRPC du service `productcatalogservice`                        | ✅                       | *aucune*          |
| `ENABLE_TRACING`               | Active OpenTelemetry si défini à `"1"`                                 | ❌                       | désactivé         |
| `COLLECTOR_SERVICE_ADDR`       | Adresse du collecteur OTLP pour exporter les traces                    | ✅ si `ENABLE_TRACING=1` | `localhost:4317`  |
| `DISABLE_PROFILER`             | Si défini, désactive Google Cloud Profiler                             | ❌                       | Profiler activé   |
| `GCP_PROJECT_ID`               | (optionnel) Projet GCP pour utiliser Stackdriver Profiler correctement | ❌                       | -                 |

---

## ✅ Santé & Requêtes

Le service implémente l’API de santé gRPC (`/Check`, `/Watch`) et une méthode `ListRecommendations` exposée via gRPC.
Vous pouvez tester avec un client gRPC comme `grpcurl` ou depuis un autre microservice.

---

## 📝 Remarques

* Les produits sont recommandés de manière aléatoire (jusqu’à 5 maximum).
* Le service s'arrête proprement sur interruption (`Ctrl+C`).
* Utilise un exécuteur multi-thread (`ThreadPoolExecutor`).
