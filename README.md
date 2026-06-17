# Online Boutique : Docker & Kubernetes Deployment

Déploiement complet de l'application cloud-native **Online Boutique**, réalisé en deux étapes : conteneurisation avec **Docker / Docker Compose**, puis orchestration en production avec **Kubernetes / Helm / Helmfile**.

---

## Présentation du projet

**Online Boutique** est une application e-commerce cloud-native composée de microservices écrits dans différents langages et communiquant entre eux en **gRPC**. Elle illustre les principes clés des architectures microservices : découplage des services, scalabilité horizontale, résilience et polyglot programming.

L'application a d'abord été entièrement conteneurisée et orchestrée avec Docker Compose, avant d'être migrée vers un cluster Kubernetes via des charts Helm.

### Architecture applicative

| Service | Langage | Rôle |
|---|---|---|
| frontend | Go | Sert l'interface web, gère les sessions automatiquement (pas de login requis) |
| cartservice | C# | Stocke et récupère le contenu du panier dans Redis |
| productcatalogservice | Go | Fournit le catalogue de produits |
| currencyservice | Node.js | Convertit les devises |
| paymentservice | Node.js | Simule des transactions de paiement |
| shippingservice | Go | Calcule les frais et simule l'expédition |
| emailservice | Python | Envoie des emails de confirmation de commande (simulés) |
| checkoutservice | Go | Orchestre le flux de commande (panier, paiement, livraison, confirmation) |
| recommendationservice | Python | Suggère des produits complémentaires |
| adservice | Java | Fournit des publicités contextuelles |
| redis | — | Stockage du panier |

---

## Structure du repo

```
.
├── docker/         # Partie 1 : conteneurisation avec Docker Compose
│   └── README.md
└── kubernetes/     # Partie 2 : déploiement sur Kubernetes avec Helm
    └── README.md
```

Chaque dossier contient son propre `README.md` avec les instructions détaillées d'installation et de déploiement.

---

## Partie 1 : Docker

Chaque microservice a été conteneurisé avec un **Dockerfile** dédié, en suivant des bonnes pratiques de sécurité et de performance : utilisateurs non-root, images allégées (`alpine`/`slim`), builds multi-stage et healthchecks.

L'ensemble est orchestré avec **Docker Compose**, avec un reverse proxy (**nginx-proxy**) gérant le trafic entrant et la terminaison SSL via un compagnon de certificats auto-signés.

➡️ Voir [`docker/README.md`](./docker-project/README.md) pour les instructions de lancement.

## Partie 2 : Kubernetes

L'application a été migrée sur un cluster **Kubernetes** (minikube), avec un **chart Helm** par microservice incluant a minima un `Deployment`, un `Service`, la gestion des variables d'environnement et des probes (`readinessProbe`, `livenessProbe`).

Le déploiement complet est centralisé via **Helmfile**, qui orchestre l'ensemble des charts et leurs dépendances entre les namespaces `boutique-backend` et `boutique-frontend`.

L'application est exposée via **Ingress NGINX**, avec un certificat TLS généré par **cert-manager** à partir d'un `ClusterIssuer` Let's Encrypt (environnement staging).

➡️ Voir [`kubernetes-project/README.md`](./kubernetes/README.md) pour les instructions de déploiement.

---

## Prérequis généraux

- [Docker](https://docs.docker.com/get-docker/) & [Docker Compose](https://docs.docker.com/compose/install/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/) & [Helmfile](https://github.com/helmfile/helmfile/releases)
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
