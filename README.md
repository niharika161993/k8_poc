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

