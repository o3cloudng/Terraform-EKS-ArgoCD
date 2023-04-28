# Terraform - Provision an EKS Cluster

This repo is a companion repo to the [Provision an EKS Cluster tutorial](https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks), containing
Terraform configuration files to provision an EKS cluster on AWS.

## Steps:
what you will need is

* AWS account 
* IAM user with programmatic access & EKS admin policy
* aws cli installation and profile configuration

```
aws configure --profile <terraform-user>
```

### Connect kubectl to EKS cluster on your machine
```
aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name)
```

### Check cluster
```
kubectl cluster-info
```

### Get nodes
```
kubectl get nodes
```

### Create namespace for argocd
```
kubectl create namespace argocd
```

### Install argocd from their official repository
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Change service type from ClusterIp to LoadBalancer
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### Username: admin, use the command below to get password and decrypt using base64
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

### Terraform Clean up
```
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

```
kubectl delete namespace argocd
```
```
terraform destroy --auto-approve
```


