
<pre>┌─[ 7:32][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main <font color="#8AE234"><b>✓</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl auth can-i create deployments --namespace kube-system
yes
</pre>

<pre>┌─[ 9:25][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ openssl genrsa -out k3s_user.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
........................+++++
...........................................................................+++++
e is 65537 (0x010001)
</pre>

<pre>┌─[ 9:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ openssl req -new -key k3s_user.key \
└─▪ -out k3s_user.csr \
└─▪ -subj &quot;/CN=k3s_user&quot;
</pre>

After copying server-ca.crt and server-ca-key from my k3s server
from the directory `/var/lib/rancher/k3s/server/tls/`
I an going to sign the CSR

<pre>┌─[ 9:45][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:2 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ openssl x509 -req -in k3s_user.csr \
└─▪ -CA ~/.kube/server-ca.crt \
└─▪ -CAkey ~/.kube/server-ca.key \
└─▪ -CAcreateserial \
└─▪ -out k3s_user.crt -days 500
Signature ok
subject=CN = k3s_user
Getting CA Private Key
</pre>

<pre>┌─[ 9:46][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl config set-credentials k3s_user \
└─▪ --client-certificate=k3s_user.crt \
└─▪ --client-key=k3s_user.key
User &quot;k3s_user&quot; set.
</pre>

<pre>┌─[ 9:50][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl config set-context k3s_user \
└─▪ --cluster=k3s --user=k3s_user
Context &quot;k3s_user&quot; created.
</pre>

Edit ~/.kube/config
<pre>Change path
- name: k3s_user
  user:
    client-certificate: ~/.kube/k3s_user.crt
    client-key: ~/.kube/k3s_user.key
contexts:
- context:
    cluster: k3s
    user: k3s_user
  name: k3s_user
</pre>

And after adding these linet to the .kube/config, switch to the new user
<pre>┌─[10:00][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl config use-context k3s_user
Switched to context &quot;k3s_user&quot;.
</pre>

<pre>┌─[11:40][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl get node
error: You must be logged in to the server (Unauthorized)

┌─[11:41][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl get pod
error: You must be logged in to the server (Unauthorized)

</pre>

<pre>┌─[11:43][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:3 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl config use-context default
Switched to context &quot;default&quot;.
</pre>

<pre>┌─[11:49][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:4 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl apply -f ./binding.yaml
rolebinding.rbac.authorization.k8s.io/k3s_user created
</pre>








# Homework

- Create users deploy_view and deploy_edit. Give the user deploy_view rights only to view deployments, pods. Give the user deploy_edit full rights to the objects deployments, pods.

To create these users with such rights, we are about to create some manifest files and apply them.
<pre>
┌─[ 1:53[][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ cat ./deploy_view.yaml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deploy_view_role
  namespace: default
rules:
  - verbs:
      - get
      - watch
      - list
    apiGroups:
      - apps
    resources:
      - pods
      - deployments
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deploy_view
  namespace: default
subjects:
- kind: User
  name: deploy_view
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: deploy_view_role
  apiGroup: rbac.authorization.k8s.io

┌─[ 1:55][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl apply -f ./deploy_view_role.yaml 
role.rbac.authorization.k8s.io/deploy_view_role created
rolebinding.rbac.authorization.k8s.io/deploy_view unchanged

┌─[ 1:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ cat ./deploy_edit.yaml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deploy_edit_role
  namespace: default
rules:
  - verbs:
      - get
      - watch
      - list
      - create
      - update
      - patch
      - delete
    apiGroups:
      - apps
    resources:
      - pods
      - deployments
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deploy_edit
  namespace: default
subjects:
- kind: User
  name: deploy_edit
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: deploy_edit_role
  apiGroup: rbac.authorization.k8s.io

┌─[ 2:00][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./deploy_edit_role.yaml 
role.rbac.authorization.k8s.io/deploy_edit_role created
rolebinding.rbac.authorization.k8s.io/deploy_edit unchanged
</pre>


- Create namespace prod. Create users prod_admin, prod_view. Give the user prod_admin admin rights on ns prod, give the user prod_view only view rights on namespace prod.

<pre>
┌─[ 2:14][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create namespace prod
namespace/prod created

┌─[ 2:15][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl get namespace
NAME              STATUS   AGE
default           Active   42h
kube-system       Active   42h
kube-public       Active   42h
kube-node-lease   Active   42h
prod              Active   67s
</pre>

<pre>
┌─[ 2:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ cat ./prod_view.yaml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: prod_view_role
  namespace: prod
rules:
- apiGroups:
  - apps
  verbs:
  - get
  - watch
  - list
  resources:
  - pods
  - deployments
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: prod_view
  namespace: prod
subjects:
- kind: User
  name: prod_view
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: prod_view_role
  apiGroup: rbac.authorization.k8s.io

┌─[ 2:16][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./prod_view_role.yaml 
role.rbac.authorization.k8s.io/deploy_view_role created
rolebinding.rbac.authorization.k8s.io/prod_view created
</pre>

Check permissions for created prod_view user

<pre>┌─[ 6:01][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i get deployments --namespace prod --as prod_view
yes

┌─[ 6:02][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i create deployments --namespace prod --as prod_view
no
</pre>

Next, let's create analogue user/role for prod_admin. But with authentication with a certificate and context configuration
for usage from my laptop kubectl console command

<pre>┌─[ 4:03][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ openssl genrsa -out prod_admin.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
................................+++++
........+++++
e is 65537 (0x010001)

┌─[ 4:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ openssl req -new -key prod_admin.key -out prod_admin.csr -subj &quot;/CN=prod_admin&quot;

┌─[ 4:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ cat ./prod_admin_csr.yaml
apiVersion: certificates.k8s.io%2fv1
kind: CertificateSigningRequest
metadata:
  name: prod_admin
spec:
  groups:
  - system:authenticated
  request: $(cat prod_admin.csr | base64 | tr -d '\n')
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth

┌─[ 4:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl apply -f ./prod_admin_csr.yaml
certificatesigningrequest.certificates.k8s.io/john created

┌─[ 4:09][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl get csr
NAME   AGE   SIGNERNAME                            REQUESTOR      REQUESTEDDURATION   CONDITION
john   13s   kubernetes.io/kube-apiserver-client   system:admin   &lt;none&gt;              Pending

┌─[ 4:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl get csr prod_admin -o jsonpath='{.status.certificate}'  | base64 -d > prod_admin.crt

┌─[ 2:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ cat ./prod_admin.yaml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: prod_admin_role
  namespace: prod
rules:
- apiGroups:
  - apps
  verbs:
  - get
  - watch
  - list
  - create
  - update
  - patch
  - delete
  resources:
  - pods
  - deployments
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: prod_admim
  namespace: prod
subjects:
- kind: User
  name: prod_admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: prod_admin_role
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: prod_admin_role
  apiGroup: rbac.authorization.k8s.io

┌─[ 2:17][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./prod_edit_role.yaml 
role.rbac.authorization.k8s.io/prod_admin_role created
rolebinding.rbac.authorization.k8s.io/prod_admim created

┌─[ 4:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-credentials prod_admin --client-key=prod_admin.key --client-certificate=prod_admin.crt --embed-certs=true
User &quot;prod_admin&quot; set.

┌─[ 4:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-context prod_admin --cluster=default --user=prod_admin
Context &quot;prod_admin&quot; created.
</pre>

Checking permissions
<pre>┌─[ 5:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i get deployments --namespace prod --as prod_admin
yes

┌─[ 5:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i create deployments --namespace prod --as prod_admin
yes

┌─[ 4:25][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config use-context prod_admin
Switched to context &quot;prod_admin&quot;.

┌─[ 4:25][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config current-context
prod_admin

┌─[ 4:50][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i create pods --namespace prod
yes
</pre>


- Create a serviceAccount sa-namespace-admin. Grant full rights to namespace default. Create context, authorize using the created sa, check accesses.

<pre>┌─[ 6:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ openssl genrsa -out sa-namespace-admin.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
.........................+++++
........+++++
e is 65537 (0x010001)

┌─[ 6:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ openssl req -new -key sa-namespace-admin.key -out sa-namespace-admin.csr -subj &quot;/CN=sa-namespace-admin&quot;

┌─[ 6:53][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ cat &lt;&lt;EOF | kubectl apply -f -
└─▪ apiVersion: certificates.k8s.io/v1
└─▪ kind: CertificateSigningRequest
└─▪ metadata:
└─▪   name: sa-namespace-admin
└─▪ spec:
└─▪   groups:
└─▪   - system:authenticated
└─▪   request: $(cat sa-namespace-admin.csr | base64 | tr -d &apos;\n&apos;)
└─▪   signerName: kubernetes.io/kube-apiserver-client
└─▪   usages:
└─▪   - client auth
└─▪ EOF
certificatesigningrequest.certificates.k8s.io/sa-namespace-admin created

┌─[ 6:56][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ 
kubectl get csr
NAME                 AGE   SIGNERNAME                            REQUESTOR      REQUESTEDDURATION   CONDITION
sa-namespace-admin   64s   kubernetes.io/kube-apiserver-client   system:admin   &lt;none&gt;              Pending

┌─[ 6:57][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl certificate approve sa-namespace-admin
certificatesigningrequest.certificates.k8s.io/sa-namespace-admin approved

┌─[ 6:57][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl get csr
NAME                 AGE     SIGNERNAME                            REQUESTOR      REQUESTEDDURATION   CONDITION
sa-namespace-admin   2m11s   kubernetes.io/kube-apiserver-client   system:admin   &lt;none&gt;              Approved,Issued

┌─[ 6:57][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl get csr sa-namespace-admin -o jsonpath=&apos;{.status.certificate}&apos;  | base64 -d &gt; sa-namespace-admin.crt
</pre>

<pre>
┌─[ 6:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt; ./sa-namespace-admin.yaml

┌─[ 6:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create serviceaccount sa-namespace-admin --namespace=&quot;default&quot; -o yaml --dry-run=client &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 6:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 7:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create role sa-namespace-admin-role --verb=* --resource=* --template=* --dry-run=client -o yaml &gt;&gt; ./sa-namespace-admin.yaml 

┌─[ 6:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 6:49][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create clusterrolebinding sa-namespace-admin --clusterrole=cluster-admin --serviceaccount=default:sa-namespace-admin --dry-run=client -o yaml &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 6:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ cat ./sa-namespace-admin.yaml 
---
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: null
  name: sa-namespace-admin
  namespace: default
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  creationTimestamp: null
  name: sa-namespace-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: sa-namespace-admin
  namespace: default

┌─[ 6:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./sa-namespace-admin.yaml 
serviceaccount/sa-namespace-admin created
clusterrolebinding.rbac.authorization.k8s.io/sa-namespace-admin created

┌─[ 7:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-credentials sa-namespace-admin --client-key=sa-namespace-admin.key --client-certificate=sa-namespace-admin.crt 
User &quot;sa-namespace-admin&quot; set.

┌─[ 7:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-context sa-namespace-admin --cluster=default --user=sa-namespace-admin
Context &quot;sa-namespace-admin&quot; created.
</pre>

Okaaaay. Ler's test it.

<pre>┌─[ 7:08][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config use-context sa-namespace-admin
Switched to context &quot;sa-namespace-admin&quot;.
</pre>

