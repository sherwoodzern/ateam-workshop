# Helidon Hello World  

## Before You Begin

This tutorial shows you how to create a hello world program using the Helidon microservice framework. 

### Background

Project Helidon is an open source set of Java libraries used to write microservices. According to the official documentation, "Helidon is a collection of Java libraries for writing microservices that run on a fast web core powered by Netty... There is no unique tooling or deployment model. Your microservice is just a Java SE application."

In this tutorial you will be using the Helidon framework to create a Java SE application, package the application as a Docker Image, and then deploy and connect to the packaged application running on Kubernetes. 

### What Do You Need?

The following list shows the minimum versions: 

- [Java SE 8](https://www.oracle.com/technetwork/java/javase/downloads) or [Open JDK 8](http://jdk.java.net/)
- [Maven 3.5](https://maven.apache.org/download.cgi) 
- [Docker 18.02](https://docs.docker.com/install/)
- [Kubectl 1.7.4](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- Kubernetes: Enable [Kubernetes Support for Mac](https://docs.docker.com/docker-for-mac/#kubernetes) or [Kubernetes Support for Windows](https://docs.docker.com/docker-for-windows/#kubernetes).

[Here](https://helidon.io/docs/latest/#/getting-started/01_prerequisites) is an updated list of pre-requisites for using Helidon.



## Generate The Project

Generate the project sources using the Helidon SE Maven archetypes. The Helidon SE example implements the REST service using the Helidon WebServer component directly. It shows the basics of configuring the WebServer and implementing basic routing rules.

1. Inside your development folder run the Helidon SE Example Maven archetype:

```
mvn archetype:generate -DinteractiveMode=false \
    -DarchetypeGroupId=io.helidon.archetypes \
    -DarchetypeArtifactId=helidon-quickstart-mp \
    -DarchetypeVersion=0.11.0 \
    -DgroupId=io.helidon.examples \
    -DartifactId=ateam-mp \
    -Dpackage=io.helidon.examples.ateam.mp
```

2. change directories into the one created by the archetype:

```
cd ateam-mp
```

3. Build the application: 

```
mvn package
```

4. The project builds an application jar for the example and saves all runtime dependencies in the target/libs directory. This means you can easily start the application by running the application jar file: 

```
java -jar target/ateam-mp.jar
```
You can now access the application	at http://localhost:8080/greet

The example is a very simple "Hello World" greeting service. It supports GET requests for generating a greeting message, and a PUT request for changing the greeting itself. The response is encoded using JSON. For example: 

```
curl -X GET http://<external-ip>:<port>/greet
{"message":"Hello World!"}

curl -X GET http://<external-ip>:<port>/greet/Joe
{"message":"Hello Joe!"}

curl -X PUT http://<external-ip>:<port>/greet/greeting/Hola
{"greeting":"Hola"}

curl -X GET http://<external-ip>:<port>/greet/Jose
{"message":"Hola Jose!"}
```

5. The project also contains a Docker file so that you can easily build and run a docker image. Because the example’s runtime dependencies are already in target/libs, the Docker file is pretty simple (see target/Dockerfile). To build the Docker image, you need to have Docker installed and running on your system.

For this tutorial you will be using your own github repository. In upcoming tutorials you will use Oracle Cloud Infrastructure Registry (OCIR).

```
docker login (provide your userId and password when prompted)
docker build -t ateam-mp:latest target
docker tag ateam-mp:latest <github user>/ateam-mp:1.0.0
docker push <dockerhub-user>/ateam-mp:1.0.0
```

6. If you would like to start the application with Docker run: 

```
docker run --rm -p 8080:8080 <github-user>/ateam-mp:1.0.0
```
You can access the application	at http://localhost:8080/greet

## Deploy the Application to Kubernetes. 


1. Verify connectivity to cluster: 

```
kubectl cluster-info
kubectl get nodes
```

2. Deploy the application to Kubernetes:

Prior to deploying to Kubernetes we need to modify 2 YAML files
Open the ateam-mp-svc.yaml file and make the changes specified in the YAML file

Open the ateam-mp-LoadBalancer.yaml file and make the changes specified.

In actuality, when you executed the mvn package the app.yaml file was modified making it a Service and Deployment YAML file.  You could actually execute a kubectl apply ... using the app.yaml file. However,in order to gain some practice in creating and understanding the YAML file let's take a slightly different approach.

Modify the ateam-mp-svc.yaml file filling in the specifics as notated in the file.

```
kubectl apply -f ateam-mp-svc.yaml

Verify the deployment is successful

kubectl get po
```

Deploy the load balancer

```
kubectl apply -f ateam-mp-LoadBalancer.yaml

kubectl get svc 

kubectl get pods 
```

3. Identify the external IP for the LoadBalancer. With the exposed external IP you can now access your service - 
```
curl -v http://<external-ip>:<port>/<uriPath>

```

4. Now let's access the Kubernetes dashboard: 

```
kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/overview?namespace=default
```
Take a few minutes to navigate around the dashboard.

5. We deployed a LoadBalancer for this specific service. Obviously, we don't want to do this for every service; it would be very expensive. The better approach would be to use an ingress gateway. Using a gateway will not require a load balancer for every service. Let's setup a gateway. 

Since we already have Istio deployed we are going to use the Istio ingress-gateway for the gateway.  Let's remove the load balancer that we recently deployed.
```
kubectl delete -f ateam-mp-LoadBalancer.yaml

```
In order to use Istio we will have to deploy a couple of extra YAML files. We need to deploy a VirtualService and a DestinationRule. If you recall from the effort yesterday we did this on a number of services.

Modify the virtual service yaml file
Modify the destination rule yaml file

```
kubectl apply -f ateam-mp-virtualservice.yaml
kubectl apply -f ateam-mp-destinationrule.yaml
```
Identify the external IP address of the Istio ingress gateway. Since the Istio ingress gateway is deployed in the istio-system namespace you have to specify the namespace in order to locate the services in that namespace.  There will be a handful of services shown. You want the istio-ingressgateway.  It will be a type of LoadBalancer.  The ingress gateway listens on port 80; therefore, there is no need to specify a port.

```
kubectl get svc -n istio-system

curl -v http://<ingress-gateway-IP>/<uriPath>
```


## Clean Up 

After you’re done, remove the application from Kubernetes: 


## Want to Learn More?

- [Official Helidon Documentation](https://helidon.io/docs/latest/#/about/01_introduction)
