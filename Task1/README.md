Here we are about to go through the task1 at https://github.com/LinPin7994/education/tree/main/task_1
and try to note all the process here. Step by step.

To be honest, I didn't meet Requirements part and used k3s instead of Minikube because 
I work on my laptop ant it doesn't have enough memory to run all the stuff.
So I decided to bring a k3s service on my desktop PC and ... control it from my laptop. 
So, it is pretty primitive to install k3s. Just do a couple of bash commands from https://k3s.io
and you done :)
But then to be able to administer it from my laptop I installed `kubectl` from the manual https://kubernetes.io/docs/tasks/tools/
and copied `/etc/rancher/k3s/k3s.yaml` from my desktop computer as `~/.kube/config` and that's it!

# Task1.1

>┌─[10:21][][alexLaptop]  
├─[~]  
└─▪ kubectl version --client  
Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.2", GitCommit:"8b5a19147530eaac9476b0ab82980b4088bbc1b2", GitTreeState:"clean", BuildDate:"2021-09-15T21:38:50Z", GoVersion:"go1.16.8", Compiler:"gc", Platform:"linux/amd64"}


>┌─[10:21][][alexLaptop]  
├─[~]  
└─▪ kubectl cluster-info  
Kubernetes control plane is running at https://homepc.baklanov.net:6443  
CoreDNS is running at https://homepc.baklanov.net:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

> ┌─[10:24][][alexLaptop]  
├─[~]  
└─▪ kubectl get nodes  
NAME     STATUS   ROLES                  AGE   VERSION  
homepc   Ready    control-plane,master   23h   v1.22.5+k3s1


>┌─[10:26][][alexLaptop]  
├─[~]  
└─▪ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml  
namespace/kubernetes-dashboard created  
serviceaccount/kubernetes-dashboard created  
service/kubernetes-dashboard created  
secret/kubernetes-dashboard-certs created  
secret/kubernetes-dashboard-csrf created  
secret/kubernetes-dashboard-key-holder created  
configmap/kubernetes-dashboard-settings created  
role.rbac.authorization.k8s.io/kubernetes-dashboard created  
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created  
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created  
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created  
deployment.apps/kubernetes-dashboard created  
service/dashboard-metrics-scraper created  
Warning: spec.template.metadata.annotations[seccomp.security.alpha.kubernetes.io/pod]: deprecated since v1.19; use the "seccompProfile" field instead  
deployment.apps/dashboard-metrics-scraper created


>┌─[10:29][][alexLaptop]  
├─[~]  
└─▪ kubectl get pod -n kubernetes-dashboard  
NAME                                         READY   STATUS    RESTARTS   AGE  
dashboard-metrics-scraper-856586f554-nwjnn   1/1     Running   0          2m12s  
kubernetes-dashboard-67484c44f6-cwjsb        1/1     Running   0          2m12s  


>┌─[10:30][][alexLaptop]  
├─[~]  
└─▪ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml  
Warning: resource serviceaccounts/metrics-server is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
serviceaccount/metrics-server configured  
Warning: resource clusterroles/system:aggregated-metrics-reader is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader configured  
Warning: resource clusterroles/system:metrics-server is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
clusterrole.rbac.authorization.k8s.io/system:metrics-server configured  
Warning: resource rolebindings/metrics-server-auth-reader is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader configured  
Warning: resource clusterrolebindings/metrics-server:system:auth-delegator is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator configured  
Warning: resource clusterrolebindings/system:metrics-server is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server configured  
Warning: resource services/metrics-server is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
service/metrics-server configured  
Warning: resource deployments/metrics-server is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
deployment.apps/metrics-server configured  
Warning: resource apiservices/v1beta1.metrics.k8s.io is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.  
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io configured  


>┌─[10:32][][alexLaptop]  
├─[~]  
└─▪ kubectl edit -n kube-system deployment metrics-server  
deployment.apps/metrics-server edited

>┌─[ 7:12][][alexLaptop]  
├─[~]  
└─▪ kubectl describe sa -n kube-system default  
Name:                default  
Namespace:           kube-system  
Labels:              <none>  
Annotations:         <none>  
Image pull secrets:  <none>  
Mountable secrets:   default-token-zw62s  
Tokens:              default-token-zw62s  
Events:              <none>  


