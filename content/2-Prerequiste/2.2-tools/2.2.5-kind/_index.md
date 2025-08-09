---
title : "Kind"
date : "2025-07-29"
weight : 5
chapter : false
pre : " <b> 2.2.5 </b> "
---

## Install
- **Run the following commands:**
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.24.0/kind-linux-amd64
chmod +x ./kind
sudo install -o root -g root -m 0755 ./kind /usr/local/bin/kind
```

## Verify Installation
   ```
   kind version
   ```
