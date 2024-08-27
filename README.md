# Sock Shop Microservices Application on Kubernetes

## Overview

This project deploys the Sock Shop microservices demo application on a Kubernetes cluster using Infrastructure as Code (IaC) principles. It includes configurations for all necessary components, including the application services, databases, monitoring, alerting, and security measures.

## Components

1. Microservices:
   - Front-end, Catalogue, Carts, Orders, Shipping, Payment, User

2. Databases:
   - MongoDB (for Carts, Orders, and User services)
   - MySQL (for Catalogue service)

3. Supporting Services:
   - RabbitMQ
   - Redis (Session DB)

4. Monitoring and Alerting:
   - Prometheus
   - Alertmanager
   - Grafana

5. Ingress:
   - NGINX Ingress Controller

6. SSL/TLS:
   - cert-manager with Let's Encrypt

7. Infrastructure Provisioning:
   - Terraform (for AWS EKS)

8. Configuration Management:
   - Ansible

9. CI/CD:
   - GitLab CI

## Prerequisites

- AWS account with appropriate permissions
- Terraform installed
- Ansible installed
- kubectl installed and configured
- GitLab account (for CI/CD)
- Domain name for HTTPS setup

## Infrastructure Provisioning

1. Navigate to the project directory:

`cd sock-shop-k8s`

2. Initialize Terraform:

`terraform init`

3. Review and apply the Terraform configuration:

`terraform plan`
`terraform apply`

This will create an EKS cluster and necessary networking components in AWS.

## Application Deployment

1. Update the `inventory` file with your EKS cluster details.

2. Encrypt sensitive information:

`ansible-vault create secrets.yml`

Enter the Slack webhook URL and any other sensitive data.

3. Deploy the application using Ansible:

`ansible-playbook -i inventory deploy.yml --ask-vault-pass`

## Monitoring and Alerting Setup

Prometheus, Alertmanager, and Grafana are automatically deployed as part of the Ansible playbook. To access:

1. For Prometheus:

`kubectl port-forward svc/prometheus -n sock-shop 9090:9090`

Access at `http://localhost:9090`

2. For Grafana:

`kubectl port-forward svc/grafana -n sock-shop 3000:3000`

Access at `http://localhost:3000` (default credentials: admin/admin)

3. Configure Grafana to use Prometheus as a data source and import dashboards as needed.

## Security Measures

1. Network security is managed through AWS Security Groups, defined in the Terraform configuration.
2. Sensitive information is encrypted using Ansible Vault.
3. HTTPS is enabled using Let's Encrypt certificates, managed by cert-manager.

## CI/CD Pipeline

The CI/CD pipeline is configured using GitLab CI. The `.gitlab-ci.yml` file in the repository root defines the pipeline:

1. Terraform stage: Provisions or updates infrastructure
2. Deploy stage: Deploys the application using Ansible

To use:
1. Push your code to a GitLab repository.
2. Configure necessary CI/CD variables in GitLab (AWS credentials, Ansible Vault password).
3. The pipeline will run automatically on push to the main branch.

## Accessing the Application

After successful deployment, access the application at: `https://your-domain.com`

## Troubleshooting

1. Check pod status:

`kubectl get pods -n sock-shop`

2. View logs of a specific pod:

`kubectl logs <pod-name> -n sock-shop`

3. Check ingress status:

`kubectl get ingress -n sock-shop`

4. For issues with Terraform or Ansible, check their respective logs during execution.

## Cleanup

To remove all resources:

1. Destroy Terraform-managed resources:

`terraform destroy`

2. Delete the Kubernetes namespace:

`kubectl delete namespace sock-shop`

