# YourMedia-Infrastructure

Ce dépôt contient l'infrastructure as code pour le projet YourMédia de migration vers le cloud. Le code Terraform dans ce dépôt permet de déployer l'ensemble de l'infrastructure nécessaire pour l'application de gestion de contenu média.

## Objectif du projet

YourMédia est une startup spécialisée dans la gestion de contenu média avec une application mobile React Native connectée à une API Java Spring Boot. Ce projet vise à migrer l'infrastructure existante vers le cloud pour résoudre les problèmes de capacité des serveurs locaux face à la croissance du nombre d'utilisateurs.

## Architecture cible

- **Machine virtuelle** : Instance EC2 avec Java et Tomcat pour l'API Spring Boot
- **Base de données** : MySQL as a service (RDS)
- **Stockage média** : Service S3 pour le stockage des médias uploadés
- **Monitoring** : Prometheus/Grafana pour la supervision
- **CI/CD** : Automatisation du build/test et déploiement

## Structure du dépôt

```
yourmedia-infrastructure/
├── README.md
├── main.tf                 # Point d'entrée principal
├── variables.tf            # Variables globales
├── outputs.tf              # Outputs globaux
├── versions.tf             # Contraintes de versions
├── modules/
│   ├── ec2/                # Module pour la VM (EC2)
│   ├── s3/                 # Module pour le stockage média (S3)
│   ├── rds/                # Module pour la base de données (RDS)
│   └── networking/         # Module pour la configuration réseau
└── environments/
    ├── local/              # Configuration pour LocalStack (dev)
    └── aws-free-tier/      # Configuration pour AWS (prod)
```

## Approche LocalStack vs AWS

Ce projet utilise deux environnements distincts :

- **Branche `dev`** : Développement et tests avec LocalStack, sans coûts
- **Branche `main`** : Déploiement sur AWS (niveau gratuit quand possible)

Cette approche permet de développer et tester l'infrastructure sans engager de coûts, puis de la déployer sur AWS une fois validée.

## Prérequis

- **Terraform** (version >= 1.0.0)
- **AWS CLI** (pour le déploiement AWS)
- **Podman** (pour exécuter LocalStack et les conteneurs)
- **LocalStack** (pour le développement local)

## Installation et configuration

### Configuration pour le développement local (LocalStack)

1. Installez LocalStack :
   ```bash
   podman pull localstack/localstack
   podman run -d --name localstack -p 4566:4566 localstack/localstack
   ```

2. Configurez Terraform pour utiliser LocalStack :
   ```bash
   git checkout dev
   cp environments/local/terraform.tfvars terraform.tfvars
   terraform init
   terraform plan
   terraform apply
   ```

### Configuration pour AWS

1. Configurez vos identifiants AWS :
   ```bash
   aws configure
   ```

2. Déployez sur AWS :
   ```bash
   git checkout main
   cp environments/aws-free-tier/terraform.tfvars terraform.tfvars
   terraform init
   terraform plan
   terraform apply
   ```

## Modules

### Module EC2

Ce module déploie une instance EC2 pour héberger l'API Java Spring Boot avec les caractéristiques suivantes :
- Instance t2.micro (éligible au niveau gratuit)
- Installation automatique de Java et Tomcat
- Configuration de sécurité optimisée

### Module S3

Ce module configure un bucket S3 pour le stockage des médias avec :
- Configuration CORS pour permettre les uploads depuis l'application mobile
- Politique de cycle de vie pour optimiser les coûts
- Sécurité et encryption des données

### Module RDS

Ce module met en place une base de données MySQL (RDS) avec :
- Instance db.t3.micro (éligible au niveau gratuit)
- Configuration de sécurité et performance
- Backups automatiques

### Module Networking

Ce module configure le réseau et la sécurité avec :
- VPC dédié avec sous-réseaux publics et privés
- Groupes de sécurité pour contrôler l'accès
- Configuration réseau optimisée

## Workflow de développement

1. Créez une branche de fonctionnalité à partir de `dev` :
   ```bash
   git checkout dev
   git pull
   git checkout -b feature/nom-de-la-fonctionnalite
   ```

2. Développez et testez avec LocalStack

3. Soumettez une Pull Request vers `dev`

4. Après validation, créez une branche de promotion vers `main` :
   ```bash
   git checkout -b promote/batch-fonctionnalites
   ```

5. Adaptez la configuration pour AWS et soumettez une PR vers `main`

## Contribution

Les contributions sont les bienvenues ! Veuillez suivre ces étapes :
1. Créez une issue décrivant la fonctionnalité ou le bug
2. Créez une branche de fonctionnalité à partir de `dev`
3. Soumettez une Pull Request avec une description détaillée

## Licence

Ce projet est sous licence [MIT](LICENSE).
