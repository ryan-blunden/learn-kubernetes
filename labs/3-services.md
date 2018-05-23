# Services Lab

To illustrate the dynamic and loosely coupled design of Kubernetes, we're going to expose a service, but exposing the service first without any pods to support it.

!!! note

    Because Docker for Desktop does not bind the NodePort associated with the LoadBalancer Service type
    to localhost (on the host), I will be using the NodePort service template in the below examples.

Create the service:

    # `np` is short for NodePort
    kubectl apply -f templates/api-service-np.yaml

Inspect the service so we can get the randomly assigned ports:

    kubectl describe service api-service-np | grep Port

Confirm we don't have any pods matching the label the service is querying for:

    kubectl get pods api-pod

## Services and Docker for Mac

For those of you coming from Minikube, you may find Docker for Desktop's approach to service exposure a bit strange.

So can we access the service from our host machine? Well sort of, but not in the way you might expect.

### Load Balancer

The for load port forwarding every `port` value (not the NodePort) in our service to the VM for us from our host.

Open a new terminal window and create the api pod.

    kubectl apply -f templates/api-pod.yaml

### Service name by DNS

This must be done through a container running in the same namespace as the service.

Now that we've deployed our API pod, we can get the FQDN.

    kubectl exec -ti <pod-id> nslookup api-service

### The ClusterIP

When a Service is created, it is assigned Cluster IP which is unique for the life of the service. This Service IP is completely virtual though. See https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies for more info.

Let's find the `Cluster IP` value of the `api-service`. The Cluster IP is guaranteed not to change.

    echo `kubectl get service api-service --template='{{.spec.clusterIP}}'`

### TODO

 - Example with hitting the service from the Docker VM.
 - Example of hitting the service IP address.
 - Example of hitting the service hostname.
 - Example of using the NodePort so you can have services that forward to the same Pod ports.
