---
title : "Workload Distribution"
date : "2025-07-29"
weight : 6
chapter : false
pre : " <b> 6. </b> "
---


Distribute workloads across clusters for high availability and load balancing.

1. **Deploy Sample Application**:
   - Deploy a sample app (e.g., `nginx`) on both workload clusters:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig apply -f nginx-deployment.yaml
     kubectl --kubeconfig=workload-cluster-2.kubeconfig apply -f nginx-deployment.yaml
     ```

2. **Set Up Multi-Cluster Load Balancing**:
   - Use an external DNS service or AWS Global Accelerator to distribute traffic across clusters.
   - Example with AWS Route 53:
     ```
     aws route53 create-hosted-zone --name example.com --caller-reference $(date +%s)
     ```
   - Create DNS records pointing to both clusters’ ingress controllers.

3. **Test Workload Distribution**:
   - Access the application via the Route 53 DNS name and verify load balancing across clusters.