---
title : "Implement Governance and Security"
date : "2025-07-29"
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

Apply governance and security policies to ensure compliance and secure operations.

1. **Define RBAC Policies**:
   - Create a role for restricted access in workload clusters:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig create role developer --verb=get,list --resource=pods
     kubectl --kubeconfig=workload-cluster-1.kubeconfig create rolebinding developer-binding --role=developer --user=dev-user
     ```

2. **Network Policies**:
   - Apply a network policy to restrict traffic:
     ```yaml
     apiVersion: networking.k8s.io/v1
     kind: NetworkPolicy
     metadata:
       name: restrict-access
       namespace: default
     spec:
       podSelector:
         matchLabels:
           app: nginx
       policyTypes:
       - Ingress
       ingress:
       - from:
         - podSelector:
             matchLabels:
               role: frontend
     ```
   - Apply to both workload clusters:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig apply -f network-policy.yaml
     ```

3. **Enable AWS Security Features**:
   - Enable AWS EKS encryption for secrets:
     ```
     aws eks associate-encryption-config --cluster-name workload-cluster-1 --encryption-config file://encryption-config.json
     ```
   - Use AWS IAM roles for service accounts (IRSA) to secure pod access to AWS resources.

4. **Audit Logging**:
   - Enable EKS control plane logging:
     ```
     aws eks update-cluster-config --name workload-cluster-1 --logging '{"clusterLogging":[{"types":["api","audit","authenticator","controllerManager","scheduler"],"enabled":true}]}'
     ```