> ┌─[10:33][][alexLaptop]  
├─[~/dashboard]  
└─▪ kubectl get secrets -n kube-system $(kubectl describe sa -n kube-system default|grep Tokens|awk '{print $2}') -o yaml|grep -E "^[[:space:]]*token:"|awk '{print $2}'|base64 -d  
eyJhbGciOiJSUzI1NiIsImtpZCI6IkRFRXRDSW85QnBsaW1lRWtKUm5TRThSd1lxdTdld0o5ZkpnVFhlSWlKSFEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJyZWFkLW9ubHktdXNlci10b2tlbi1jMnJ6cyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJyZWFkLW9ubHktdXNlciIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjFlYjkzYzY5LTNkMzgtNDQxNC1iMTExLWM2YzZhMmY4Njc3NiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDpyZWFkLW9ubHktdXNlciJ9.b5Wpb4ogCFS__vmyTduRkjUdWfc_NdriBPNtCWWBqrLUbmD8CbBuJRKPGPOyqfNIHnEnwQWewNOfAuSB5_fsgvB43soUo2K6Se_UOhTzdhoGqA6DP3VTnJKT8rboHOo7Yn-nanS6RmHx9NNA8VTpIphWA6oCqRI6ZIxxn6qW9KN0KptP0d26JFtjOzNshSQVAbZqeeUWSCNgW2n-2JIMdcQ0oSUtoQ0GQ6hAQbPFkrbJBtMCGJZyXe9YcSrgXDcdT0IDbaOfDPy87UIDSrL6cQ5lq3OoapVt4GRavz_6jrosOm-si4kMAPKM9WsDzhp2tM-xEQOQ


>┌─[10:34][][alexLaptop]  
├─[~]  
└─▪ kubectl proxy  
Starting to serve on 127.0.0.1:8001


Okay. Here I shall say, that this console, where I ran the proxy command halted running the process, so to continue 
administration I started another console window.

Open a browser and go to the URL http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
to open the Kubernetes dashboard.

First you'll see the login window where you shall paste the hash you got on the pre-previous step.

