# Online Boutique – Kubernetes Deployment

- **Project Repository**: https://gitlab.unige.ch/pereira_oct/kubernetes-project
- **Helm Charts**: https://gitlab.unige.ch/pereira_oct/kubernetes-project/-/packages

---

## Prérequis

- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Helmfile](https://github.com/helmfile/helmfile/releases)
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- Le plugin helm-diff :

```bash
helm plugin install https://github.com/databus23/helm-diff
```

---

## Installation

### 1. Démarrer le cluster minikube

```bash
minikube start
```

### 2. Activer l'addon Ingress

```bash
minikube addons enable ingress
```

### 3. Installer Ingress NGINX

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

### 4. Installer cert-manager

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --set crds.enabled=true
```

### 5. Déployer tous les services

```bash
helmfile apply
```

Cela crée les namespaces `boutique-backend` et `boutique-frontend` et déploie l'ensemble des microservices ainsi que l'Ingress.

### 6. Créer les secrets pour le GitLab Container Registry

Les namespaces étant maintenant créés, on peut y ajouter les secrets :

```bash
kubectl create secret docker-registry gitlab-registry `
  --docker-server=registry.gitlab.unige.ch `
  --docker-username=<username> `
  --docker-password=<token> `
  --docker-email=<email> `
  --namespace boutique-backend

kubectl create secret docker-registry gitlab-registry `
  --docker-server=registry.gitlab.unige.ch `
  --docker-username=<username> `
  --docker-password=<token> `
  --docker-email=<email> `
  --namespace boutique-frontend
```

### 7. Relancer le déploiement

Les pods en `ImagePullBackOff` vont maintenant pouvoir récupérer les images :

```bash
helmfile apply
```

---

## Accès

Expose l'Ingress controller via minikube tunnel (dans un terminal séparé) :

```bash
minikube tunnel
```

L'application est alors accessible à :

- HTTPS : `https://boutique.127.0.0.1.nip.io`

Le certificat étant auto-signé, le navigateur affichera un avertissement de sécurité : cliquez sur **Avancé** → **Continuer vers le site**.

---

## Architecture

### Namespaces

Les services sont répartis en deux namespaces distincts :

- `boutique-backend` : tous les microservices internes (Redis, adservice, cartservice, checkoutservice, currencyservice, emailservice, paymentservice, productcatalogservice, recommendationservice, shippingservice)
- `boutique-frontend` : le frontend, seul service exposé à l'extérieur via l'Ingress

### Helm Charts

Chaque microservice possède son propre Helm chart dans `online-boutique/charts/`. Les charts suivent une structure commune :

- `Chart.yaml` : métadonnées du chart
- `values.yaml` : configuration du service (image, port, variables d'environnement)
- `templates/deployment.yaml` : déploiement Kubernetes
- `templates/service.yaml` : service Kubernetes

Le chart frontend inclut également `templates/ingress.yaml` qui définit la ressource Ingress.

### Helmfile

Le fichier `helmfile.yaml` orchestre le déploiement de tous les services en respectant les dépendances : Redis avant cartservice, productcatalogservice avant recommendationservice, etc. Les dépendances cross-namespace sont déclarées avec le format `namespace/release`.

### Ingress NGINX

Ingress NGINX expose le frontend à l'extérieur du cluster et route le trafic HTTPS vers le service frontend sur le port 8080.

---

## Troubleshooting

### Vérifier l'état des pods

```bash
kubectl get pods -n boutique-backend
kubectl get pods -n boutique-frontend
```

Ou pour tous les namespaces d'un coup :

```bash
kubectl get pods -A
```

### Voir les logs d'un service

```bash
kubectl logs <nom-du-pod> -n boutique-backend
```

### Vérifier l'état des certificats

```bash
kubectl get certificate -n boutique-frontend
kubectl describe certificate boutique-tls -n boutique-frontend
```

### Vérifier l'Ingress

```bash
kubectl get ingress -n boutique-frontend
kubectl describe ingress online-boutique -n boutique-frontend
```

### Redéployer tous les services

```bash
helmfile apply
```

### Supprimer tous les services

```bash
helmfile destroy
```