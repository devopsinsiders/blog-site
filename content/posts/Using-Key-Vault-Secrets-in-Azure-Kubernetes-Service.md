---
title: "Using Key Vault Secrets in Azure Kubernetes Service"
date: 2024-10-02T11:10:36+08:00
draft: false
language: en
featured_image: ../assets/images/featured/Using-Key-Vault-Secrets-in-Azure-Kubernetes-Service.png
summary: Securely managing secrets is critical for modern applications, and Azure Key Vault integrated with Azure Kubernetes Service (AKS) provides a robust solution.
description: In this post, learn how to securely inject Azure Key Vault secrets into your applications running on Azure Kubernetes Service (AKS) using Managed Identities and Kubernetes secrets.
author: Aman Gupta - DevOps Insiders
authorimage: ../assets/images/global/author.webp
categories: Blog
tags: Azure, Kubernetes, Key Vault, Security, AKS
---

Managing secrets like connection strings, API keys, and certificates in a secure manner is crucial for any cloud-native application. Azure Key Vault provides a centralized and secure way to manage sensitive information, while Azure Kubernetes Service (AKS) allows you to run scalable containerized applications. By integrating Key Vault with AKS, you can securely inject secrets into your applications without hardcoding them into your code or configuration files.

In this blog post, we'll explore how to use Azure Key Vault with Azure Kubernetes Service using Managed Identities and Kubernetes Secrets.

---

## Why Use Azure Key Vault in AKS?

Using Key Vault in AKS brings several benefits:

1. **Centralized Secret Management**: Key Vault provides a secure, centralized store for secrets, keys, and certificates, making it easier to manage them across multiple applications.
2. **Enhanced Security**: With Managed Identities, you can securely access Key Vault without storing credentials in your application code.
3. **Automated Secret Rotation**: You can automatically update secrets in Key Vault without redeploying your applications.
4. **RBAC Support**: Azure Role-Based Access Control (RBAC) allows you to control who has access to specific secrets.

---

## Steps to Use Key Vault Secrets in AKS

### Step 1: Create an Azure Key Vault

First, you need to create an Azure Key Vault to store your secrets. You can do this via the Azure Portal, Azure CLI, or ARM templates.

```bash
az keyvault create --name <YourKeyVaultName> --resource-group <YourResourceGroup> --location <YourLocation>
```

After creating the Key Vault, add your secrets to it:

```bash
az keyvault secret set --vault-name <YourKeyVaultName> --name <YourSecretName> --value <YourSecretValue>
```

### Step 2: Enable Managed Identity for AKS

Azure Managed Identity allows your AKS cluster to securely authenticate to Azure services, including Key Vault, without requiring you to manage any credentials.

1. Assign a Managed Identity to your AKS cluster:

```bash
az aks update -g <YourResourceGroup> -n <YourAKSClusterName> --enable-managed-identity
```

2. Grant your AKS Managed Identity access to Key Vault secrets:

```bash
az keyvault set-policy -n <YourKeyVaultName> --secret-permissions get --spn <ManagedIdentityClientID>
```

### Step 3: Install the Azure Key Vault Provider for Secrets Store CSI Driver

The Azure Key Vault Provider for Secrets Store CSI Driver enables Kubernetes applications to use secrets from Azure Key Vault as Kubernetes secrets.

1. Add the CSI Driver to your AKS cluster:

```bash
helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
helm install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver -n kube-system
```

2. Install the Azure Key Vault Provider:

```bash
helm repo add csi-secrets-store-provider-azure https://azure.github.io/secrets-store-csi-driver-provider-azure/charts
helm install csi-secrets-store-provider-azure csi-secrets-store-provider-azure/secrets-store-csi-driver-provider-azure -n kube-system
```

### Step 4: Create a SecretProviderClass in AKS

The `SecretProviderClass` defines how secrets from Key Vault should be mounted into your Kubernetes pods.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-keyvault-secret
  namespace: default
spec:
  provider: azure
  secretObjects: 
    - secretName: my-k8s-secret
      type: Opaque
      data:
        - objectName: <YourSecretName>
          key: <YourKey>
  parameters:
    usePodIdentity: "false"
    keyvaultName: "<YourKeyVaultName>"
    cloudName: ""
    objects: |
      array:
        - |
          objectName: <YourSecretName>
          objectType: secret
    tenantId: "<YourTenantID>"
```

### Step 5: Use Secrets in Your Kubernetes Application

After creating the `SecretProviderClass`, you can reference the Kubernetes secret in your deployment YAML file like any other Kubernetes secret:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-image
        env:
        - name: MY_SECRET
          valueFrom:
            secretKeyRef:
              name: my-k8s-secret
              key: <YourKey>
```

### Step 6: Test the Setup

Deploy your application and verify that the secrets are being retrieved from Key Vault by checking the environment variables or application logs.

---

## Conclusion

By using Azure Key Vault in combination with AKS, you can manage and protect sensitive data in your Kubernetes workloads. This integration ensures that secrets are securely managed and accessed only by authorized services, eliminating the need for hardcoded secrets in your code or configuration files.

Take advantage of Azure Key Vault and AKS for better security and centralized management of your application secrets.

---


