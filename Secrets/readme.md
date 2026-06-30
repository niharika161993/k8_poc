Secrets
Let's assume that we have a Wordpress blog application, in which our wordpress frontend connects to the MySQL database backend using a password. While creating the Deployment for wordpress, we can include the MySQL password in the Deployment's YAML definition manifest, but the password would not be protected. The password would be available to anyone who has access to the definition manifest.

In this scenario, the Secret object can help by allowing us to encode in base 64 the sensitive information before sharing it. We can share sensitive information like passwords, tokens, or keys in the form of key-value pairs, similar to ConfigMaps; thus, we can control how the information in a Secret is used, reducing the risk for accidental exposures. In Deployments or other resources, the Secret object is referenced, without exposing its content.

It is important to keep in mind that by default, the Secret data is stored as plain text inside etcd, therefore administrators must limit access to the API server and etcd. However, Secret data can be encrypted at rest while it is stored in etcd, but this feature needs to be enabled at the API server level by the Kubernetes cluster administrator.
-------------------------------------------------------------------------------------

Create a Secret from Literal Values
To create a Secret, we can use the imperative kubectl create secret command:

$ kubectl create secret generic my-password \
  --from-literal=password=mysqlpassword

The above command would create a secret called my-password, which has the value of the password key set to mysqlpassword.

After successfully creating a secret we can analyze it with the get and describe commands. They do not reveal the content of the Secret. The type is listed as Opaque.

$ kubectl get secret my-password

NAME          TYPE     DATA   AGE
my-password   Opaque   1      8m

$ kubectl describe secret my-password

Name:          my-password
Namespace:     default
Labels:        <none>
Annotations:   <none>

Type Opaque

Data
====
password: 13 bytes

-----------------------------------------------------------------------------

Create a Secret from a Definition Manifest
We can create a Secret manually from a YAML definition manifest. The example manifest below is named mypass.yaml. There are two types of maps for sensitive information inside a Secret: data and stringData.

With data maps, each value of a sensitive information field must be encoded using base64. If we want to have a definition manifest for our Secret, we must first create the base64 encoding of our password:

$ echo mysqlpassword | base64

bXlzcWxwYXNzd29yZAo=

and then use it in the definition manifest:

apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=

Please note that base64 encoding does not mean encryption, and anyone can easily decode our encoded data:

$ echo "bXlzcWxwYXNzd29yZAo=" | base64 --decode

mysqlpassword

Therefore, make sure you do not commit a Secret's definition file in the source code.

With stringData maps, there is no need to encode the value of each sensitive information field. The value of the sensitive field will be encoded when the my-password Secret is created:

apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
stringData:
  password: mysqlpassword

Using the mypass.yaml definition file we can now create a secret with kubectl create command: 

$ kubectl create -f mypass.yaml

secret/my-password created


-----------------------------------------------------------------------------------

Create a Secret from a File
To create a Secret from a File, we can use the kubectl create secret command. 

First, we encode the sensitive data and then we write the encoded data to a text file:

$ echo mysqlpassword | base64

bXlzcWxwYXNzd29yZAo=

$ echo -n 'bXlzcWxwYXNzd29yZAo=' > password.txt

Now we can create the Secret from the password.txt file:

$ kubectl create secret generic my-file-password \
  --from-file=password.txt

secret/my-file-password created

After successfully creating a secret we can analyze it with the get and describe commands. They do not reveal the content of the Secret. The type is listed as Opaque.

$ kubectl get secret my-file-password

NAME               TYPE     DATA   AGE 
my-file-password   Opaque   1      8m

$ kubectl describe secret my-file-password

Name:          my-file-password
Namespace:     default
Labels:        <none>
Annotations:   <none>

Type  Opaque

Data
====
password.txt:  13 bytes 

--------------------------------------------------------------------------------

Use Secrets Inside Pods: As Environment Variables
Secrets are consumed by Containers in Pods as mounted data volumes, or as environment variables, and are referenced in their entirety (using the envFrom heading) or specific key-values (using the env heading).

Below we reference only the password key of the my-password Secret and assign its value to the WORDPRESS_DB_PASSWORD environment variable:

....
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
....

-----------------------------------------------------------------------------

Use Secrets Inside Pods: As Volumes
We can also mount a Secret as a Volume inside a Pod. The secret Volume plugin converts the Secret object into a mountable resource. The following example creates a file for each my-password Secret key (where the files are named after the names of the keys), the files containing the values of the respective Secret keys:

....
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-data"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-password
....

For more details, you can explore the documentation on managing Secrets.

--------------------------------------------------------------------------------
