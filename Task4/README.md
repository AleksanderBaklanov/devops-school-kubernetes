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
      - ''
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
      - ''
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
┌─[ 2:17][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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
  - ''
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

┌─[ 2:20][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./prod_view_role.yaml 
role.rbac.authorization.k8s.io/deploy_view_role created
rolebinding.rbac.authorization.k8s.io/prod_view created
</pre>

Check permissions for created prod_view user

<pre>
┌─[ 2:25][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i get deployments --namespace prod --as prod_view
yes

┌─[ 2:26][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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

┌─[ 4:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4</font>]
└─▪ kubectl get csr
NAME   AGE   SIGNERNAME                            REQUESTOR      REQUESTEDDURATION   CONDITION
john   13s   kubernetes.io/kube-apiserver-client   system:admin   &lt;none&gt;              Pending

┌─[ 4:06][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:6 <font color="#EF2929"><b>✗</b></font>]
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
  - ''
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

┌─[ 4:17][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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
<pre>┌─[ 4:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl auth can-i get deployments --namespace prod --as prod_admin
yes

┌─[ 4:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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

┌─[ 4:25][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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
┌─[ 7:00][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt; ./sa-namespace-admin.yaml

┌─[ 7:01][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create serviceaccount sa-namespace-admin --namespace=&quot;default&quot; -o yaml --dry-run=client &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 7:01][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 7:03][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create role sa-namespace-admin-role --verb=* --resource=* --template=* --dry-run=client -o yaml &gt;&gt; ./sa-namespace-admin.yaml 

┌─[ 7:03][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ echo "---" &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 7:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl create clusterrolebinding sa-namespace-admin --clusterrole=cluster-admin --serviceaccount=default:sa-namespace-admin --dry-run=client -o yaml &gt;&gt; ./sa-namespace-admin.yaml

┌─[ 7:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
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
kind: RoleBinding
metadata:
  creationTimestamp: null
  name: sa-namespace-admin-rb
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: sa-namespace-admin
  namespace: default


┌─[ 7:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl apply -f ./sa-namespace-admin.yaml 
serviceaccount/sa-namespace-admin created
clusterrolebinding.rbac.authorization.k8s.io/sa-namespace-admin created

┌─[ 7:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-credentials sa-namespace-admin --client-key=sa-namespace-admin.key --client-certificate=sa-namespace-admin.crt 
User &quot;sa-namespace-admin&quot; set.

┌─[ 7:07][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task4/homework</font>]
└─▪ kubectl config set-context sa-namespace-admin --cluster=default --user=sa-namespace-admin
Context &quot;sa-namespace-admin&quot; created.
</pre>

Okaaaay. Ler's test it.  
First let's start a pod with a default service account:
<pre>
┌─[ 7:15][][<font color="#4E9A06">alexLaptop</font>]
├─[<font color="#06989A">~</font>]
└─▪ kubectl run -i --tty --rm ca-test-pod --image=radial/busyboxplus:curl
If you don&apos;t see a command prompt, try pressing enter.
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> ls -l /var/run/secrets/kubernetes.io/serviceaccount
total 0
lrwxrwxrwx    1 root     root          13 Feb 13 10:16 <font color="#34E2E2"><b>ca.crt</b></font> -&gt; ..data/ca.crt
lrwxrwxrwx    1 root     root          16 Feb 13 10:16 <font color="#34E2E2"><b>namespace</b></font> -&gt; ..data/namespace
lrwxrwxrwx    1 root     root          12 Feb 13 10:16 <font color="#34E2E2"><b>token</b></font> -&gt; ..data/token
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> curl -k https://kubernetes
{
  &quot;kind&quot;: &quot;Status&quot;,
  &quot;apiVersion&quot;: &quot;v1&quot;,
  &quot;metadata&quot;: {
    
  },
  &quot;status&quot;: &quot;Failure&quot;,
  &quot;message&quot;: &quot;Unauthorized&quot;,
  &quot;reason&quot;: &quot;Unauthorized&quot;,
  &quot;code&quot;: 401
}
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> CERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> curl --cacert $CERT -H &quot;Authorization: Bearer $TOKEN&quot; &quot;https://kubernetes/api/v1/namespaces/d
efault/pods/&quot;
{
  &quot;kind&quot;: &quot;Status&quot;,
  &quot;apiVersion&quot;: &quot;v1&quot;,
  &quot;metadata&quot;: {
    
  },
  &quot;status&quot;: &quot;Failure&quot;,
  &quot;message&quot;: &quot;pods is forbidden: User \&quot;system:serviceaccount:default:default\&quot; cannot list resource \&quot;pods\&quot; in API group \&quot;\&quot; in the namespace \&quot;default\&quot;&quot;,
  &quot;reason&quot;: &quot;Forbidden&quot;,
  &quot;details&quot;: {
    &quot;kind&quot;: &quot;pods&quot;
  },
  &quot;code&quot;: 403
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> exit
</pre>

Then, let's start a pod with previously created service account sa-namespace-admin and check what it will return

<pre>┌─[ 7:20][][<font color="#4E9A06">alexLaptop</font>]
├─[<font color="#06989A">~</font>]
└─▪ kubectl run -i --tty --rm ca-test-pod --image=radial/busyboxplus:curl --overrides=&apos;{ &quot;spec&quot;: { &quot;serviceAccount&quot;: &quot;sa-namespace-admin&quot; }  }&apos; sh
If you don&apos;t see a command prompt, try pressing enter.
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> CERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> curl --cacert $CERT -H &quot;Authorization: Bearer $TOKEN&quot; &quot;https://kubernetes/api/v1/namespaces/default/pods/&quot;
{
  &quot;kind&quot;: &quot;PodList&quot;,
  &quot;apiVersion&quot;: &quot;v1&quot;,
  &quot;metadata&quot;: {
    &quot;resourceVersion&quot;: &quot;18236&quot;
  },
  &quot;items&quot;: [
    {
      &quot;metadata&quot;: {
        &quot;name&quot;: &quot;ca-test-pod&quot;,
        &quot;namespace&quot;: &quot;default&quot;,
        &quot;uid&quot;: &quot;c8bfe601-04d2-4ed0-b9f5-31ea97cc20c6&quot;,
        &quot;resourceVersion&quot;: &quot;18210&quot;,
        &quot;creationTimestamp&quot;: &quot;2022-02-13T10:42:08Z&quot;,
        &quot;labels&quot;: {
          &quot;run&quot;: &quot;ca-test-pod&quot;
        },
        &quot;managedFields&quot;: [
          {
            &quot;manager&quot;: &quot;kubectl-run&quot;,
            &quot;operation&quot;: &quot;Update&quot;,
            &quot;apiVersion&quot;: &quot;v1&quot;,
            &quot;time&quot;: &quot;2022-02-13T10:42:08Z&quot;,
            &quot;fieldsType&quot;: &quot;FieldsV1&quot;,
            &quot;fieldsV1&quot;: {&quot;f:metadata&quot;:{&quot;f:labels&quot;:{&quot;.&quot;:{},&quot;f:run&quot;:{}}},&quot;f:spec&quot;:{&quot;f:containers&quot;:{&quot;k:{\&quot;name\&quot;:\&quot;ca-test-pod\&quot;}&quot;:{&quot;.&quot;:{},&quot;f:args&quot;:{},&quot;f:image&quot;:{},&quot;f:imagePullPolicy&quot;:{},&quot;f:name&quot;:{},&quot;f:resources&quot;:{},&quot;f:stdin&quot;:{},&quot;f:stdinOnce&quot;:{},&quot;f:terminationMessagePath&quot;:{},&quot;f:terminationMessagePolicy&quot;:{},&quot;f:tty&quot;:{}}},&quot;f:dnsPolicy&quot;:{},&quot;f:enableServiceLinks&quot;:{},&quot;f:restartPolicy&quot;:{},&quot;f:schedulerName&quot;:{},&quot;f:securityContext&quot;:{},&quot;f:serviceAccount&quot;:{},&quot;f:serviceAccountName&quot;:{},&quot;f:terminationGracePeriodSeconds&quot;:{}}}
          },
          {
            &quot;manager&quot;: &quot;k3s&quot;,
            &quot;operation&quot;: &quot;Update&quot;,
            &quot;apiVersion&quot;: &quot;v1&quot;,
            &quot;time&quot;: &quot;2022-02-13T10:42:09Z&quot;,
            &quot;fieldsType&quot;: &quot;FieldsV1&quot;,
            &quot;fieldsV1&quot;: {&quot;f:status&quot;:{&quot;f:conditions&quot;:{&quot;k:{\&quot;type\&quot;:\&quot;ContainersReady\&quot;}&quot;:{&quot;.&quot;:{},&quot;f:lastProbeTime&quot;:{},&quot;f:lastTransitionTime&quot;:{},&quot;f:status&quot;:{},&quot;f:type&quot;:{}},&quot;k:{\&quot;type\&quot;:\&quot;Initialized\&quot;}&quot;:{&quot;.&quot;:{},&quot;f:lastProbeTime&quot;:{},&quot;f:lastTransitionTime&quot;:{},&quot;f:status&quot;:{},&quot;f:type&quot;:{}},&quot;k:{\&quot;type\&quot;:\&quot;Ready\&quot;}&quot;:{&quot;.&quot;:{},&quot;f:lastProbeTime&quot;:{},&quot;f:lastTransitionTime&quot;:{},&quot;f:status&quot;:{},&quot;f:type&quot;:{}}},&quot;f:containerStatuses&quot;:{},&quot;f:hostIP&quot;:{},&quot;f:phase&quot;:{},&quot;f:podIP&quot;:{},&quot;f:podIPs&quot;:{&quot;.&quot;:{},&quot;k:{\&quot;ip\&quot;:\&quot;10.42.0.15\&quot;}&quot;:{&quot;.&quot;:{},&quot;f:ip&quot;:{}}},&quot;f:startTime&quot;:{}}},
            &quot;subresource&quot;: &quot;status&quot;
          }
        ]
      },
      &quot;spec&quot;: {
        &quot;volumes&quot;: [
          {
            &quot;name&quot;: &quot;kube-api-access-g4b9p&quot;,
            &quot;projected&quot;: {
              &quot;sources&quot;: [
                {
                  &quot;serviceAccountToken&quot;: {
                    &quot;expirationSeconds&quot;: 3607,
                    &quot;path&quot;: &quot;token&quot;
                  }
                },
                {
                  &quot;configMap&quot;: {
                    &quot;name&quot;: &quot;kube-root-ca.crt&quot;,
                    &quot;items&quot;: [
                      {
                        &quot;key&quot;: &quot;ca.crt&quot;,
                        &quot;path&quot;: &quot;ca.crt&quot;
                      }
                    ]
                  }
                },
                {
                  &quot;downwardAPI&quot;: {
                    &quot;items&quot;: [
                      {
                        &quot;path&quot;: &quot;namespace&quot;,
                        &quot;fieldRef&quot;: {
                          &quot;apiVersion&quot;: &quot;v1&quot;,
                          &quot;fieldPath&quot;: &quot;metadata.namespace&quot;
                        }
                      }
                    ]
                  }
                }
              ],
              &quot;defaultMode&quot;: 420
            }
          }
        ],
        &quot;containers&quot;: [
          {
            &quot;name&quot;: &quot;ca-test-pod&quot;,
            &quot;image&quot;: &quot;radial/busyboxplus:curl&quot;,
            &quot;args&quot;: [
              &quot;sh&quot;
            ],
            &quot;resources&quot;: {
              
            },
            &quot;volumeMounts&quot;: [
              {
                &quot;name&quot;: &quot;kube-api-access-g4b9p&quot;,
                &quot;readOnly&quot;: true,
                &quot;mountPath&quot;: &quot;/var/run/secrets/kubernetes.io/serviceaccount&quot;
              }
            ],
            &quot;terminationMessagePath&quot;: &quot;/dev/termination-log&quot;,
            &quot;terminationMessagePolicy&quot;: &quot;File&quot;,
            &quot;imagePullPolicy&quot;: &quot;IfNotPresent&quot;,
            &quot;stdin&quot;: true,
            &quot;stdinOnce&quot;: true,
            &quot;tty&quot;: true
          }
        ],
        &quot;restartPolicy&quot;: &quot;Always&quot;,
        &quot;terminationGracePeriodSeconds&quot;: 30,
        &quot;dnsPolicy&quot;: &quot;ClusterFirst&quot;,
        &quot;serviceAccountName&quot;: &quot;sa-namespace-admin&quot;,
        &quot;serviceAccount&quot;: &quot;sa-namespace-admin&quot;,
        &quot;nodeName&quot;: &quot;homepc&quot;,
        &quot;securityContext&quot;: {
          
        },
        &quot;schedulerName&quot;: &quot;default-scheduler&quot;,
        &quot;tolerations&quot;: [
          {
            &quot;key&quot;: &quot;node.kubernetes.io/not-ready&quot;,
            &quot;operator&quot;: &quot;Exists&quot;,
            &quot;effect&quot;: &quot;NoExecute&quot;,
            &quot;tolerationSeconds&quot;: 300
          },
          {
            &quot;key&quot;: &quot;node.kubernetes.io/unreachable&quot;,
            &quot;operator&quot;: &quot;Exists&quot;,
            &quot;effect&quot;: &quot;NoExecute&quot;,
            &quot;tolerationSeconds&quot;: 300
          }
        ],
        &quot;priority&quot;: 0,
        &quot;enableServiceLinks&quot;: true,
        &quot;preemptionPolicy&quot;: &quot;PreemptLowerPriority&quot;
      },
      &quot;status&quot;: {
        &quot;phase&quot;: &quot;Running&quot;,
        &quot;conditions&quot;: [
          {
            &quot;type&quot;: &quot;Initialized&quot;,
            &quot;status&quot;: &quot;True&quot;,
            &quot;lastProbeTime&quot;: null,
            &quot;lastTransitionTime&quot;: &quot;2022-02-13T10:42:08Z&quot;
          },
          {
            &quot;type&quot;: &quot;Ready&quot;,
            &quot;status&quot;: &quot;True&quot;,
            &quot;lastProbeTime&quot;: null,
            &quot;lastTransitionTime&quot;: &quot;2022-02-13T10:42:09Z&quot;
          },
          {
            &quot;type&quot;: &quot;ContainersReady&quot;,
            &quot;status&quot;: &quot;True&quot;,
            &quot;lastProbeTime&quot;: null,
            &quot;lastTransitionTime&quot;: &quot;2022-02-13T10:42:09Z&quot;
          },
          {
            &quot;type&quot;: &quot;PodScheduled&quot;,
            &quot;status&quot;: &quot;True&quot;,
            &quot;lastProbeTime&quot;: null,
            &quot;lastTransitionTime&quot;: &quot;2022-02-13T10:42:08Z&quot;
          }
        ],
        &quot;hostIP&quot;: &quot;192.168.44.110&quot;,
        &quot;podIP&quot;: &quot;10.42.0.15&quot;,
        &quot;podIPs&quot;: [
          {
            &quot;ip&quot;: &quot;10.42.0.15&quot;
          }
        ],
        &quot;startTime&quot;: &quot;2022-02-13T10:42:08Z&quot;,
        &quot;containerStatuses&quot;: [
          {
            &quot;name&quot;: &quot;ca-test-pod&quot;,
            &quot;state&quot;: {
              &quot;running&quot;: {
                &quot;startedAt&quot;: &quot;2022-02-13T10:42:09Z&quot;
              }
            },
            &quot;lastState&quot;: {
              
            },
            &quot;ready&quot;: true,
            &quot;restartCount&quot;: 0,
            &quot;image&quot;: &quot;docker.io/radial/busyboxplus:curl&quot;,
            &quot;imageID&quot;: &quot;sha256:4776f1f7d1f625c8c5173a969fdc9ae6b62655a2746aba989784bb2b7edbfe9b&quot;,
            &quot;containerID&quot;: &quot;containerd://d93125edabb96ae11619bb9c35efa98b7d5c4bad4a770423c29794e11ff6cedf&quot;,
            &quot;started&quot;: true
          }
        ],
        &quot;qosClass&quot;: &quot;BestEffort&quot;
      }
    }
  ]
}<span style="background-color:#2E3436"><font color="#3465A4">[ </font></span><span style="background-color:#2E3436"><font color="#C4A000">root@ca-test-pod:</font></span><span style="background-color:#2E3436"><font color="#4E9A06">/</font></span><span style="background-color:#2E3436"><font color="#3465A4"> ]$</font></span> 
</pre>

And what let us get such info from API ?
Ofcourse our service account which we can see getting info about the running pod `ca-test-pod` from another console.

<pre>
┌─[ 7:28][][<font color="#4E9A06">alexLaptop</font>]
├─[<font color="#06989A">~</font>]
└─▪ kubectl get pod ca-test-pod -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: &quot;2022-02-13T10:42:08Z&quot;
  labels:
    run: ca-test-pod
  name: ca-test-pod
  namespace: default
  resourceVersion: &quot;18210&quot;
  uid: c8bfe601-04d2-4ed0-b9f5-31ea97cc20c6
spec:
  containers:
  - args:
    - sh
    image: radial/busyboxplus:curl
    imagePullPolicy: IfNotPresent
    name: ca-test-pod
    resources: {}
    stdin: true
    stdinOnce: true
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    tty: true
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-g4b9p
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: homepc
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: sa-namespace-admin
  serviceAccountName: sa-namespace-admin
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-g4b9p
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: &quot;2022-02-13T10:42:08Z&quot;
    status: &quot;True&quot;
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: &quot;2022-02-13T10:42:09Z&quot;
    status: &quot;True&quot;
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: &quot;2022-02-13T10:42:09Z&quot;
    status: &quot;True&quot;
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: &quot;2022-02-13T10:42:08Z&quot;
    status: &quot;True&quot;
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://d93125edabb96ae11619bb9c35efa98b7d5c4bad4a770423c29794e11ff6cedf
    image: docker.io/radial/busyboxplus:curl
    imageID: sha256:4776f1f7d1f625c8c5173a969fdc9ae6b62655a2746aba989784bb2b7edbfe9b
    lastState: {}
    name: ca-test-pod
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: &quot;2022-02-13T10:42:09Z&quot;
  hostIP: 192.168.44.110
  phase: Running
  podIP: 10.42.0.15
  podIPs:
  - ip: 10.42.0.15
  qosClass: BestEffort
  startTime: &quot;2022-02-13T10:42:08Z&quot;

</pre>

Aaaaaand - this is it.  
serviceAccount: sa-namespace-admin  
serviceAccountName: sa-namespace-admin  

Okay. Suppose this is it.
Service account created, and tested getting pods with API request from a pod with the custom service account.
