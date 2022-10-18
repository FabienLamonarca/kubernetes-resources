# Minikube

## Memo setup

```bash
# set embedCerts into kube config
# this will put inline certs into kube config file, instead of their path which is useful for WSL cert path resolving
minikube config set EmbedCerts true
```