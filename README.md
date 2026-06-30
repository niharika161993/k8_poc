# k8_poc

Name_based_virtual_hosting_ingress.yml 

With Services, routing rules are associated with a given Service. They exist for as long as the Service exists, and there are many rules because there are many Services in the cluster. If we can somehow decouple the routing rules from the application and centralize the rules management, we can then update our application without worrying about its external access. This can be done using the Ingress resource - a collection of rules that manage inbound connections to cluster Services.

To allow the inbound connection to reach the cluster Services, Ingress configures a Layer 7 HTTP/HTTPS load balancer for Services and provides the following:

TLS (Transport Layer Security)
Name-based virtual hosting
Fanout routing
Loadbalancing
Custom rules.

<img width="1201" height="430" alt="ssd5q8n8k3nt-Ingress2023" src="https://github.com/user-attachments/assets/d144ab01-f9e3-4739-b234-076adbb287d7" />



With Ingress, users do not connect directly to a Service. Users reach the Ingress endpoint, and, from there, the request is forwarded to the desired Service. You can see an example of a Name-Based Virtual Hosting Ingress definition in Name_based_virtual_hosting_ingress.yml 
In the example, user requests to both blue.example.com and green.example.com would go to the same Ingress endpoint, and, from there, they would be forwarded to webserver-blue-svc, and webserver-green-svc, respectively.


Ingress Controller
An Ingress Controller is an application watching the Control Plane Node's API server for changes in the Ingress resources and updates the Layer 7 Load Balancer accordingly. An Ingress Controller is also known as Controllers, Ingress Proxy, Service Proxy, Reverse Proxy, etc. Kubernetes supports an array of Ingress Controllers, and, if needed, we can also build our own. GCE L7 Load Balancer Controller, AWS Load Balancer Controller, and Nginx Ingress Controller are commonly used Ingress Controllers. Other controllers are Contour, HAProxy Ingress, Istio Ingress, Kong, Traefik, etc. In order to ensure that the ingress controller is watching its corresponding ingress resource, the ingress resource definition manifest needs to include an ingress class name, such as spec.ingressClassName: nginx and optionally one or several annotations specific to the desired controller, such as nginx.ingress.kubernetes.io/service-upstream: "true" (for an nginx ingress controller).

Starting the Ingress Controller in Minikube is extremely simple. Minikube ships with the Nginx Ingress Controller set up as an add-on, disabled by default. It can be easily enabled by running the following command:

$ minikube addons enable ingress 

Deploy an Ingress Resource
Once the Ingress Controller is deployed, we can create an Ingress resource using the kubectl create command. For example, if we create a virtual-host-ingress.yaml file with the Name-Based Virtual Hosting Ingress rule definition that we saw in the Ingress (2) section, then we use the following command to create an Ingress resource:

$ kubectl create -f virtual-host-ingress.yaml
