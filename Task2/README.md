# Task 2
Going through the Task2 - https://github.com/LinPin7994/education/tree/main/task_2

### ConfigMap & Secrets  
<pre>┌─[11:11][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl create secret generic connection-string --from-literal=DATABASE_URL=postgres://connect --dry-run=client -o yaml > secret.yaml  

┌─[11:11][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl create configmap user --from-literal=firstname=firstname --from-literal=lastname=lastname --dry-run=client -o yaml > cm.yaml  

┌─[11:12][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f secret.yaml  
secret/connection-string created  

┌─[11:12][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f cm.yaml  
configmap/user created  

┌─[11:17][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f pod.yaml  
pod/nginx created  
</pre>

### Check env in pod
<pre>┌─[11:20][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl exec -ti nginx -- bash  
root@nginx:/# printenv  
KUBERNETES_SERVICE_PORT_HTTPS=443  
KUBERNETES_SERVICE_PORT=443  
**DATABASE_URL=postgres://connect**  
HOSTNAME=nginx  
PWD=/  
PKG_RELEASE=1~bullseye  
HOME=/root  
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443  
NJS_VERSION=0.7.2  
TERM=xterm  
SHLVL=1  
KUBERNETES_PORT_443_TCP_PROTO=tcp  
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1  
lastname=lastname  
KUBERNETES_SERVICE_HOST=10.43.0.1  
KUBERNETES_PORT=tcp://10.43.0.1:443  
KUBERNETES_PORT_443_TCP_PORT=443  
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin  
firstname=firstname  
NGINX_VERSION=1.21.6  
_=/usr/bin/printenv  
root@nginx:/# exit  
exit  
</pre>

### Create deployment with simple application
<pre>┌─[11:25][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f nginx-configmap.yaml  
configmap/nginx-configmap created  

┌─[11:25][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f deployment.yaml  
deployment.apps/web created  

### Get pod ip address
┌─[11:25][][alexLaptop][±][main S:7 U:7 ?:2 ✗]      
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]      
└─▪ kubectl get pods -o wide  
<table style="border: 0px solid transparent">  
  <th>NAME</th><th>READY</th><th>STATUS</th><th>RESTARTS</th><th>AGE</th><th>IP</th><th>NODE</th><th>NOMINATED NODE</th><th>READINESS GATES</th>    
  <tr>
    <td>nginx</td><td>1/1</td><td>Running</td><td>1 (71m ago)</td><td>9h</td><td>10.42.0.87</td><td>homepc</td><td><none></td><td><none></td>
  </tr>  
  <tr>
    <td>web-5584c6c5c6-fpjw5</td><td>1/1</td><td>Running</td><td>0</td><td>2m10s</td><td>10.42.0.94</td><td>homepc</td><td><none></td><td><none></td>
  </tr>  
    <tr><td>web-5584c6c5c6-mvkws</td><td>1/1</td><td>Running</td><td>0</td><td>2m10s</td><td>10.42.0.95</td><td>homepc</td><td><none></td><td><none></td>
  </tr>  
  <tr>
    <td>web-5584c6c5c6-kcrvq</td><td>1/1</td><td>Running</td><td>0</td><td>2m10s</td><td>10.42.0.96</td><td>homepc</td><td><none></td><td><none></td>
  </tr>  
</table>  

┌─[11:25][][alexLaptop][±][main S:7 U:7 ?:2 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ curl 10.42.0.94  
curl: (7) Failed to connect to 10.42.0.94 port 80: No route to host  
</pre>

So. As far as we are running Kubernetes cluster on a separated host, and my router even haven't heard about such network
we have what we have. I cam't connect to the pod's address from external network.

But the host where kubernetes cluster runs know about that networks. So let's connect to the host and check it out: 

<pre>┌─[10:04][][alexLaptop][±][main S:8 U:8 ?:5 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ ssh homepc.baklanov.net  
alexander@192.168.44.99's password:  
Last login: Sun Jan 30 09:38:56 2022 from 192.168.44.103  
alexander@HomePC:~$ **curl 10.42.0.94**  
**web-5584c6c5c6-fpjw5**  
alexander@HomePC:~$  
</pre>

Tadaaaa. here we do have a response - **web-5584c6c5c6-fpjw5**   

And we also may check the connectivity between pods:

<pre>┌─[10:45][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl exec -it $(kubectl get pod |awk '{print $1}'|grep web-|head -n1) bash  
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.  
root@web-5584c6c5c6-fpjw5:/# **curl web-5584c6c5c6-kcrvq**  
**curl: (6) Could not resolve host: web-5584c6c5c6-kcrvq**  
root@web-5584c6c5c6-fpjw5:/# **curl 10.42.0.96**  
**web-5584c6c5c6-kcrvq**  
root@web-5584c6c5c6-fpjw5:/#  
</pre>

So it didn't recognize the pod wit its name.
But we had a chance to access it by its IP.

### Create service (ClusterIP)
<pre>┌─[10:48][][alexLaptop][±][main S:8 U:8 ?:6 ✗]   
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]   
└─▪ kubectl expose deployment/web --type=ClusterIP --dry-run=client -o yaml > service_template.yaml   

┌─[11:05][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ cat ./service_template.yaml  
apiVersion: v1  
kind: Service  
metadata:  
&emsp;&emsp;creationTimestamp: null  
&emsp;&emsp;labels:  
&emsp;&emsp;&emsp;&emsp;app: web    
&emsp;&emsp;name: web    
spec:  
&emsp;&emsp;ports:    
&emsp;&emsp;- port: 80  
&emsp;&emsp;&emsp;&emsp;protocol: TCP  
&emsp;&emsp;&emsp;&emsp;targetPort: 80  
&emsp;&emsp;selector:  
&emsp;&emsp;&emsp;&emsp;app: web  
&emsp;&emsp;type: ClusterIP  
status:  
&emsp;&emsp;loadBalancer: {}  

┌─[11:05][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f service_template.yaml  
service/web created  

>┌─[11:06][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl get svc  
<table style="border: 0px solid transparent">  
  <th>NAME</th><th>TYPE</th><th>CLUSTER-IP</th><th>EXTERNAL-IP</th><th>PORT(S)</th><th>AGE</th>
  <tr>
    <td>kubernetes</td><td>ClusterIP</td><td>10.43.0.1</td><td><none></td><td>443/TCP</td><td>3d12h</td>
  </tr>
  <tr>
    <td>web</td><td>ClusterIP</td><td>10.43.44.52</td><td><none></td><td>80/TCP</td><td>5m10s</td>
  </tr>
</table>
</pre>

And again, even that the cluster IP for the web is from different network (10.43.44.52) than the pod was (10.42.0.96),
But my laptop and my router still doesn't have any idea about this net. 
So I doesn't have anu connection to this cluster IP from my laptop :(

#### From my laptop
<pre>┌─[11:11][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ curl 10.43.44.52  
curl: (28) Failed to connect to 10.43.44.52 port 80: Connection timed out  
</pre>

#### From k3s host
<pre>┌─[11:23][][alexLaptop][±][main S:8 U:8 ?:6 ✗]   
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]   
└─▪ ssh homepc.baklanov.net  
alexander@192.168.44.99's password:  
Last login: Sun Jan 30 10:06:50 2022 from 192.168.44.103  
alexander@HomePC:~$ **curl 10.43.44.52**  
**web-5584c6c5c6-fpjw5**  
alexander@HomePC:~$   
</pre>

#### From another pod
<pre>┌─[11:28][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl exec -it $(kubectl get pod |awk '{print $1}'|grep web-|head -n1) -- bash  
root@web-5584c6c5c6-fpjw5:/# **curl 10.43.44.52**  
**web-5584c6c5c6-fpjw5**  
root@web-5584c6c5c6-fpjw5:/#  
</pre>

And yes. Still the same. There is connectivity from k3s host and from pods, but no access from external network.

### NodePort
<pre>┌─[11:32][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f service-nodeport.yaml  
service/web-np created  

┌─[11:32][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl get service  
<table style="border: 0px solid transparent">  
  <th>NAME</th><th>TYPE</th><th>CLUSTER-IP</th><th>EXTERNAL-IP</th><th>PORT(S)</th><th>AGE</th>
  <tr>
    <td>kubernetes</td><td>ClusterIP</td><td>10.43.0.1</td><td><none></td><td>443/TCP</td><td>3d12h</td>
  </tr>
  <tr>
    <td>web</td><td>ClusterIP</td><td>10.43.44.52</td><td><none></td><td>80/TCP</td><td>27m</td>
  </tr>
  <tr>
    <td>web-np</td><td>NodePort</td><td>10.43.28.240</td><td><none></td><td>80:31534/TCP</td><td>38s</td>
  </tr>
</table>
</pre>

### Checking the availability of the NodePort service type
<pre>┌─[11:33][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ curl homepc.baklanov.net:80  
404 page not found  

┌─[11:42][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ curl homepc.baklanov.net:31534  
web-5584c6c5c6-fpjw5  
</pre>

So from the above curls I understand that the first port in the record `80:31534/TCP` is the port of pod service
and the second is the port on the k3s host accessible from external net.

### Headless service  

<pre>
┌─[11:42][][alexLaptop][±][main S:8 U:8 ?:6 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl apply -f service-headless.yaml    

root@HomePC:~# cat /etc/resolv.conf  
# This file is managed by man:systemd-resolved(8). Do not edit.  
#  
# This is a dynamic resolv.conf file for connecting local clients to the  
# internal DNS stub resolver of systemd-resolved. This file lists all  
# configured search domains.  
#  
# Run "resolvectl status" to see details about the uplink DNS servers  
# currently in use.  
#  
# Third party programs must not access this file directly, but only through the  
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,  
# replace this symlink by a static file or a different symlink.  
#  
# See man:systemd-resolved.service(8) for details about the supported modes of  
# operation for /etc/resolv.conf.  

┌─[ 1:38][][alexLaptop][±][main S:8 U:8 ?:7 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2]  
└─▪ kubectl exec -it $(kubectl get pod |awk '{print $1}'|grep web-|head -n1) -- bash  
root@web-5584c6c5c6-fpjw5:/# cat /etc/resolv.conf  
search default.svc.cluster.local svc.cluster.local cluster.local  
nameserver 10.43.0.10  
options ndots:5  
root@web-5584c6c5c6-fpjw5:/# curl web-headless
curl: (6) Could not resolve host: web-headless
root@web-5584c6c5c6-fpjw5:/# curl web-np
web-5584c6c5c6-fpjw5
root@web-5584c6c5c6-fpjw5:/# curl web
web-5584c6c5c6-fpjw5
root@web-5584c6c5c6-fpjw5:/# 

root@HomePC:~# curl web-headless  
curl: (6) Could not resolve host: web-headless  
root@HomePC:~# curl web-np  
curl: (6) Could not resolve host: web-np  
root@HomePC:~# curl web  
curl: (6) Could not resolve host: web  
root@HomePC:~#
</pre>

Let's update repository cache 
<pre> root@web-5584c6c5c6-fpjw5:/# apt update</pre>

and install DNStools package inside a pod
<pre>
root@web-5584c6c5c6-fpjw5:/# apt install dnsutils
...
root@web-5584c6c5c6-fpjw5:/# nslookup 10.43.28.240  
240.28.43.10.in-addr.arpa       name = web-np.default.svc.cluster.local.  

root@web-5584c6c5c6-fpjw5:/# nslookup 10.43.44.52  
52.44.43.10.in-addr.arpa        name = web.default.svc.cluster.local.  

root@web-5584c6c5c6-fpjw5:/# dig web-headless  

; <<>> DiG 9.16.22-Debian <<>> web-headless  
;; global options: +cmd  
;; Got answer:  
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 6836  
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1  

;; OPT PSEUDOSECTION:  
; EDNS: version: 0, flags:; udp: 4096  
; COOKIE: dda1a82446d751f9 (echoed)  
;; QUESTION SECTION:  
;web-headless.                  IN      A    
  
;; Query time: 0 msec  
;; SERVER: 10.43.0.10#53(10.43.0.10)  
;; WHEN: Sun Jan 30 09:08:09 UTC 2022  
;; MSG SIZE  rcvd: 53  
</pre>

From the above you may understand that DNS names are available only from inter-namespace  
except headless pod which doesn't available even from inside O_O  


## Ingress

As far as I am running k3s but not minikube as it said in the task...

<pre>┌─[ 5:52][][alexLaptop]  
├─[~]  
└─▪ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml

┌─[ 5:52][][alexLaptop]  
├─[~]  
└─▪ kubectl get pods --all-namespaces -w  
NAMESPACE       NAME                                        READY   STATUS      RESTARTS   AGE  
kube-system     local-path-provisioner-84bb864455-s4v77     1/1     Running     0          27m  
kube-system     coredns-96cc4f57d-k4skf                     1/1     Running     0          27m  
kube-system     metrics-server-ff9dbcb6c-rdfmj              1/1     Running     0          27m  
ingress-nginx   ingress-nginx-admission-create--1-fkw7g     0/1     Completed   0          23m  
ingress-nginx   ingress-nginx-admission-patch--1-kcqz6      0/1     Completed   1          23m  
ingress-nginx   ingress-nginx-controller-6f5584ff89-bwz5x   1/1     Running     0          20m  
</pre>

Great, we have an ingress controller now. But, we do not have a load balancer. So we need to enable the ingress controller to use port 80 and 443 on the host. Let's patch the ingress controller.

Create the patch first:

>cat > ingress.yaml <<EOF      
spec:  
  template:  
    spec:  
      hostNetwork: true  
EOF  

<pre>┌─[ 5:52][][alexLaptop]  
├─[~]  
└─▪ kubectl patch deployment ingress-nginx-controller -n ingress-nginx --patch "$(cat ingress.yaml)"
</pre>

<pre>┌─[ 5:52][][alexLaptop]  
├─[~]  
└─▪ curl homepc.baklanov.net  
  <html>
    <head><title>404 Not Found</title></head>
    <body>
      <center><h1>404 Not Found</h1></center>
      <hr><center>nginx</center>
    </body>
  </html>
</pre>

# Homework

- In Minikube in namespace kube-system, there are many different pods running. Your task is to figure out who creates them, and who makes sure they are running (restores them after deletion).

Okay. Let's delete a pod in the kube-system namespace
<pre>
┌─[ 1:49][][alexLaptop][±][main ✓]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ kubectl get pod --namespace kube-system
NAME                                        READY   STATUS      RESTARTS        AGE
local-path-provisioner-84bb864455-s4v77     1/1     Running     1 (4h36m ago)   20h
coredns-96cc4f57d-k4skf                     1/1     Running     1 (4h36m ago)   20h
metrics-server-ff9dbcb6c-rrc6v              1/1     Running     0               20m

┌─[ 2:02][][alexLaptop][±][main ✓]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ kubectl delete pod local-path-provisioner-84bb864455-s4v77 --namespace kube-system
pod "local-path-provisioner-84bb864455-s4v77" deleted

┌─[ 2:03][][alexLaptop][±][main ✓]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ kubectl get pod --namespace kube-system
NAME                                        READY   STATUS      RESTARTS        AGE
local-path-provisioner-84bb864455-s4v77     1/1     Terminating 1 (4h36m ago)   20h
coredns-96cc4f57d-k4skf                     1/1     Running     1 (4h38m ago)   20h
metrics-server-ff9dbcb6c-rrc6v              1/1     Running     0               22m
local-path-provisioner-84bb864455-thdhf     1/1     Pending     0               1s

┌─[ 2:03][][alexLaptop][±][main ✓]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ kubectl get pod --namespace kube-system
NAME                                        READY   STATUS      RESTARTS        AGE
coredns-96cc4f57d-k4skf                     1/1     Running     1 (4h38m ago)   20h
metrics-server-ff9dbcb6c-rrc6v              1/1     Running     0               22m
local-path-provisioner-84bb864455-thdhf     1/1     Running     0               10s
</pre>

Okay. As you can see the deleted pod was successfully deleted and a new one was created.

<pre>
┌─[ 2:04][][alexLaptop][±][main U:1 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ kubectl describe pod local-path-provisioner-84bb864455-thdhf --namespace kube-system
Name:                 local-path-provisioner-84bb864455-thdhf
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 homepc/192.168.44.110
Start Time:           Sun, 06 Feb 2022 14:03:04 +0500
Labels:               app=local-path-provisioner
pod-template-hash=84bb864455
Annotations:          <none>
Status:               Running
IP:                   10.42.0.19
IPs:
IP:           10.42.0.19
Controlled By:  ReplicaSet/local-path-provisioner-84bb864455
Containers:
local-path-provisioner:
Container ID:  containerd://00aa17b9982c147bffea9040d4851b2d925886060ef86797fa3443a786451737
Image:         rancher/local-path-provisioner:v0.0.21
Image ID:      docker.io/rancher/local-path-provisioner@sha256:1da612c913ce0b4ab82e20844baa9dce1f7065e39412d6a0bb4de99c413f21bf
Port:          <none>
Host Port:     <none>
Command:
local-path-provisioner
start
--config
/etc/config/config.json
State:          Running
Started:      Sun, 06 Feb 2022 14:03:05 +0500
Ready:          True
Restart Count:  0
Environment:
POD_NAMESPACE:  kube-system (v1:metadata.namespace)
Mounts:
/etc/config/ from config-volume (rw)
/var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-7qbdk (ro)
Conditions:
Type              Status
Initialized       True
Ready             True
ContainersReady   True
PodScheduled      True
Volumes:
config-volume:
Type:      ConfigMap (a volume populated by a ConfigMap)
Name:      local-path-config
Optional:  false
kube-api-access-7qbdk:
Type:                    Projected (a volume that contains injected data from multiple sources)
TokenExpirationSeconds:  3607
ConfigMapName:           kube-root-ca.crt
ConfigMapOptional:       <nil>
DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 CriticalAddonsOnly op=Exists
node-role.kubernetes.io/control-plane:NoSchedule op=Exists
node-role.kubernetes.io/master:NoSchedule op=Exists
node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
Normal  Scheduled  16m   default-scheduler  Successfully assigned kube-system/local-path-provisioner-84bb864455-thdhf to homepc
Normal  Pulled     16m   kubelet            Container image "rancher/local-path-provisioner:v0.0.21" already present on machine
Normal  Created    16m   kubelet            Created container local-path-provisioner
Normal  Started    16m   kubelet            Started container local-path-provisioner
</pre>

Okaaay, from the description above we can see that the pod is controlled by:
Controlled By:  ReplicaSet/local-path-provisioner-84bb864455
So it was the replication set local-path-provisioner-84bb864455, I believe,  which issued to deploy another pod. O_o


- Implement Canary deployment of an application via Ingress. Traffic to canary deployment should be redirected if you add "canary:always" in the header, otherwise it should go to regular deployment. Set to redirect a percentage of traffic to canary deployment.

So let's create and apply:
1. a deployment of nginx-v1.yaml to implement a service and then we'll configure an access to it through an ingress service. 
<pre>┌─[12:17][][alexLaptop][±][main S:8 U:8 ?:10 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ cat ./nginx-v1.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-v1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      version: v1
  template:
    metadata:
      labels:
        app: nginx
        version: v1
    spec:
      containers:
      - name: nginx
        image: "openresty/openresty:centos"
        ports:
        - name: http
          protocol: TCP
          containerPort: 80
        volumeMounts:
        - mountPath: /usr/local/openresty/nginx/conf/nginx.conf
          name: config
          subPath: nginx.conf
      volumes:
      - name: config
        configMap:
          name: nginx-v1

---
apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    app: nginx
    version: v1
  name: nginx-v1
data:
  nginx.conf: |-
    worker_processes  1;

    events {
        accept_mutex on;
        multi_accept on;
        use epoll;
        worker_connections  1024;
    }

    http {
        ignore_invalid_headers off;
        server {
            listen 80;
            location / {
                access_by_lua '
                    local header_str = ngx.say("nginx-v1")
                ';
            }
        }
    }

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-v1
spec:
  type: ClusterIP
  ports:
  - port: 80
    protocol: TCP
    name: http
  selector:
    app: nginx
    version: v1
</pre>


one yaml file to implement an ingress service to redirect web traffic into the nginx-v1 service
<pre>┌─[11:49][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:8 U:8 ?:10 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment</font>]
└─▪ cat ingress.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: hello-world.net
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx-v1
                port: 
                  number: 80
</pre>

<pre>┌─[10:59][][alexLaptop][±][main S:8 U:8 ?:9 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]  
└─▪ kubectl apply -f ./ingress.yaml  
ingress.networking.k8s.io/nginx created
</pre>

And now we can check that it works.
<pre>┌─[11:00][][alexLaptop][±][main S:8 U:8 ?:9 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ curl hello-world.net
nginx-v1
</pre>

So it works :)
Okay. Let's implement another deployment of nginx:
2. a deployment of nginx-v2
<pre>
┌─[12:17][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:8 U:8 ?:10 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment</font>]
└─▪ cat ./nginx-v2.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
      version: v2
  template:
    metadata:
      labels:
        app: nginx
        version: v2
    spec:
      containers:
      - name: nginx
        image: &quot;openresty/openresty:centos&quot;
        ports:
        - name: http
          protocol: TCP
          containerPort: 80
        volumeMounts:
        - mountPath: /usr/local/openresty/nginx/conf/nginx.conf
          name: config
          subPath: nginx.conf
      volumes:
      - name: config
        configMap:
          name: nginx-v2
---

apiVersion: v1
kind: ConfigMap
metadata:
  labels:
    app: nginx
    version: v2
  name: nginx-v2
data:
  nginx.conf: |-
    worker_processes  1;

    events {
        accept_mutex on;
        multi_accept on;
        use epoll;
        worker_connections  1024;
    }

    http {
        ignore_invalid_headers off;
        server {
            listen 80;
            location / {
                access_by_lua &apos;
                    local header_str = ngx.say(&quot;nginx-v2&quot;)
                &apos;;
            }
        }
    }

---

apiVersion: v1
kind: Service
metadata:
  name: nginx-v2
spec:
  type: ClusterIP
  ports:
  - port: 80
    protocol: TCP
    name: http
  selector:
    app: nginx
    version: v2
</pre>
<pre>┌─[11:02][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:8 U:8 ?:10 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment</font>]
└─▪ cat ./canary_ingress.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: canary-nginx
  namespace: default
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/canary: &quot;true&quot;
  name: nginx-canary
spec:
  rules:
    - host: hello-world.net
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: nginx-v2
                port:
                  number: 80
</pre>

<pre>┌─[11:02][][alexLaptop][±][main S:8 U:8 ?:9 ✗]  
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]  
└─▪ kubectl apply -f ./canary_ingress.yaml  
ingress.networking.k8s.io/nginx-canary created  
</pre>

<pre>┌─[11:25][][alexLaptop][±][main S:8 U:8 ?:9 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ while true; do curl -s hello-world.net | grep nginx; sleep 1; done
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
nginx-v1
^C

┌─[11:29][][alexLaptop][±][main S:8 U:8 ?:9 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment]
└─▪ curl -H "Canary: True" hello-world.net
nginx-v2
</pre>


And now, I believe, it must be understandable, how to route traffic between different services using a headers. 
