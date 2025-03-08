# **Explication du fichier `buildspec.yml`**

Le fichier `buildspec.yml` est utilisé pour configurer les étapes de construction (build) dans **AWS CodeBuild**. Il définit les phases du processus de construction, les commandes à exécuter, et les artefacts à générer. Voici une explication détaillée de chaque section et commande.

---

## **Structure de base d'un `buildspec.yml`**

```yaml
version: 0.2

phases:
  install:
    commands:
      - echo "Installation des dépendances..."
  build:
    commands:
      - echo "Construction du projet..."
  post_build:
    commands:
      - echo "Nettoyage et finalisation..."

artifacts:
  files:
    - '**/*'
```

---

## **1. `version: 0.2`**

- **Description** : Spécifie la version du schéma `buildspec.yml` utilisé.
- **Valeurs possibles** : `0.1`, `0.2`.
- **Explication** : La version `0.2` est la plus récente et prend en charge des fonctionnalités supplémentaires comme les variables d'environnement et les phases personnalisées.

---

## **2. `phases`**

- **Description** : Définit les différentes phases du processus de construction.
- **Phases principales** :
  - **`install`** : Phase d'installation des dépendances.
  - **`pre_build`** : Phase de pré-construction (setup avant le build).
  - **`build`** : Phase de construction (compilation, tests, etc.).
  - **`post_build`** : Phase de post-construction (nettoyage, packaging, etc.).

---

### **2.1 Phase `install`**

- **Objectif** : Installer les dépendances nécessaires pour le projet.
- **Commandes typiques** :
  - Installation de packages avec `pip`, `npm`, `yarn`, etc.
  - Configuration de l'environnement de construction.

#### **Exemple** :
```yaml
install:
  runtime-versions:
    python: 3.8
  commands:
    - echo "Installation des dépendances Python..."
    - pip install -r requirements.txt
```

- **`runtime-versions`** : Spécifie la version du runtime (ici, Python 3.8).
- **`commands`** : Liste des commandes à exécuter pendant cette phase.

---

### **2.2 Phase `pre_build`**

- **Objectif** : Préparer l'environnement avant la construction.
- **Commandes typiques** :
  - Configuration des variables d'environnement.
  - Exécution de scripts de préparation.

#### **Exemple** :
```yaml
pre_build:
  commands:
    - echo "Configuration de l'environnement..."
    - export ENV=production
```

---

### **2.3 Phase `build`**

- **Objectif** : Compiler le code, exécuter les tests et construire l'application.
- **Commandes typiques** :
  - Compilation du code source.
  - Exécution des tests unitaires ou d'intégration.
  - Construction des artefacts.

#### **Exemple** :
```yaml
build:
  commands:
    - echo "Exécution des tests..."
    - python -m unittest discover
    - echo "Construction de l'application..."
```

---

### **2.4 Phase `post_build`**

- **Objectif** : Finaliser la construction et préparer les artefacts pour le déploiement.
- **Commandes typiques** :
  - Packaging de l'application (ex : création d'un fichier `.zip`).
  - Envoi des artefacts vers un stockage (ex : S3).

#### **Exemple** :
```yaml
post_build:
  commands:
    - echo "Création de l'artefact..."
    - zip -r application.zip .
```

---

## **3. `artifacts`**

- **Objectif** : Définir les fichiers à inclure dans les artefacts de construction.
- **Utilisation** : Les artefacts sont les fichiers de sortie du processus de construction (ex : fichiers binaires, packages, etc.).

#### **Exemple** :
```yaml
artifacts:
  files:
    - '**/*'            # Inclut tous les fichiers
    - 'dist/*.zip'      # Inclut uniquement les fichiers .zip dans le dossier dist
  discard-paths: yes    # Ignore les chemins des fichiers dans l'artefact
```

- **`files`** : Liste des fichiers ou patterns à inclure.
  - `'**/*'` : Inclut tous les fichiers et dossiers.
  - `'dist/*.zip'` : Inclut uniquement les fichiers `.zip` dans le dossier `dist`.
- **`discard-paths`** : Si `yes`, les chemins des fichiers sont ignorés dans l'artefact final.

---

## **4. Variables d'environnement**

- **Objectif** : Définir des variables d'environnement pour le processus de construction.
- **Utilisation** : Les variables peuvent être utilisées dans les commandes.

#### **Exemple** :
```yaml
env:
  variables:
    ENV: "production"
    AWS_REGION: "us-east-1"
  parameter-store:
    DB_PASSWORD: "/myapp/db_password"
```

- **`variables`** : Variables statiques définies directement dans le fichier.
- **`parameter-store`** : Variables récupérées depuis AWS Systems Manager Parameter Store.

---

## **5. Cache (Optionnel)**

- **Objectif** : Utiliser un cache pour accélérer les builds ultérieurs.
- **Utilisation** : Le cache peut stocker des dépendances ou des fichiers intermédiaires.

#### **Exemple** :
```yaml
cache:
  paths:
    - '/root/.m2/**/*'   # Cache pour Maven
    - '/usr/local/lib/**/*' # Cache pour les librairies Python
```

---

## **Exemple complet de `buildspec.yml`**

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.8
    commands:
      - echo "Installation des dépendances..."
      - pip install -r requirements.txt

  pre_build:
    commands:
      - echo "Configuration de l'environnement..."
      - export ENV=production

  build:
    commands:
      - echo "Exécution des tests..."
      - python -m unittest discover
      - echo "Construction de l'application..."

  post_build:
    commands:
      - echo "Création de l'artefact..."
      - zip -r application.zip .

artifacts:
  files:
    - 'application.zip'
  discard-paths: yes
```

---

## **Résumé des commandes et syntaxe**

| **Section**          | **Commande/Syntaxe**                     | **Description**                                                                 |
|-----------------------|------------------------------------------|---------------------------------------------------------------------------------|
| `version`             | `0.2`                                    | Version du schéma `buildspec.yml`.                                              |
| `phases`              | `install`, `pre_build`, `build`, `post_build` | Phases du processus de construction.                                            |
| `runtime-versions`    | `python: 3.8`                            | Définit la version du runtime (ex : Python, Node.js).                           |
| `commands`            | `pip install -r requirements.txt`        | Liste des commandes à exécuter dans une phase.                                  |
| `artifacts`           | `files: ['**/*']`                        | Liste des fichiers à inclure dans les artefacts.                                |
| `discard-paths`       | `yes`/`no`                               | Ignore ou conserve les chemins des fichiers dans les artefacts.                 |
| `env`                 | `variables`, `parameter-store`           | Définit les variables d'environnement.                                         |
| `cache`               | `paths: ['/root/.m2/**/*']`              | Configure le cache pour accélérer les builds ultérieurs.                        |

