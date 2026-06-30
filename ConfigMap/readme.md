ConfigMaps
ConfigMaps allow us to decouple the configuration details from the container image. Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes. We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.

Create a ConfigMap from Literal Values

A ConfigMap can be created with the imperative kubectl create configmap command, and we can display its details using the kubectl get or kubectl describe commands.

Create the ConfigMap:

$ kubectl create configmap my-config \
  --from-literal=key1=value1 \
  --from-literal=key2=value2

configmap/my-config created

Display the ConfigMap details in YAML for my-config:

$ kubectl get configmaps my-config -o yaml

apiVersion: v1
data:
  key1: value1
  key2: value2
kind: ConfigMap
metadata:
  creationTimestamp: 2024-03-02T07:21:55Z
  name: my-config
  namespace: default
  resourceVersion: "241345"
  selfLink: /api/v1/namespaces/default/configmaps/my-config
  uid: d35f0a3d-45d1-11e7-9e62-080027a46057

With the -o yaml option, we are requesting the kubectl command to produce the output in the YAML format. The object has the ConfigMap kind, and it has the key-value pairs listed under the data field. The name of ConfigMap and other details are part of the metadata field.




