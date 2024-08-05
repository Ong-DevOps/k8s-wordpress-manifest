# Kubernetes Deployment Guide for WordPress

This guide provides instructions to deploy a WordPress application on a Kubernetes cluster, including setting up MySQL, cert-manager, and Let's Encrypt for SSL certificates.

## Prerequisites

- **doctl CLI installed:** [Installation Guide](https://docs.digitalocean.com/reference/doctl/how-to/install/)
- **kubectl CLI installed:** [Installation Guide](https://kubernetes.io/docs/tasks/tools/)
- **Generate and save your personal access token.**

## Steps

### 1. Create the Secret

Run the following command:
```bash
kubectl apply -f wp-mysql-secrets.yaml
```

### 2. Set Up MySQL

- Create the MySQL volume and ReplicaSet, and expose the internal service:
  ```bash
  kubectl apply -f mysql-volume-claim.yaml
  kubectl apply -f mysql-replicaset.yaml
  kubectl apply -f mysql-service.yaml
  ```

- Access the MySQL container shell, log into MySQL, and set up the database:
  ```bash
  kubectl exec -it <mysql-pod-name> -- bash
  mysql -u root -p
  CREATE DATABASE wordpress;
  ```
  Use `Ctrl-d` to exit the MySQL shell.

### 3. Deploy WordPress

- Apply the WordPress data volume claim, deployment, and service:
  ```bash
  kubectl apply -f wordpress-datavolume-claim.yaml
  kubectl apply -f wordpress-deployment.yaml
  kubectl apply -f wordpress-service.yaml
  ```

- Change the domain in the ingress host details in `wordpress-ingress.yaml` as needed.

### 4. Add Cert-Manager for SSL Certificates

- Create the cert-manager namespace and apply the cert-manager configuration:
  ```bash
  kubectl create namespace cert-manager
  kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.2/cert-manager.yaml
  kubectl get pods -n cert-manager
  ```

### 5. Deploy Let's Encrypt Issuer

- Create the Issuer and Certificate resources:
  ```bash
  kubectl apply -f issuer.yaml
  kubectl apply -f certificate.yaml
  ```

### 6. Create Ingress Resource

- Apply the WordPress ingress configuration:
  ```bash
  kubectl apply -f wordpress-ingress.yaml
  ```

## Dynamic yaml file for New WP site

### 1. Create a new namespace:
  ```bash
  kubectl create namespace <new-namespace>
  ```


### 2. Update the <new-domain> and <new-namespace> values in your configuration files.
Apply the configuration files:
  ```bash
  kubectl apply -f filename.yaml
  ```

