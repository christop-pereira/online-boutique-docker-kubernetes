# ShippingService

**ShippingService** est un microservice écrit en Go, responsable de la simulation des coûts d’expédition et de la génération de numéros de suivi de commande.
Il fait partie de l'application **Online Boutique** et expose une API **gRPC**.

---

## ✨ Fonctionnalités

* Calcule un tarif d’expédition basé sur le nombre d’articles.
* Génère un identifiant de suivi (tracking ID) simulé.
* Expose deux méthodes principales :

  * `GetQuote`: retourne le coût de livraison.
  * `ShipOrder`: retourne un identifiant de suivi.
* Implémente une API de **vérification d'état (health check)**.
* Compatible avec Google Cloud Profiler et OpenTelemetry (profilage et traçabilité).

---

## ⚙️ Prérequis

* Go 1.20 ou plus récent
* gRPC et `protoc` installés si vous devez recompiler les `.proto`
* (Optionnel) Environnement GCP ou collecteur OpenTelemetry

---

## 🛠️ Compilation & Lancement

### Étapes :

1. **Compiler le binaire :**

```bash
go build -o shippingservice .
```

2. **Définir les variables d’environnement (facultatif selon votre besoin) :**

```bash
export PORT=50051
```

3. **Lancer le service :**

```bash
./shippingservice
```

Le service écoutera par défaut sur le port `50051`, sauf si `PORT` est défini.

---

## 🌐 Variables d’Environnement

| Variable           | Description                                                              | Obligatoire | Valeur par défaut |
| ------------------ | ------------------------------------------------------------------------ | ----------- | ----------------- |
| `PORT`             | Port d'écoute gRPC du service                                            | ❌           | `50051`           |
| `DISABLE_PROFILER` | Si défini, désactive Google Cloud Profiler                               | ❌           | Profiling activé  |
| `DISABLE_TRACING`  | Si défini, désactive l’init du traçage distribué (tracing)               | ❌           | Tracing activé\*  |
| `DISABLE_STATS`    | Si défini, désactive les statistiques OpenTelemetry (non encore actives) | ❌           | Stats activées    |

> 💡 \*Actuellement, le tracing est mentionné mais non encore implémenté (`TODO` dans le code).

---

## ✅ Contrôle de Santé

Ce service implémente l’API standard de **health check gRPC** :

* `Check` : retourne l’état `SERVING`
* `Watch` : non implémentée (retourne une erreur)

---

## 🧪 Test rapide avec grpcurl

```bash
grpcurl -plaintext localhost:50051 list
grpcurl -plaintext -d '{}' localhost:50051 hipstershop.ShippingService/GetQuote
```

---

## 📝 Remarques

* Le calcul du tarif est simulé (non dynamique).
* Le profilage est actif par défaut (sauf en local sans GCP).
* Le tracking ID est généré à partir de l’adresse de livraison simulée.
