+++
title = "Clean up resources"
date = 2022
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

To avoid unnecessary costs, clean up all resources created during the workshop.

1. **Delete Workload Clusters**:
   ```
   kubectl delete cluster workload-cluster-1
   kubectl delete cluster workload-cluster-2
   ```

2. **Delete Management Cluster**:
   ```
   eksctl delete cluster --name management-cluster --region us-east-1
   ```

3. **Remove VPC Peering**:
   ```
   aws ec2 delete-vpc-peering-connection --vpc-peering-connection-id <peering-id> --region us-east-1
   ```

4. **Delete SSH Key**:
   ```
   aws ec2 delete-key-pair --key-name workshop-key
   rm workshop-key.pem
   ```

5. **Remove Backup Bucket**:
   ```
   aws s3 rb s3://workshop-backup --force
   ```

6. **Verify Cleanup**:
   - Check AWS Console or CLI to ensure no resources (EKS clusters, EC2 instances, VPCs) remain.