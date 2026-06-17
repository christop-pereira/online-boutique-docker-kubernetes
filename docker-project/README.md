## Documentation Docker

## Prérequis

- Ajouter l'entrée suivante dans votre fichier hosts :
  - Windows : `C:\Windows\System32\drivers\etc\hosts`
  - Linux/Mac : `/etc/hosts`

```bash
127.0.0.1   boutique.local
```

- Modifier/Créer le fichier .env avec la variable :
```bash
VIRTUAL_HOST: "boutique.local"
```

---

## Lancement

Builder et lancer tous les services :

```bash
docker compose up -d
```

Vérifier que tout tourne :

```bash
docker compose ps
```

Arrêter les services :

```bash
docker compose down
```

---

## Accès

L'application est accessible via le navigateur à l'adresse :

- HTTP : http://boutique.local
- HTTPS : https://boutique.local

Le certificat étant auto-signé, le navigateur affichera un avertissement de sécurité.
- Cliquer sur **Avancé** → **Continuer vers boutique.local**

---

## Explications

### Networks

Trois réseaux sont définis pour isoler les services :

- `frontend-net` relie le frontend aux services qu'il appelle directement
- `backend-net` relie les services internes entre eux et avec Redis
- `proxy-net` relie le reverse proxy au frontend uniquement

Le `backend-net` est configuré avec `internal: true` ce qui empêche les services internes d'accéder à internet, limitant la surface d'attaque.

### Reverse Proxy

Un reverse proxy **nginx-proxy** est configuré pour gérer le trafic entrant :

- Écoute sur les ports 80 (HTTP) et 443 (HTTPS)
- Redirige automatiquement le trafic vers le frontend
- Le certificat SSL auto-signé est généré automatiquement par **self-signed-proxy-companion**

### Images Docker

Chaque service suit les bonnes pratiques suivantes :

- **Multi-stage build** sépare la compilation de l'exécution pour réduire la taille des images
- **Images légères** utilisation de variantes `slim` ou `alpine` pour le runtime
- **Utilisateurs non-root** chaque conteneur tourne avec un utilisateur dédié sans privilèges

---

## Troubleshooting

### Voir les logs d'un service

```bash
docker compose logs <nom_du_service>
```

### Un service ne démarre pas

```bash
docker compose ps
```

### Le site n'est pas accessible sur https://boutique.local

Vérifier que l'entrée `127.0.0.1 boutique.local` est bien présente dans le fichier hosts.

Vérifier que nginx-proxy tourne correctement :

```bash
docker compose logs nginx-proxy
docker compose logs self-signed-companion
```

### Supprimer tous les conteneurs et volumes

```bash
docker compose down -v
```