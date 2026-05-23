# Keda.sh

## Installation

```bash
helm repo update
kubectl create namespace keda
helm upgrade --install keda kedacore/keda --namespace keda --create-namespace --version 2.14.2
```

```bash
kubectl create namespace redis
helm repo add bitnami https://charts.bitnami.com/bitnami
helm upgrade --install redis bitnami/redis --namespace redis --set architecture=standalone
```

## Autoscaling Using Redis Length

Below are the required example manifest files for demonstration of scaling kubernetes pod using Redis List.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-worker
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-worker
  template:
    metadata:
      labels:
        app: redis-worker
    spec:
      containers:
        - name: redis-worker
          image: nginx:latest
          env:
            - name: REDIS_HOST
              value: "redis-master.redis.svc.cluster.local:6379"
            - name: REDIS_LIST
              value: "myqueue"
            - name: REDIS_PASSWORD
              value: "SxsKUXw1j1"
```

```yaml
apiVersion: keda.sh/v1alpha1
kind: TriggerAuthentication
metadata:
  name: redis-auth
spec:
  secretTargetRef:
    - parameter: host
      name: redis-secret
      key: host
    - parameter: password
      name: redis-secret
      key: password
```

```yaml

---
apiVersion: v1
kind: Secret
metadata:
  name: redis-secret
type: Opaque
stringData:
  host: "redis-master.redis.svc.cluster.local:6379"
  password: SxsKUXw1j1
```


```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: redis-scaledobject
spec:
  scaleTargetRef:
    name: redis-worker
  minReplicaCount: 1
  maxReplicaCount: 3
  triggers:
    - type: redis
      metadata:
        addressFromEnv: REDIS_HOST
        listName: myqueue
        listLength: "5"
        listLengthOperator: GreaterThanOrEqualTo
      authenticationRef:
        name: redis-auth
```

## Autoscaling Using Azure AQS

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: queue-worker
spec:
  replicas: 1
  selector:
    matchLabels:
      app: queue-worker
  template:
    metadata:
      labels:
        app: queue-worker
    spec:
      containers:
      - name: queue-worker
        image: nginx:latest
        env:
        - name: AZURE_STORAGE_ACCOUNT_NAME
          value: "production817392"
        - name: AZURE_STORAGE_QUEUE_NAME
          value: "bot-queue"
        - name: AZURE_STORAGE_CONNECTION_STRING
          valueFrom:
            secretKeyRef:
              name: azure-storage-secret
              key: connectionString
```

```yaml
apiVersion: keda.sh/v1alpha1
kind: TriggerAuthentication
metadata:
  name: azure-storage-auth
spec:
  secretTargetRef:
    - parameter: connectionString
      name: azure-storage-secret
      key: connectionString
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-storage-secret
type: Opaque
stringData:
  connectionString: "<ConnectionString>"
```

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: queue-worker-scaledobject
spec:
  scaleTargetRef:
    name: queue-worker
  minReplicaCount: 1
  maxReplicaCount: 10
  triggers:
    - type: azure-queue
      metadata:
        queueName: "bot-queue"
        connectionFromEnv: AZURE_STORAGE_CONNECTION_STRING
        queueLength: "5"
      authenticationRef:
        name: azure-storage-auth
```