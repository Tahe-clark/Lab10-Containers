# TODO – Lab 10 (Docker + Kubernetes)

Énoncé : `Conteneurs- Docker, Kubernetes.pdf` (suivre les sections dans l'ordre).
Le livrable est le **rapport** avec les captures Q1 à Q6. Les captures se mettent dans `docs/screenshots/` (q1.png, q2.png, …).

## Répartition

| | Clark | Mathys |
|---|---|---|
| Code | **1.1** serveur, **1.2** client, **2** Kubernetes | **1.3** Docker Compose (conteneurs, réseau, volume, healthcheck) |
| Captures | Q1, Q2, Q4, Q5, Q6 | Q3 |
| Rapport | Fournit ses captures (Q1, Q2, Q4, Q5, Q6) et relit | **Responsable du rapport** : rédaction et mise en forme complètes (Word) |
| Branche | `clark/docker-k8s` | `mathys/compose` |

Ordre : Clark termine d'abord les images (1.1 et 1.2) et ouvre sa PR. Mathys écrit son `docker-compose.yml` en parallèle, puis le teste une fois la PR de Clark fusionnée dans `main` (Compose construit les images à partir des Dockerfiles de Clark).

Règles : une branche par personne, une PR vers `main`, on ne pousse pas directement sur `main`. On ne commit jamais `node_modules`, `build/`, `dist/`, `.gradle/`.
Dans le rapport, la section « Répartition du travail » doit refléter ce que chacun a vraiment fait.

---

## TODO de Clark (parties 1.1, 1.2 et 2)

### 0. Avant de commencer
- [ ] Docker Desktop est lancé (`docker ps` répond sans erreur), Java 21 et Node 20 installés.
- [ ] Branche `clark/docker-k8s`.

### 1. Serveur (section 1.1 du PDF) — dossier `books-rest-api/`
- [ ] `Dockerfile` à la racine de `books-rest-api/` (PDF, lignes 1 à 12).
- [ ] Remplacer le contenu de `src/main/resources/application.properties` par celui du PDF (variables MYSQL_*).
- [ ] Construire l'image `books-rest-api:latest`, créer le réseau `mysql-network`, démarrer `mysqldb` puis `booksserver` (commandes du PDF).
- [ ] Vérifier : http://localhost:8080/swagger-ui.html
- [ ] **Q1** : `docker container ls` → `docs/screenshots/q1.png`.

Pièges connus :
- « gradlew: not found » dans le build : en Linux, un script du dossier courant ne s'appelle pas comme une commande du PATH. Trouve la bonne façon de l'appeler.
- `--network=host` peut poser problème sous Windows : essaie sans.
- Erreur avec `\r` ou « bad interpreter » : fins de ligne (le `.gitattributes` est là pour l'éviter ; refais un clone propre si besoin).

### 2. Client (section 1.2) — dossier `bookstore-client/`
- [ ] `Dockerfile` à la racine de `bookstore-client/` (PDF, lignes 1 à 18).
- [ ] `src/assets/` avec `env.template.json` et `env.json` (PDF).
- [ ] `entrypoint.sh` à la racine de `bookstore-client/` (PDF).
- [ ] Modifier `src/main.ts` (PDF, étape 3) et `BooksService` (`src/app/books/service/books-service.ts`, PDF, étape 4).
- [ ] Construire l'image `books-api-client:latest`, démarrer `booksclient` (commandes du PDF).
- [ ] http://localhost:4100 : la page charge (ajoute un livre via Swagger si la base est vide).
- [ ] **Q2** : `docker container ls` avec les 3 conteneurs → `docs/screenshots/q2.png`.
- [ ] **Avant de pousser** : `git status` doit être propre de tout fichier généré. Commit, push `clark/docker-k8s`, ouvrir la PR vers `main` et prévenir Mathys. (Tu peux ouvrir une première PR avec 1.1 et 1.2, puis une autre pour la partie 2.)
- [ ] Arrêter et supprimer tes conteneurs avant la suite (ports 8080 et 4100 occupés) : `docker container stop booksclient booksserver mysqldb` puis `docker container rm booksclient booksserver mysqldb`.

### 3. Kubernetes (section 2) — dossier `k8s/`
- [ ] `minikube start` (installer Minikube et kubectl si besoin, section 2.1).
- [ ] `mysql-deployment.yaml`, `mysql-volume.yaml`, `booksserver-deployment.yaml`, `booksclient-deployment.yaml` (PDF, section 2.2).
- [ ] Charger les 2 images dans Minikube, puis `kubectl apply -f .` → **Q4** (`q4.png`).
- [ ] `kubectl get deployments` → **Q5** (`q5.png`).
- [ ] Port-forward `booksserver` (8080) et `booksapp` (4100), vérifier http://localhost:4100, puis `kubectl get pods` → **Q6** (`q6.png`).
- [ ] Supprimer un pod `booksserver` (`kubectl delete pod <nom>`) et vérifier qu'un nouveau est créé (réplication).
- [ ] `kubectl delete -f .` pour nettoyer. Commit, push, PR.

---

## TODO de Mathys (partie 1.3 + rapport)

### Docker Compose (section 1.3)
- [ ] Branche `mathys/compose`.
- [ ] Créer le dossier `docker/` à la racine du dépôt (au-dessus de `books-rest-api` et `bookstore-client`) avec `docker-compose.yml` (contenu du PDF, lignes 1 à 55 — attention à l'indentation YAML).
- [ ] Une fois la PR de Clark fusionnée (`git pull` sur `main`) : dans `docker/`, `docker compose up -d --build`. Ouvrir http://localhost:4100.
- [ ] **Q3** : capture du résultat de la commande → `docs/screenshots/q3.png`.
- [ ] `docker compose down` pour nettoyer. Commit uniquement `docker-compose.yml` et la capture, push, PR vers `main`, prévenir Clark.
- [ ] Attention : arrête d'abord les conteneurs lancés à la main (ports 8080 et 4100), sinon Compose échoue.
- [ ] Être capable d'expliquer : à quoi sert `depends_on` avec le `healthcheck`, pourquoi `MYSQL_HOST: mysql`, ce que fait un volume nommé.

### Rapport (Word)
- [ ] Page de couverture comme les rapports précédents (noms, numéros étudiants, date, lien du dépôt GitHub).
- [ ] Captures **Q1 à Q6** dans l'ordre, chacune avec sa légende et la commande utilisée (Clark t'envoie q1, q2, q4, q5, q6 dans `docs/screenshots/`).
- [ ] Brève description du travail réalisé (avec tes mots) et étapes pour installer et exécuter le projet.
- [ ] Section « Répartition du travail » fidèle à ce que chacun a fait.
- [ ] Export en PDF et remise sur Brightspace.
