# CELERY
Celery Task Repository

```bash
# Celery Worker
helm upgrade --install celery-worker ./celery-worker --namespace celery -f celery-worker/override-gke.yaml
# Celery Beat
helm upgrade --install celery-beat ./celery-beat --namespace celery -f celery-beat/override-gke.yaml
# Celery Flower
helm upgrade --install celery-flower ./celery-flower --namespace celery -f celery-flower/override-gke.yaml
# Celery Redis (Temp)
helm upgrade --install celery-redis ./celery-redis --namespace celery -f celery-redis/override-gke.yaml
```