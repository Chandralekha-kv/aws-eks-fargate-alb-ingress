# Setup Guide

## Overview

This guide explains how to provision an Amazon EKS cluster using AWS Fargate, configure IAM Roles for Service Accounts (IRSA), install the AWS Load Balancer Controller, and deploy the 2048 application using Kubernetes Deployments, Services, and Ingress resources.

---

# Prerequisites

Ensure the following tools are installed and configured before starting.

## AWS CLI

The AWS Command Line Interface (AWS CLI) is used to create and manage AWS resources.

Configure your AWS credentials:

```bash
aws configure
```

---

## kubectl

`kubectl` is the Kubernetes command-line tool used to interact with the EKS cluster.

Verify installation:

```bash
kubectl version --client
```

---

## eksctl

`eksctl` simplifies the creation and management of Amazon EKS clusters.

Verify installation:

```bash
eksctl version
```

---

## Helm

Helm is the package manager for Kubernetes and is used to install the AWS Load Balancer Controller.

Verify installation:

```bash
helm version
```

---

# Step 1 – Create Amazon EKS Cluster

### Purpose

Create a managed Kubernetes cluster on Amazon EKS using AWS Fargate.

```bash
eksctl create cluster \
--name demo-cluster \
--region us-east-1 \
--fargate
```

Verify cluster creation:

```bash
kubectl get nodes
```

---

# Step 2 – Create a Fargate Profile

### Purpose

Create a Fargate profile for the `game-2048` namespace so Pods are scheduled on AWS Fargate.

```bash
eksctl create fargateprofile \
--cluster demo-cluster \
--region us-east-1 \
--name alb-sample-app \
--namespace game-2048
```

Verify:

```bash
eksctl get fargateprofile \
--cluster demo-cluster
```

---

# Step 3 – Configure IAM OIDC Provider

### Purpose

Associate the IAM OIDC provider with the EKS cluster to enable IAM Roles for Service Accounts (IRSA).

Export the cluster name:

```bash
export cluster_name=demo-cluster
```

Retrieve the OIDC ID:

```bash
oidc_id=$(aws eks describe-cluster \
--name $cluster_name \
--query "cluster.identity.oidc.issuer" \
--output text | cut -d '/' -f 5)
```

Check whether an OIDC provider already exists:

```bash
aws iam list-open-id-connect-providers | grep $oidc_id
```

If no provider exists, associate one:

```bash
eksctl utils associate-iam-oidc-provider \
--cluster $cluster_name \
--approve
```

---

# Step 4 – Create IAM Policy

### Purpose

Create an IAM policy that grants the AWS Load Balancer Controller permission to provision and manage AWS Application Load Balancers.

Download the IAM policy:

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

Create the IAM policy:

```bash
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
```

---

# Step 5 – Create IAM Service Account (IRSA)

### Purpose

Create a Kubernetes ServiceAccount and associate it with an IAM Role using IRSA.

```bash
eksctl create iamserviceaccount \
--cluster <cluster-name> \
--namespace kube-system \
--name aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn arn:aws:iam::<aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
--approve
```

Verify:

```bash
kubectl get serviceaccounts -n kube-system
```

---

# Step 6 – Install AWS Load Balancer Controller

### Purpose

Deploy the AWS Load Balancer Controller, which watches Kubernetes Ingress resources and automatically provisions AWS Application Load Balancers.

Add the Helm repository:

```bash
helm repo add eks https://aws.github.io/eks-charts
```

Update the repository:

```bash
helm repo update
```

Install the controller:

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=<cluster-name> \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller \
--set region=<aws-region> \
--set vpcId=<vpc-id>
```

Verify:

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

---

# Step 7 – Deploy the Application

### Purpose

Deploy the 2048 application to the EKS cluster.

```bash
kubectl apply -f manifests/deployment.yaml
```

Verify:

```bash
kubectl get pods -n game-2048
```

---

# Step 8 – Create a Kubernetes Service

### Purpose

Expose the application internally within the cluster.

```bash
kubectl apply -f manifests/service.yaml
```

Verify:

```bash
kubectl get svc -n game-2048
```

---

# Step 9 – Create an Ingress Resource

### Purpose

Expose the application externally using an AWS Application Load Balancer.

```bash
kubectl apply -f manifests/ingress.yaml
```

Verify:

```bash
kubectl get ingress -n game-2048
```

---

# Validation

Verify the deployment:

```bash
kubectl get pods -n game-2048

kubectl get svc -n game-2048

kubectl get ingress -n game-2048
```

Once the ALB has been provisioned, copy the ALB DNS name and open it in your browser to access the 2048 application.

---

# Cleanup

Delete the cluster:

```bash
eksctl delete cluster \
--name demo-cluster \
--region us-east-1
```