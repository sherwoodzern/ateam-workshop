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
With everything deployed let's make sure all components are working properly.

```bash
kubectl port-forward es-cluster-0 9200:9200 -n kube-logging
```

In a separate terminal window, perform a curl request against the REST API

```bash
curl http://localhost:9200/_cluster/state?pretty
```

You should see the following output:

```bash
{
  "cluster_name" : "k8s-logs",
  "compressed_size_in_bytes" : 348,
  "cluster_uuid" : "QD06dK7CQgids-GQZooNVw",
  "version" : 3,
  "state_uuid" : "mjNIWXAzQVuxNNOQ7xR-qg",
  "master_node" : "IdM5B7cUQWqFgIHXBp0JDg",
  "blocks" : { },
  "nodes" : {
    "u7DoTpMmSCixOoictzHItA" : {
      "name" : "es-cluster-1",
      "ephemeral_id" : "ZlBflnXKRMC4RvEACHIVdg",
      "transport_address" : "10.244.8.2:9300",
      "attributes" : { }
    },
    "IdM5B7cUQWqFgIHXBp0JDg" : {
      "name" : "es-cluster-0",
      "ephemeral_id" : "JTk1FDdFQuWbSFAtBxdxAQ",
      "transport_address" : "10.244.44.3:9300",
      "attributes" : { }
    },
    "R8E7xcSUSbGbgrhAdyAKmQ" : {
      "name" : "es-cluster-2",
      "ephemeral_id" : "9wv6ke71Qqy9vk2LgJTqaA",
      "transport_address" : "10.244.40.4:9300",
      "attributes" : { }
    }
  },
...
```
Some of the values for the elements will be different for your cluster; however, make sure you have es-cluster-0, es-cluster-1, and es-cluster-2. 

From the output of the pod listing you can see that the kibana image has also been deployed.  Let's test to verify that Kibana is working properly.

```bash
kubectl port-forward kibana-67d565b9cc-k4d95 5601:5601 -n kube-logging
```

You should see the following output

```bash

Forwarding from 127.0.0.1:5601 -> 5601
Forwarding from [::1]:5601 -> 5601
```

Now, in your web browser, visit the following URL:

```bash
http://localhost:5601
```

The dashboard below should be rendered within the browser.

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/kibana.png "Kibana Dashboard")

In the dashboard select "Discover" in the left-hand navigation.

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/indexPattern.png "Index Page")

In the Index Pattern we will specify: logstash-*
Then select "> Next step"

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/createIndexPattern.png "Create Index")

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/configureSettings.png "Configure Settings")

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/indexSummary.png "Summary")

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/graphReporting.png "Graph")

![alt text](https://github.com/sherwoodzern/ateam-workshop/day-4/exercises/efk/Resources/discover.png "Discover")


