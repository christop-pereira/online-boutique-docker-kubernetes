# ProductCatalogService

**ProductCatalogService** est un microservice écrit en Go, chargé de fournir la liste des produits de la boutique.
Il expose une API **gRPC** et charge les données depuis un fichier statique JSON.

---

## 🔧 Fonctionnalités

* Expose une API gRPC permettant de :

  * Récupérer la liste des produits.
  * Obtenir un produit spécifique.
* Intègre les outils d’observabilité (tracing, profiling).
* Gère une latence artificielle pour simuler des comportements réalistes.
* Permet de recharger dynamiquement le catalogue à l’aide de signaux système (`SIGUSR1`, `SIGUSR2`).

---

## 🚀 Build & Exécution

### Prérequis

* Go 1.20 ou supérieur
* Accès au fichier de catalogue JSON (`products.json`)
* (Facultatif) OpenTelemetry collector si `ENABLE_TRACING` est activé

### Compilation

```bash
go build -o productcatalogservice .
```

### Lancement

```bash
PORT=3550 ./productcatalogservice
```

---

## ⚙️ Variables d’environnement

| Variable                 | Description                                                                   | Obligatoire             | Valeur par défaut |
| ------------------------ | ----------------------------------------------------------------------------- | ----------------------- | ----------------- |
| `PORT`                   | Port sur lequel le serveur gRPC sera lancé                                    | ❌                       | `3550`            |
| `ENABLE_TRACING`         | Active le tracing OpenTelemetry si défini à `"1"`                             | ❌                       | Tracing désactivé |
| `COLLECTOR_SERVICE_ADDR` | Adresse du collector OTLP pour exporter les traces                            | ✅ si `ENABLE_TRACING=1` | N/A               |
| `DISABLE_PROFILER`       | Si défini, désactive l’envoi des données de profiling à Google Cloud Profiler | ❌                       | Profiler activé   |
| `EXTRA_LATENCY`          | Temps de latence simulée (ex: `100ms`, `1s`) injectée dans les appels         | ❌                       | Aucune            |

---

## 🔁 Rechargement du catalogue à chaud

Le service peut activer ou désactiver dynamiquement le rechargement du catalogue via des signaux :

* `SIGUSR1` → Active le rechargement automatique
* `SIGUSR2` → Désactive le rechargement

Utilisation avec `kill` :

```bash
kill -USR1 <pid>  # Active
kill -USR2 <pid>  # Désactive
```

---

## 🧪 Test gRPC

Ce service ne fournit pas d’interface HTTP. Pour tester les appels :

* Utilisez `grpcurl` ou un client gRPC dans un autre langage.
* Exemple :

```bash
grpcurl -plaintext localhost:3550 list
```

---

## 🔒 Observabilité (Optionnel)

* Si `ENABLE_TRACING=1`, vous devez configurer un `COLLECTOR_SERVICE_ADDR` valide.
* Le service utilise OTLP/gRPC pour exporter les traces.
* Le profiling utilise Google Cloud Profiler (désactivé si `DISABLE_PROFILER` est défini).
