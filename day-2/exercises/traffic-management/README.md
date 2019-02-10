# Traffic Management

# Basic Hello Web

In this part you will deploy the Hello Web and a Greeter service v1 to the cluster. Once you've deployed it, we will create gateway and a virtual service to be able to access the Hello Web from outside.

## 1. Deploy the Hello Web and Greeter Service v1

1. Before deploying any services we will enable automatic proxy injection. This will make sure that every pod deployed in the namespace "default" will be deployed with the Envoy proxy also within the pod

    ```bash
    kubectl label namespace default istio-injection=enabled
    ```

1. Create the Hello Web deployment and service with Istio sidecar injected:

    ```bash
    kubectl apply -f k8s/helloweb.yaml
    ```

1. Verify the deployment by running `kubectl get deploy helloweb`:

    ```bash
    $ kubectl get deploy helloweb
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    helloweb   3         3         3            3           2m
    ```

1. Create the Greeter V1 deployment and service with Istio sidecar injected:

    ```bash
    kubectl apply -f k8s/greeter-v1.yaml
    ```

1. Verify the deployment by running `kubect get deploy greeter-service-v1`:

    ```bash
    $ kubectl get deploy greeter-service-v1
    NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    greeter-service-v1   3         3         3            3           44s
    ```

## 2. Access the Hello Web service

```bash
kubectl get svc -n istio-system
```
Locate the istio-ingressgateway in the list. Get the external ip for the gateway. It is a type of Load Balancer. We will refer to this IP going forward as GATEWAY 


1. Deploy the gateway:

```bash
kubectl apply -f k8s/gateway.yaml
```

1. Deploy the Hello Web virtual service:

```bash
kubectl apply -f k8s/helloweb-virtualservice.yaml
```

1. Try accessing the service at `http://GATEWAY`

## 3. Clean up

Clean everything up:

```bash
kubectl delete -f k8s/helloweb.yaml)
kubectl delete -f k8s/greeter-v1.yaml)
kubectl delete -f k8s/helloweb-virtualservice.yaml
```

# Traffic Splitting

## 1. Deploy Hello Web and two versions of the Greeter service

1. Deploy Hello web and v1 and v2 of the Greeter service and corresponding virtual services:

    ```bash
    kubectl apply -f k8s/helloweb.yaml
    kubectl apply -f k8s/helloweb-virtualservice.yaml

    kubectl apply -f k8s/greeter-v1.yaml
    kubectl apply -f k8s/greeter-v2.yaml
    kubectl apply -f k8s/greeter-virtualservice.yaml

    ```

## 2. Deploy the destination rule

1. Deploy the destination rule:

    ```bash
    kubectl apply -f k8s/dest-rule.yaml
    ```

    If you installed Istio with mTLS, use the `dest-rule-auth.yaml` file instead.

## 3. Split traffic 50/50 between v1 and v2 versions of the Greeter service

1. Update the virtual service to route 30% of traffic to the v2 version and 70% to the v1 version of the Greeter service

## 4. Deploy a v3 version of the Greeter service

The v3 version of the greeter service uses an image named `learnistio/greeter-service:3.0.0`. Create the v3 deployment, update the destination rule and the virtual service, so 10% of the traffic goes to v3, 40% goes to v2 and 50% goes to v1. Once you verified that works, route all traffic to v3 version.

## 5. Advanced traffic routing

1. Deploy the virtual service that routes requests coming from Firefox to v2, and all other requests to v1:

```bash
kubectl apply -f k8s/greeter-virtualservice-v2-firefox.yaml
```

1. Update the virtual service to route all traffic with header `x-user: alpha` to v3 and traffic with header `x-user: beta` to v2, while all other traffic gets routed to v1.

# Movie Web (external API)

We will deploy a Movie Web frontend that tries to access and talk to an external API (themoviedb.org).

## 1. Obtain the API and deploy the Movie Web

1. Get the API from http://themoviedb.org (detailed instructions are [here](https://developers.themoviedb.org/3/getting-started/introduction)) and update the `movieweb.yaml` (replace the value <API_KEY_HERE> with an actual API KEY)
1. Deploy the Movie Web + virtual service

    ```bash
    kubectl apply -f k8s/movieweb.yaml)
    kubectl apply -f k8s/movieweb-virtualservice.yaml
    ```

1. Try accessing the service through the \GATEWAY (note: it won't work). 
Why won't it work?  The moviedb.org is an external service outside of the service mesh cluster; therefore, the Envoy proxy is unable to locate the service. 

The resolution is to deploy a service entry. The service entry updates the iptable in the service mesh. The Envoy proxy is now able to locate the external service.

## 2. Create and deploy a ServiceEntry

1. Create the service entry that allows access to themoviedb.org:

    ```bash
    kubectl apply -f k8s/movieweb-serviceentry.yaml
    ```

1. Access the service (it should work)

## 3. Clean up

```bash
kubectl delete -f k8s/movieweb.yaml)
kubectl delete -f k8s/movieweb-virtualservice.yaml
kubectl delete -f k8s/movieweb-serviceentry.yaml
```
