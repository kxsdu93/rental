# Rental DevOps — checkpoint 00

Ce monorepo est le socle d'une formation DevOps pratique destinée à des
étudiants de master. Le checkpoint 00 fournit deux services autonomes,
testables et conteneurisés, mais laisse volontairement à construire la
communication distribuée, la persistance et le déploiement Kubernetes.

## Architecture actuelle

| Service | Technologie | Port | Responsabilité |
|---|---|---:|---|
| `car-rental-service` | Java 17, Spring Boot, Gradle | 8080 | Gestion en mémoire des voitures et locations |
| `pricing-service` | Python 3.12, FastAPI | 8000 | Calcul autonome des tarifs |

Les deux services partagent un réseau Docker. Ils ne communiquent pas encore
entre eux.

## Prérequis

- Git ;
- JDK 17 ou supérieur ;
- Python 3.12 ou une version compatible ;
- Docker avec Docker Compose.

Vérification rapide :

```bash
python scripts/course.py prerequisites
```

## Installation des dépendances Python

```bash
cd apps/pricing-service
python -m venv .venv
```

Activation sous Linux ou macOS :

```bash
source .venv/bin/activate
```

Activation sous PowerShell :

```powershell
.venv\Scripts\Activate.ps1
```

Puis :

```bash
python -m pip install -r requirements-dev.txt
cd ../..
```

## Tests

Depuis la racine :

```bash
python scripts/course.py test
```

Cette commande exécute les tests Gradle puis les tests Pytest.
Pour diagnostiquer séparément un service, utiliser `test-java` ou
`test-python` à la place de `test`.

Pour lancer les tests puis la qualité (Checkstyle et Ruff) comme dans la CI :

```bash
python scripts/course.py verify
```

La commande s'arrête au premier échec. On peut aussi lancer `lint`,
`lint-java` ou `lint-python` seuls.

## Exécution avec Docker Compose

```bash
python scripts/course.py compose-up
python scripts/course.py smoke
```

Endpoints utiles :

```text
GET  http://localhost:8080/health
GET  http://localhost:8080/cars
GET  http://localhost:8000/health
POST http://localhost:8000/quotes
GET  http://localhost:8000/docs
```

Arrêt :

```bash
python scripts/course.py compose-down
```

## Organisation

```text
apps/                 Services applicatifs
archive/              Notes historiques conservées
course/               Futurs supports Quarto
docs/                 État de référence et checkpoints
infra/                Future infrastructure de test
scripts/              Commandes portables du cours
tests/                Futures suites de tests système
.github/workflows/     CI minimale à améliorer pendant la formation
```

## Limites intentionnelles du checkpoint

- stockage Java uniquement en mémoire ;
- aucune communication entre les services ;
- aucune base PostgreSQL ni migration ;
- aucun test de contrat ou test E2E ;
- aucun manifeste Kubernetes ou cluster Kind ;
- workflows CI encore redondants et non durcis.

Voir [la fiche du checkpoint](docs/CHECKPOINT-00.md) pour les objectifs et les
critères de validation.

L'état historique antérieur à la refonte reste disponible sous le tag
`legacy/v1-microservices`.
