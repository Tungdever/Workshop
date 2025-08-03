---
title : "Cost Management"
date : "2025-07-29"
weight : 8
chapter : false
pre : " <b> 8. </b> "
---

Optimize costs for multi-cluster operations.

1. **Tag Resources**:
   - Tag all clusters and resources for cost tracking:
     ```
     aws eks tag-resource --resource-arn arn:aws:eks:us-east-1:<account-id>:cluster/workload-cluster-1 --tags Environment=Workshop,CostCenter=Training
     ```

2. **Use Spot Instances**:
   - Modify worker node groups to use Spot Instances:
     ```
     eksctl create nodegroup --cluster workload-cluster-1 --spot --instance-types t3.medium
     ```

3. **Monitor Costs**:
   - Enable AWS Cost Explorer and create a cost allocation report for the `Environment=Workshop` tag.
