 IAC-VProfile – Infrastructure AWS avec Terraform & GitHub Actions

Ce projet provisionne l’infrastructure cloud du projet **VProfile** sur **AWS** grâce à **Terraform** et un pipeline GitHub Actions. Il déploie un cluster Kubernetes (EKS), configure le backend Terraform sur S3, et installe automatiquement un Ingress Controller NGINX.

---

## 📁 Structure du projet

```

iac-vprofile/
├── .github/workflows/
│   └── terraform.yml          # Workflow GitHub Actions (CI/CD IaC)
├── terraform/
│   ├── main.tf                # Ressources principales (EKS, IAM, etc.)
│   ├── variables.tf           # Variables d’entrée
│   ├── outputs.tf             # Valeurs exportées
│   ├── terraform.tf           # Backend config & provider
│   ├── vpc.tf                 # Réseau (VPC, Subnets)
│   └── eks-cluster.tf         # Cluster EKS
├── .gitignore
└── README.md

````

---

## 🎯 Objectif

Automatiser le déploiement d’un environnement Kubernetes sur AWS :

- Création du **VPC**, sous-réseaux, internet gateway
- Provisionnement d’un **cluster Amazon EKS**
- Configuration de `kubeconfig` pour accéder au cluster
- Déploiement d’un **Ingress Controller** dans le cluster
- Stockage de l’état Terraform dans un **bucket S3**

---

## ⚙️ Technologies utilisées

| Outil               | Rôle                                  |
|---------------------|----------------------------------------|
| Terraform           | Infrastructure as Code (IaC)          |
| GitHub Actions      | Automatisation CI/CD Terraform         |
| Amazon EKS          | Orchestration Kubernetes               |
| Amazon S3           | Backend Terraform (tfstate)            |
| AWS IAM             | Gestion des identités et permissions   |
| Ingress NGINX       | Contrôleur de trafic HTTP              |

---

## 🔐 Secrets GitHub requis

Pour que le pipeline fonctionne correctement, tu dois définir les secrets suivants dans **Settings > Secrets > Actions** :

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `BUCKET_TF_STATE` (nom du bucket S3)

---

## ⚡ Déclenchement du pipeline

Le fichier `.github/workflows/terraform.yml` est déclenché :

```yaml
on:
  push:
    branches: [main, stage]
    paths: ["terraform/**"]
  pull_request:
    branches: [main]
    paths: ["terraform/**"]
````

✅ Il s’exécute uniquement lors de modifications dans le dossier `terraform/`.

---

## 🧪 Étapes du workflow `terraform.yml`

| Étape                  | Action effectuée                           |
| ---------------------- | ------------------------------------------ |
| ✅ `terraform init`     | Initialise Terraform + backend S3          |
| ✅ `terraform fmt`      | Vérifie la mise en forme du code           |
| ✅ `terraform validate` | Valide la syntaxe Terraform                |
| ✅ `terraform plan`     | Génère un plan d’exécution                 |
| ✅ `terraform apply`    | Applique le plan (si branche `main`)       |
| ✅ Configure AWS        | Configure `aws-cli` pour l’environnement   |
| ✅ Update kubeconfig    | Permet à `kubectl` d'accéder à EKS         |
| ✅ Deploy Ingress NGINX | Installe le controller via `kubectl apply` |

---

## 🔄 Exécution locale

Tu peux aussi exécuter Terraform manuellement :

```bash
cd terraform
terraform init -backend-config="bucket=<your-bucket-name>"
terraform plan
terraform apply
```

---

## 🧼 Nettoyage de l’environnement

```bash
terraform destroy
```

---

## 📌 Remarques

* Le `terraform apply` ne s’exécute **que sur la branche `main`**
* Le `kubeconfig` est mis à jour à partir d’AWS pour gérer le cluster avec `kubectl`
* L'Ingress Controller est automatiquement installé après déploiement

---

## 👤 Auteur

**Jouneid Guefif**
Projet DevOps – Infrastructure as Code avec Terraform, AWS, GitHub Actions

---

## 📎 Liens utiles

* [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
* [GitHub Actions for Terraform](https://github.com/hashicorp/setup-terraform)
* [Ingress NGINX AWS Deploy](https://kubernetes.github.io/ingress-nginx/deploy/#aws)

---

