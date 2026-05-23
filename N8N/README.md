```bash
helm install n8n oci://8gears.container-registry.com/library/n8n \
  --version 1.1.0 \
  --namespace n8n --create-namespace \
  -f n8n-values.yaml
```