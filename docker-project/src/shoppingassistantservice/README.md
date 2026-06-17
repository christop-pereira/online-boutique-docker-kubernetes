# 🧠 Gemini Interior Design Recommender

Cette application Flask met en œuvre un système de **RAG (Retrieval-Augmented Generation)** pour recommander des articles de décoration d’intérieur à partir d’une image de pièce.
Elle utilise **Gemini (Google Generative AI)**, **LangChain**, et **AlloyDB VectorStore** pour fournir des recommandations basées sur le style détecté dans l’image et la demande utilisateur.

---

## 🚀 Fonctionnalités

* Analyse d’une image de pièce via **Gemini 1.5 Flash**.
* Recherche sémantique dans une base vectorielle **AlloyDB** pour trouver les produits pertinents.
* Génération d’une réponse argumentée par Gemini avec les recommandations.
* Retour JSON contenant un texte descriptif et une liste de produits pertinents.

---

## 🔧 Prérequis

* Python 3.10 ou supérieur
* Accès aux services suivants :

  * Google Cloud Secret Manager
  * AlloyDB (base de données PostgreSQL compatible avec les vecteurs)
  * Gemini (via l’API Generative AI de Google)
* Clés API et IAM correctement configurés pour y accéder (authentification par service account recommandée)

---

## ⚙️ Variables d’environnement

| Variable                | Description                                                             |
| ----------------------- | ----------------------------------------------------------------------- |
| `PROJECT_ID`            | ID du projet GCP                                                        |
| `REGION`                | Région GCP dans laquelle se trouve l’instance AlloyDB                   |
| `ALLOYDB_DATABASE_NAME` | Nom de la base de données AlloyDB                                       |
| `ALLOYDB_TABLE_NAME`    | Nom de la table contenant les embeddings vectoriels                     |
| `ALLOYDB_CLUSTER_NAME`  | Nom du cluster AlloyDB                                                  |
| `ALLOYDB_INSTANCE_NAME` | Nom de l’instance AlloyDB                                               |
| `ALLOYDB_SECRET_NAME`   | Nom du secret GCP contenant le mot de passe de l’utilisateur PostgreSQL |

ℹ️ Le mot de passe de la base est récupéré dynamiquement via **Secret Manager**.

---

## 📦 Installation

1. **Cloner le projet** :

```bash
git clone https://github.com/votre-organisation/votre-projet.git
cd votre-projet
```

2. **Créer un environnement virtuel et installer les dépendances** :

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## 🧪 Lancement de l’application

```bash
export PROJECT_ID=<votre-projet>
export REGION=<votre-région>
export ALLOYDB_DATABASE_NAME=<nom-db>
export ALLOYDB_TABLE_NAME=<nom-table>
export ALLOYDB_CLUSTER_NAME=<nom-cluster>
export ALLOYDB_INSTANCE_NAME=<nom-instance>
export ALLOYDB_SECRET_NAME=<nom-secret>

python app.py
```

Le service démarre sur `http://0.0.0.0:8080`.

---

## 🛠️ Appel de l’API

**Méthode HTTP :** `POST`
**Endpoint :** `/`
**Corps JSON attendu :**

```json
{
  "image": "https://exemple.com/image.jpg",
  "message": "Je cherche des objets dans un style industriel"
}
```

**Réponse :**

```json
{
  "content": "Votre pièce semble de style industriel minimaliste... Voici nos suggestions : ... [ID1], [ID2], [ID3]"
}
```

---

## 📝 Remarques

* L'application fonctionne avec **Gemini Flash** pour l’analyse d’image et **Gemini Pro** pour la recommandation finale.
* Les vecteurs sont recherchés dans une table AlloyDB contenant des embeddings de produits.
* L’authentification est basée sur les permissions IAM de votre environnement GCP.
