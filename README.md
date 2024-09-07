# K8S Experiments

Kubernetes experiments using Vagrant to setup an RKE2 cluster

## Insecure registry

Create `/etc/docker/daemon.json` if it does not exist

```json
{
  "insecure-registries": ["harbor.k-1.local"]
}
```