Then you just have to observe a web-page like this:  
![web page shall look like](https://github.com/AleksanderBaklanov/devops-school-kubernetes/raw/main/Task1/Screenshot_2022-01-27_23-05-08.png)

# Task1.2

>┌─[11:00][][alexLaptop]  
├─[~]  
└─▪ kubectl run web --image=nginx:latest  
pod/web created

>┌─[11:01][][alexLaptop]  
├─[~]  
└─▪ kubectl get pods  
NAME   READY   STATUS    RESTARTS   AGE  
web    1/1     Running   0          29s

But when you'll try to do next two steps from the Task1.2, you'll find NOTHING!!!
>┌─[11:10][][alexLaptop]  
├─[~]  
└─▪ ssh homepc.baklanov.net  
alexander@192.168.44.99's password:  
Last login: Thu Jan 27 20:42:43 2022 from 192.168.44.103  
alexander@HomePC:~$ sudo -i  
[sudo] password for alexander:                
root@HomePC:~# docker container ls  
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES  
root@HomePC:~# 

And it is because k3s service use `crioctl` service. So to show all running containers on host we use
>root@HomePC:~# crictl pods  
POD ID              CREATED             STATE               NAME                                         NAMESPACE              ATTEMPT             RUNTIME  
f3b2c41ff0230       2 minutes ago       Ready               web                                          default                0                   (default)  
a479f27ee1b7a       19 minutes ago      Ready               metrics-server-54894c7776-fx2xp              kube-system            0                   (default)  
dbe6728a02416       19 minutes ago      Ready               kubernetes-dashboard-67484c44f6-cwjsb        kubernetes-dashboard   0                   (default)  
678cd4de362fe       19 minutes ago      Ready               dashboard-metrics-scraper-856586f554-nwjnn   kubernetes-dashboard   0                   (default)  
9c8df9f3139d1       23 minutes ago      Ready               coredns-85cb69466-hp4pg                      kube-system            1                   (default)  
c983b8ac037e7       23 minutes ago      Ready               svclb-traefik-29jzv                          kube-system            1                   (default)  
61637642004b9       23 minutes ago      Ready               local-path-provisioner-64ffb68fd-kwwmc       kube-system            1                   (default)  
2b7c33f65739c       23 minutes ago      Ready               traefik-786ff64748-lv9xc                     kube-system            1                   (default)  
root@HomePC:~#  


>┌─[11:22][][alexLaptop][±][main S:3 U:4 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task1]  
└─▪ kubectl apply -f pod.yaml  
pod/nginx created


>┌─[11:23][][alexLaptop][±][main S:3 U:4 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task1]  
└─▪ kubectl apply -f rs.yaml  
replicaset.apps/webreplica created  


>┌─[11:23][][alexLaptop][±][main S:3 U:4 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task1]  
└─▪ kubectl get pod  
NAME               READY   STATUS    RESTARTS   AGE  
web                1/1     Running   0          23m  
nginx              1/1     Running   0          71s  
webreplica-fczwl   1/1     Running   0          33s  

# So to speak about Homework...
>┌─[ 7:40][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl apply -f ./Task1/nginx-deployment.yaml  
deployment.apps/nginx created  

>┌─[ 7:42][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl get deployments  
NAME    READY   UP-TO-DATE   AVAILABLE   AGE  
nginx   1/1     1            1           2m11s  

>┌─[ 7:46][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl edit deployment nginx  
deployment.apps/nginx edited  

find the line `replicas: 1` and change it to `replicas: 2`
then you should see 

>┌─[ 7:47][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl get deployments  
NAME    READY   UP-TO-DATE   AVAILABLE   AGE  
nginx   2/2     2            2           7m57s  

so loging in to the k3s host and list running pods:
>root@HomePC:~# crictl ps  
CONTAINER           IMAGE               CREATED             STATE               NAME                        ATTEMPT             POD ID  
f738612cc72db       c316d5a335a5c       4 minutes ago       Running             nginx                       0                   858519a0fbfb2  
9c40217b4e34b       c316d5a335a5c       10 minutes ago      Running             nginx                       0                   1876dd12d3e83  
d6ef7161ff9f3       c316d5a335a5c       44 minutes ago      Running             nginx                       1                   606847294ae7b  
ab338c1ea6453       c316d5a335a5c       44 minutes ago      Running             web                         1                   46a0a0dcfd3d1  
487e5f680310b       c316d5a335a5c       44 minutes ago      Running             nginx                       1                   772c5f59af01a  
f90e73c8565d6       746788bcc27e2       44 minutes ago      Running             lb-port-443                 2                   dcfec6dbc5b18  
f2b7fa0bce020       746788bcc27e2       44 minutes ago      Running             lb-port-80                  2                   dcfec6dbc5b18  
ccbe469b3d914       e1482a24335a6       44 minutes ago      Running             kubernetes-dashboard        5                   ee4194ab3b49d  
d4f0f188669eb       8d147537fb7d1       44 minutes ago      Running             coredns                     2                   06115f8a4e81d  
635a5bc0ac244       3c1baa65c3430       44 minutes ago      Running             traefik                     2                   1535e2bcfd748  
575a65f67107b       48d79e554db69       44 minutes ago      Running             dashboard-metrics-scraper   1                   6e625f2e099d0  
f1aff23e3b48d       933989e1174c2       44 minutes ago      Running             local-path-provisioner      7                   1f1f61f0389f6  
ff4fda5fc24b7       5787924fe1d8e       44 minutes ago      Running             metrics-server              1                   def94d860bfe8  

Then just kill one pod
>root@HomePC:~# crictl rm -f f738612cc72db  
f738612cc72db  

and see what happens then:
>┌─[ 7:57][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl get deployments  
NAME    READY   UP-TO-DATE   AVAILABLE   AGE  
nginx   1/2     2            1           15m  

and in a minute it's up again:
>┌─[ 7:59][][alexLaptop][±][main S:4 U:5 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes]  
└─▪ kubectl get deployments  
NAME    READY   UP-TO-DATE   AVAILABLE   AGE  
nginx   2/2     2            2           16m  

Yeeeeeha! We made it.  
We just have finished out first homework.
