# Amazon EKS Application Deployment using AWS Fargate and ALB Ingress

## Project Overview

This project demonstrates the deployment of a containerized application on **Amazon Elastic Kubernetes Service (Amazon EKS)** using **AWS Fargate** for serverless compute. The application is exposed to the internet through an **Application Load Balancer (ALB)** automatically provisioned by the **AWS Load Balancer Controller** using Kubernetes Ingress.

To securely grant AWS permissions to Kubernetes workloads, the project implements **IAM Roles for Service Accounts (IRSA)** with an **IAM OIDC Provider**, eliminating the need for long-term AWS access keys.

## Technologies Used

| Category | Technologies |
|----------|--------------|
| **Cloud** | Amazon EKS, AWS Fargate, AWS IAM, IAM OIDC Provider, Application Load Balancer (ALB) |
| **Kubernetes** | Deployment, Service, Ingress, ServiceAccount, Namespace |
| **Tools** | AWS CLI, kubectl, eksctl, Helm |

---

## Key Features

- Provisioned a managed **Amazon EKS** cluster using **AWS Fargate**.
- Configured **IAM OIDC Provider** and **IAM Roles for Service Accounts (IRSA)** for secure AWS authentication.
- Installed and configured the **AWS Load Balancer Controller** using **Helm**.
- Deployed the application using Kubernetes **Deployment**, **Service**, and **Ingress** resources.
- Automatically provisioned an **Application Load Balancer (ALB)** through Kubernetes Ingress.
- Implemented a fully serverless Kubernetes deployment without managing EC2 worker nodes.

---

## Project Workflow

1. Provision an Amazon EKS cluster using AWS Fargate.
2. Configure `kubectl` to communicate with the cluster.
3. Associate an IAM OIDC Provider with the EKS cluster.
4. Create an IAM Policy for the AWS Load Balancer Controller.
5. Configure IAM Roles for Service Accounts (IRSA).
6. Install the AWS Load Balancer Controller using Helm.
7. Deploy the application using Kubernetes Deployment.
8. Expose the application internally using a Kubernetes Service.
9. Create a Kubernetes Ingress resource.
10. Automatically provision an AWS Application Load Balancer (ALB).
11. Access the application using the ALB DNS endpoint.

---

## Project Outcome

Successfully deployed a containerized application on **Amazon EKS** using **AWS Fargate**, implemented secure AWS authentication through **IAM OIDC Provider** and **IAM Roles for Service Accounts (IRSA)**, and exposed the application externally using **Kubernetes Ingress** with an automatically provisioned **AWS Application Load Balancer (ALB)**.

---

## Application

The application was successfully deployed on **Amazon EKS** and was accessible through the **AWS Application Load Balancer**.

---

## Author

**Chandralekha KV**

BCA Graduate | Aspiring Cloud & DevOps Engineer

GitHub: https://github.com/Chandralekha-kv  
LinkedIn: https://www.linkedin.com/in/chandralekha-kv  
Email: kvpchandralekha224@gmail.com
