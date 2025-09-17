# Final DevOps Project: AWS Infrastructure with Terraform

## 🎯 Мета

Розгорнути продакшн-готову інфраструктуру **AWS + Kubernetes (EKS)** із підтримкою CI/CD та моніторингом.  
Усі сервіси керуються **Terraform** і автоматизовані через модулі.

---

## ⚙️ Компоненти

- **VPC** – власна мережа з публічними й приватними підмережами.
- **EKS** – Kubernetes кластер (AWS EKS).
- **RDS / Aurora** – база даних (гнучкий модуль з прапором `use_aurora`).
- **ECR** – реєстр контейнерів.
- **Jenkins** – CI/CD пайплайни (збірка образу, пуш у ECR).
- **Argo CD** – GitOps CD, деплой додатків у кластер.
- **Prometheus + Grafana** – моніторинг.
- **S3 + DynamoDB** – бекенд для Terraform state.

---

## 🏗 Структура репозиторію

#

Project/
├── main.tf
├── backend.tf
├── outputs.tf
│
├── modules/
│ ├── s3-backend/ # S3 + DynamoDB для state
│ ├── vpc/ # VPC + сабнети + маршрути
│ ├── ecr/ # AWS ECR репозиторій
│ ├── eks/ # EKS кластер + node group + CSI драйвер
│ ├── rds/ # RDS / Aurora з умовною логікою
│ ├── jenkins/ # Jenkins Helm release
│ └── argo_cd/ # ArgoCD Helm release + app-of-apps chart
│
├── charts/
│ └── django-app/ # Helm chart додатку (Deployment, Service, HPA)
│
└── Django/ # Сам додаток
├── app/
├── Dockerfile
├── Jenkinsfile
└── docker-compose.yaml

---

## 🚀 Розгортання

### 1. Підготовка середовища

```bash
terraform init
terraform plan
```

2. Розгортання інфраструктури

terraform apply -auto-approve

## Перевірка доступності

Jenkins
kubectl get all -n jenkins
kubectl port-forward svc/jenkins 8080:8080 -n jenkins

👉 http://localhost:8080

## Argo CD

kubectl get all -n argocd
kubectl port-forward svc/argocd-server 8081:443 -n argocd

👉 https://localhost:8081

## Моніторинг (Grafana)

kubectl get all -n monitoring
kubectl port-forward svc/grafana 3000:80 -n monitoring

👉 http://localhost:3000

### CI/CD

## Jenkins job: збирає Docker-образ Django та пушить у ECR.

## Argo CD: автоматично синхронізує репозиторій і оновлює деплой у кластері.

kubectl get pods -n default
kubectl get svc -n default

## 🧹 Видалення ресурсів

### Щоб уникнути витрат у AWS:

terraform destroy -auto-approve

❗ Це також видалить S3-бакет і DynamoDB таблицю для бекенду Terraform.

📊 Демонстрація

terraform apply → інфраструктура готова.

Jenkins → запускає пайплайн, пушить образ у ECR.

Argo CD → бачить зміни та оновлює деплой.

Grafana → відображає метрики з Prometheus.
