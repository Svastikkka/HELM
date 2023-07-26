# MongoDB-operator

## Quick install Mongo Kube Operator for dev-test environment

Clone repo - https://gitlab.cloud.digite.com/microservices-fabric/mongo-operator/-/tree/first_version

Run the following commands:

1. Set admin password in `resources/mongodb_replicaset_cr.yaml` file in `Secret` section

2. Install CRD

```
- kubectl apply -f config/crd/bases/mongodbcommunity.mongodb.com_mongodbcommunity.yaml
- kubectl apply -f config/rbac/. --namespace mongocr
- kubectl apply -f config/manager/manager.yaml --namespace mongocr
- kubectl get crd/mongodbcommunity.mongodbcommunity.mongodb.com
- kubectl rollout status deployment/mongodb-kubernetes-operator -n mongocr

- kubectl delete -f config/crd/bases/mongodbcommunity.mongodb.com_mongodbcommunity.yaml
- kubectl delete -f config/rbac/. --namespace mongocr
- kubectl delete -f config/manager/manager.yaml --namespace mongocr
```

3. Install Mongo resources

```
- kubectl apply -k config/rbac/. --namespace mongocr
- kubectl apply -f ./resources/rbac.yaml -n mongocr
- kubectl apply -f ./resources/mongodb_replicaset_cr.yaml -n mongocr
-
```

4. Set `web username` and `web password` for Mongo Express in `mongo_express.yaml` file in `env` section (see comments in file)

5. Install Mongo Express

        kubectl apply -f resources/mongo_express.yaml -n mongocr

6. To acces the Mongo Express use the following command:

        kubectl port-forward <mongo-express-pod-name> -n mongocr 8080:8081

and follow URL http://localhost:8080 in browser. Use login and password from step 4.