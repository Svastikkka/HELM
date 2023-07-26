## Redis Operator

Reference link - https://github.com/spotahome/redis-operator

### How to setup Spotahome Redis operator
1. Set the necessary parameters in `charts/redis-op/values.yaml ` (see comments in file)
2. Run command

    ``` helm install <release_name> charts/redis-op -n <namespace> ```

NOTE: In `dev` cluster "release_name" is `redis-cr` for Redis cluster and `sh-redis` for Redis operator.

3. Check that pod up and running, for example
```
kubectl get po -n <namespace>
NAME                                       READY   STATUS    RESTARTS   AGE
h-redis-redis-operator-77b75984f9-cm5fh   1/1     Running   0          3d8h
```


### How to setup Redis Cluster ( aka. Redis CR's, Redis resources)
1. Set the necessary parameters in `charts/redis-cr/values.yaml` (see comments in file)

NOTE: Redis auth password configured in values.yaml via parameter `redis.auth.password`. It should be plaintext (base64-encoded does not work).

2. Run command

    ``` helm install <release_name> charts/redis-cr -n <namespace> ```

3. Check that pods up and running, for example
```
redis-cr-monitor-7b8d8549c5-sf7gf          2/2     Running   0          7d1h
rfr-sh-redis-0                             2/2     Running   0          7d23h
rfr-sh-redis-1                             2/2     Running   0          7d23h
rfr-sh-redis-2                             2/2     Running   0          7d23h
rfs-sh-redis-7c8f98b5d8-7pczc              2/2     Running   0          11d
rfs-sh-redis-7c8f98b5d8-9kt8p              2/2     Running   0          11d
rfs-sh-redis-7c8f98b5d8-lb58h              2/2     Running   0          11d
sh-redis-redis-operator-7459b4bb94-2wpr8   1/1     Running   0          12d
```

NOTE: In this version `Redis Operator` and `Redis Resources` charts should be installed to the same namespace


### Connection to the created Redis cluster
In order to connect to the redis-failover and use it, a Sentinel-ready library has to be used. This will connect through the Sentinel service to the Redis node working as a master. The connection parameters are the following:

```
url: rfs-<release_name>
port: 26379
master-name: mymaster
```

WARNING: Sentinel-ready library is necessary. For example `https://www.npmjs.com/package/ioredis` for Node.js

### Monitoring

1. 1st step - Grafana dashboard for Operator state and cluster health  - https://github.com/spotahome/redis-operator/blob/master/example/grafana-dashboard/redisfailover-health.json. See panel setting in `dev` cluster to to understand changes dashboard settings.

2. 2nd step - install Redis plugin https://grafana.com/grafana/plugins/redis-datasource/. Settings for Redis datasource:
 
 - Redis address `redis://rfs-sh-redis.cache.svc.cluster.local:26379` (example for `dev` cluster. This is the redis sentinel service URL)
 - ACL - enable, username - `default`
 - Password  - redis auth password wich confirured in values file during setup Redis CRs.


- Sentinel Master name - `mymaster`
- Sentinel ACL - disable
- Password - empty or the same as Redis password. 

### Logging

1. Logging can be enabled in values file via parameter `monitor.enabled`. 
If it `true` additional deployment `monitor-exporter` will be deployed with Redis CRs.

2. Deployment `monitor-exporter` contains 2 containers: 
         - `monitor-exporter` - run redis-cli command `MONITOR` and store output of command to file
         - `monitor-fluentbit` - read file from exporter and send it to Loki.

Loki configuration can be customized in `charts/redis-cr/templates/fluentbit-configmap.yaml` file.

### Sequence
- `kubectl create ns cache`
- `helm upgrade --install sh-redis charts/redis-op -n cache`
- `helm upgrade --install redis-cr charts/redis-cr -n cache`