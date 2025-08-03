---
title : "Operational Procedures"
date : "2025-07-29"
weight : 9
chapter : false
pre : " <b> 9. </b> "
---

Define procedures for ongoing management.

1. **Cluster Upgrades**:
   - Upgrade a workload cluster using Cluster API:
     ```
     kubectl patch cluster workload-cluster-1 --type merge -p '{"spec":{"kubernetesVersion":"v1.26.0"}}'
     ```

2. **Backup and Restore**:
   - Use Velero for cluster backups:
     ```
     velero install --provider aws --plugins velero/velero-plugin-for-aws --bucket workshop-backup
     velero backup create workload-cluster-1-backup --include-cluster-resources=true
     ```

3. **Monitoring and Alerts**:
   - Deploy Prometheus and Grafana for monitoring:
     ```
     kubectl --kubeconfig=workload-cluster-1.kubeconfig apply -f prometheus-grafana.yaml
     ```
   - Set up alerts for CPU/memory usage and cluster health.

4. **Scaling**:
   - Scale worker nodes:
     ```
     eksctl scale nodegroup --cluster workload-cluster-1 --nodes 4
     ```
