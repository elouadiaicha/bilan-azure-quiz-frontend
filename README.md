# Azure Quiz - Frontend

Frontend Angular de l'application **Azure Quiz**, permettant de réviser les certifications Microsoft Azure à travers des modules et des examens blancs.

L'application est déployée dans un environnement **non-production Azure** et communique avec une API REST Spring Boot hébergée sur Azure App Service.

## URL non-production

Application :

https://gentle-moss-091704803.7.azurestaticapps.net

Le frontend est hébergé sur **Azure Static Web Apps**.

## Stack technique

- Angular 22
- Angular Material
- ngx-translate (fr/en)
- TypeScript
- Vitest
- ESLint (`angular-eslint`)
- Prettier
- Husky
- GitHub Actions
- Azure Static Web Apps

## Architecture applicative

```text
                    Internet
                       |
                       v
          +---------------------------+
          | Azure Static Web Apps     |
          | Frontend Angular          |
          +---------------------------+
                       |
                       | HTTPS / REST
                       | API Key + CORS
                       v
          +---------------------------+
          | Azure App Service         |
          | Backend Spring Boot       |
          +---------------------------+
                       |
             +---------+---------+
             |         |         |
             v         v         v
        PostgreSQL    Redis    Storage
```

Le navigateur accède uniquement au frontend Angular.

Le frontend communique ensuite avec l'API REST du backend Spring Boot.

Le backend est protégé au niveau applicatif par :

- une clé API partagée ;
- une politique CORS limitée à l'origine du frontend Azure Static Web Apps.

L'infrastructure Azure est provisionnée séparément avec Terraform dans le dépôt d'infrastructure.

## Structure du projet

- `src/app/core` : modèles et services communs.
- `src/app/features/certifications` : sélection des certifications.
- `src/app/features/modules` : affichage des modules et lancement des examens.
- `src/app/features/quiz` : déroulement des questions.
- `src/app/features/results` : affichage des résultats.
- `src/environments` : configuration des environnements Angular.
- `.github/workflows` : pipeline CI/CD.
- `.husky` : hooks Git de contrôle qualité.

## Exécution locale

### Prérequis

- Node.js 22+
- npm
- backend Azure Quiz lancé localement sur `http://localhost:8080`

Installation :

```bash
npm install
```

Démarrage :

```bash
npm start
```

L'application est alors disponible sur :

```text
http://localhost:4200
```

L'environnement de développement utilise :

```text
http://localhost:8080/api
```

comme URL du backend.

## Tests et qualité

Les commandes principales sont :

```bash
npm test
npm run test:coverage
npm run lint
npm run format:check
```

### Pre-commit

Le projet utilise **Husky**.

Avant un commit, le hook `.husky/pre-commit` exécute automatiquement :

```bash
npm run lint
npm run test
```

Un commit est bloqué si le lint ou les tests échouent.

## Build de production

```bash
npm run build:prod
```

Les fichiers statiques sont générés dans :

```text
dist/azure-quiz-frontend/browser
```

Ce répertoire est ensuite déployé sur Azure Static Web Apps.

## CI/CD

Le déploiement est automatisé avec **GitHub Actions**.

Workflow :

```text
.github/workflows/swa-deploy.yml
```

Le pipeline réalise notamment les opérations suivantes :

1. récupération du code ;
2. authentification auprès d'Azure avec **OIDC** ;
3. identification des ressources Azure nécessaires ;
4. récupération de la configuration du backend ;
5. injection de l'URL de l'API et de la clé API lors du build ;
6. build Angular de production ;
7. déploiement des artefacts sur Azure Static Web Apps.

Les identifiants Azure ne sont pas stockés directement dans le code.

L'authentification GitHub Actions → Azure utilise une **Federated Credential OIDC**.

## Environnements de déploiement

Les branches de développement peuvent être déployées dans des environnements de preview Azure Static Web Apps.

La branche :

```text
main
```

alimente l'environnement non-production principal.

## Sécurité et gouvernance

Le dépôt met en œuvre plusieurs mécanismes de qualité et de sécurité :

- commits signés et vérifiés (`Verified`) ;
- `CODEOWNERS` ;
- Dependabot pour les dépendances npm et GitHub Actions ;
- Husky pour les contrôles pre-commit ;
- ESLint ;
- Prettier ;
- tests automatisés ;
- authentification Azure par OIDC.

Aucun secret applicatif réel ne doit être committé dans le dépôt.

## Gestion des dépendances

Dependabot est configuré dans :

```text
.github/dependabot.yml
```

Il surveille :

- les dépendances npm ;
- les GitHub Actions utilisées par les workflows.

## Dépôts du projet

Le projet est séparé en trois dépôts :

```text
bilan-azure-quiz-frontend
bilan-azure-quiz-backend
bilan-azure-quiz-terraform
```

Ce dépôt contient uniquement le frontend Angular.

La création et la configuration des ressources Azure sont gérées dans le dépôt Terraform.