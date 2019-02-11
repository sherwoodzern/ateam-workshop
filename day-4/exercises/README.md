# Deploying ElasticSearch, Fluentd, Kibana (EFK)

In this lab you will deploy EFK to your cluster using Helm.  Navigate to the efk directory under the day-4 exercises.  From this location execute the following:

```bash

helm install efk --name=efk

```

As soon as the EFK components have been deployed and operational you will see the following nodes.

```bash

szern-mac:efk szern$ kubectl get po -n kube-logging
NAME                      READY   STATUS    RESTARTS   AGE
es-cluster-0              1/1     Running   0          10d
es-cluster-1              1/1     Running   0          10d
es-cluster-2              1/1     Running   0          10d
fluentd-76rwt             1/1     Running   0          10d
fluentd-bh86x             1/1     Running   0          10d
fluentd-f9cgm             1/1     Running   0          10d
kibana-67d565b9cc-k4d95   1/1     Running   0          10d

```


