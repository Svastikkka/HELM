# Cert Manager

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm upgrade --install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.16.3 \
  --set crds.enabled=true
```

# Trouble Shooting

```bash
kubectl edit clusterissuer letsencrypt-prod
```