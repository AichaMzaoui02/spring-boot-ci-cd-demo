
# Spring Boot CI/CD Demo

Projet minimal Spring Boot avec **API REST**, **Docker** et **GitHub Actions** pour CI/CD.

## Outils nécessaires
- **Java 17** (Temurin/OpenJDK)
- **Maven** (3.9+)
- **Docker** (24+)
- **Git** et un compte **GitHub** si tu utilises GitHub Actions

## Lancer localement
```bash
mvn spring-boot:run
# puis dans un autre terminal
curl http://localhost:8080/api/hello
```

## Build JAR
```bash
mvn -B -ntp clean package
ls target/*.jar
```

## Docker (local)
```bash
docker build -t spring-boot-ci-cd-demo:local .
docker run --rm -p 8080:8080 spring-boot-ci-cd-demo:local
```

## CI (GitHub Actions)
Le workflow **CI** construit, teste et pousse l'image vers **GitHub Container Registry (GHCR)**.
- Branches concernées: `main` (push) et toutes les `pull_request`
- Tags d'image:
  - `ghcr.io/<owner>/spring-boot-ci-cd-demo:<commit-sha>`
  - `ghcr.io/<owner>/spring-boot-ci-cd-demo:latest`

> Assure-toi que **Actions** est activé dans ton dépôt et que l'autorisation **packages: write** est accordée.

## CD (déploiement)
Un workflow **CD** (optionnel) déclenché sur les **tags** `v*.*.*` se connecte à une **VM** via SSH et lance un `docker compose`.

### Secrets nécessaires pour CD
- `VM_HOST` : adresse IP ou DNS de la VM
- `VM_USER` : utilisateur SSH
- `SSH_PRIVATE_KEY` : clé privée SSH (format PEM, sans passphrase)

## Qualité et tests
- **Tests** via JUnit & Spring Test (MockMvc)
- **Couverture** via **JaCoCo** (rapport `target/site/jacoco/index.html`)

## Personnalisation
- Changer le port dans `src/main/resources/application.yml`
- Ajouter des endpoints dans `HelloController`
- Ajouter des profils (`SPRING_PROFILES_ACTIVE`) pour `dev`, `test`, `prod`

## Structure du projet
```
src/
├── main/
│   ├── java/com/example/demo/
│   │   ├── Application.java
│   │   └── HelloController.java
│   └── resources/
│       └── application.yml
├── test/java/com/example/demo/
│   └── HelloControllerTest.java
Dockerfile
pom.xml
.github/workflows/ci.yml
.github/workflows/cd.yml
```

## Déploiement alternatif (Kubernetes)
Tu peux créer des manifestes `k8s/deployment.yaml` et `service.yaml` puis remplacer l'image par celle poussée dans GHCR et les appliquer avec `kubectl` depuis un pipeline.

---

**Astuces CI/CD**
- Taguer les images avec **SHA** et **version** (`v1.0.0`)
- Utiliser des **secrets** pour les credentials (GH Secrets)
- Exposer un **healthcheck** (`/actuator/health`) pour probes en prod
- Mettre en place **SonarQube** pour un Quality Gate (optionnel)

Bon dev ! 🚀
