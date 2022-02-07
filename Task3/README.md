# Task3

<pre>
┌─[ 3:47][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ cat ./pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: minio-deployment-pv
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 5Gi
  hostPath:
    path: /data/pv0001/
</pre>

<pre>┌─[ 3:49][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl apply -f pv.yaml
persistentvolume/minio-deployment-pv created
</pre>

<pre>┌─[ 3:47][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl get pv
NAME                  CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
minio-deployment-pv   5Gi        RWO            Retain           Available                                   5m57s
</pre>

<pre>─[ 3:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ cat ./pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: minio-deployment-claim
spec:
  storageClassName: &quot;&quot;
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
</pre>

<pre>┌─[ 3:52][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl apply -f pvc.yaml
persistentvolumeclaim/minio-deployment-claim created
</pre>

<pre>┌─[ 3:53][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl get pv
NAME                  CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                            STORAGECLASS   REASON   AGE
minio-deployment-pv   5Gi        RWO            Retain           Bound    default/minio-deployment-claim                           9m27s
</pre>

<pre>┌─[ 3:55][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ cat ./deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minio
  labels:
    app: minio
spec:
  replicas: 1
  selector:
    matchLabels:
      app: minio
  template:
    metadata:
      labels:
        app: minio
    spec:
      containers:
        - name: minio
          image: minio/minio
          args: [&quot;server&quot;, &quot;/data&quot;, &quot;--console-address&quot;, &quot;:9001&quot;]
          ports:
            - containerPort: 9001
          volumeMounts:
            - name: data
              mountPath: &quot;/data&quot;
              readOnly: false
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: minio-deployment-claim

</pre>

<pre>┌─[ 3:55][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl apply -f deployment.yaml
deployment.apps/minio created
</pre>

<pre>┌─[ 3:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ cat ./minio-nodeport.yaml 
apiVersion: v1
kind: Service
metadata:
  name: minio-app
spec:
  type: NodePort
  selector:
    app: minio
  ports:
    - port: 9001
      nodePort: 30008

</pre>

<pre>┌─[ 3:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl apply -f minio-nodeport.yaml
service/minio-app created
</pre>

<pre>┌─[ 3:59][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:1 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task2/canary_deployment/Task3</font>]
└─▪ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.43.0.1       &lt;none&gt;        443/TCP          22h
nginx-v2     ClusterIP   10.43.1.35      &lt;none&gt;        80/TCP           19h
nginx-v1     NodePort    10.43.203.132   &lt;none&gt;        80:32722/TCP     19h
minio-app    NodePort    10.43.154.99    &lt;none&gt;        9001:30008/TCP   2m52s
</pre>


<pre>┌─[ 4:13][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ cat ./statefulset.yaml 
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: minio-state
  labels:
    app: minio-state
spec:
  serviceName: minio-state
  replicas: 1
  selector:
    matchLabels:
      app: minio-state
  template:
    metadata:
      labels:
        app: minio-state
    spec:
      containers:
        - name: minio
          image: minio/minio
          args: [&quot;server&quot;, &quot;/data&quot;]
          ports:
            - containerPort: 9000
          volumeMounts:
            - name: minio
              mountPath: &quot;/data&quot;
              readOnly: false
  volumeClaimTemplates:
    - metadata:
        name: minio
      spec:
        accessModes: [ &quot;ReadWriteOnce&quot; ]
        resources:
          requests:
            storage: 1Gi
---
apiVersion: v1
kind: Service
metadata:
  name: minio-state
  labels:
    app: minio-state
spec:
  ports:
  - port: 9000
    name: web
  clusterIP: None
  selector:
    app: minio-state
</pre>

<pre>
┌─[ 4:14][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f statefulset.yaml 
statefulset.apps/minio-state created
service/minio-state created
</pre>

<pre>┌─[ 4:15][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get pod
NAME                        READY   STATUS    RESTARTS        AGE
nginx-v2-5f885975d5-wtj6z   1/1     Running   1 (6h51m ago)   19h
nginx-v1-79bc94ff97-9c2p4   1/1     Running   1 (6h51m ago)   19h
nginx-v1-79bc94ff97-9q66p   1/1     Running   0               5h5m
nginx-v1-79bc94ff97-6ng9t   1/1     Running   0               5h5m
minio-575d987896-qktkd      1/1     Running   0               20m
minio-state-0               1/1     Running   0               91s

</pre>

<pre>┌─[ 4:16][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:1 ?:5 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get sts
NAME          READY   AGE
minio-state   1/1     2m5s

</pre>


# Homework
## - We published minio "outside" using nodePort. Do the same but using ingress.

So, let's try to write some yaml to deploy an ingress for the Minio service
<pre>┌─[ 4:41][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ cat ./ingress.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minio-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  ingressClassName: nginx
  rules:
    - host: minio-my-minio.net
      http:
        paths:
          - path: /web(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: minio-app
                port:
                  number: 9001

</pre>

<pre>┌─[ 4:41][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f ./ingress.yaml 
ingress.networking.k8s.io/minio-ingress created
</pre>

<pre>┌─[ 4:43][][alexLaptop][±][main S:1 U:2 ?:6 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task3]
└─▪ kubectl get ingress
NAME            CLASS    HOSTS                ADDRESS          PORTS   AGE
nginx           <none>   hello-world.net      192.168.44.110   80      19h
minio-ingress   <none>   minio-my-minio.net   192.168.44.110   80      116s
nginx-canary    <none>   hello-world.net      192.168.44.110   80      36s
</pre>


## - Publish minio via ingress so that minio by ip_minikube and nginx returning hostname (previous job) by path ip_minikube/web are available at the same time.

<pre>┌─[ 4:47][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ cat ./ingress-rewrite.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/configuration-snippet: |
      rewrite ^(/web)$ $1/ redirect;
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: minio-ingress-rewrite
  namespace: default
spec:
  ingressClassName: nginx
  rules:
    - host: minio-my-minio.net
      http:
        paths:
          - path: /web(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: minio-app
                port:
                  number: 9001

┌─[ 4:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f ./ingress-web.yaml 
ingress.networking.k8s.io/minio-ingress-rewrite created

┌─[ 7:45][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:7 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get ingress
NAME                        CLASS    HOSTS                ADDRESS          PORTS   AGE
nginx                       &lt;none&gt;   hello-world.net      192.168.44.110   80      19h
nginx-canary                &lt;none&gt;   hello-world.net      192.168.44.110   80      116m
minio-ingress-web-rewrite   nginx    minio-my-minio.net   192.168.44.110   80      1m
minio-ingress               &lt;none&gt;   minio-my-minio.net   192.168.44.110   80      1m
</pre>


## - Create deploy with emptyDir save data to mountPoint emptyDir, delete pods, check data.

<pre>┌─[ 5:27][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ cat ./emptydir-deployment.yaml 
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-emptydir-deployment
spec:
  selector:
    matchLabels:
      app: nginx-emptydir
  template:
    metadata:
      labels:
        app: nginx-emptydir
    spec:
      containers:
        - name: nginx-emptydir
          image: nginx
          volumeMounts:
            - name: data
              mountPath: /data
      volumes:
        - name: data
          emptyDir: {}
      restartPolicy: Always 

</pre>

<pre>
┌─[ 5:27][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f ./emptydir-deployment.yaml
deployment.apps/nginx-emptydir-deployment created</pre>

<pre>root@HomePC:~# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                     ATTEMPT             POD ID
dc44f43fc3d8c       c316d5a335a5c       2 minutes ago       Running             nginx-emptydir           0                   61e9462282eef
900b21f5eb231       1fa835955acb3       4 hours ago         Running             minio                    0                   d3d01cbc5d04d
77347071e5028       1fa835955acb3       4 hours ago         Running             minio                    0                   a44b392df5c85
00aa17b9982c1       fb9b574e03c34       6 hours ago         Running             local-path-provisioner   0                   808b3addf6fb4
7b174a4d4f1a5       f73640fb50619       7 hours ago         Running             metrics-server           0                   a102800b1def9
37b377a481239       a9a5a499038fd       9 hours ago         Running             nginx                    0                   d9ddac421fa58
97b74702564b3       a9a5a499038fd       9 hours ago         Running             nginx                    0                   8db32cb72cc5c
f1413a0131af0       a9a5a499038fd       11 hours ago        Running             nginx                    1                   c6af628d04aa8
5359ecb99ceb6       a4ca41631cc7a       11 hours ago        Running             coredns                  1                   f2199af60af14
7d970d4bae514       a9a5a499038fd       11 hours ago        Running             nginx                    1                   6609a7a563435
30ccf2f3572af       2461b2698dcd5       11 hours ago        Running             controller               1                   8e2fd5cde637d
root@HomePC:~# 
</pre>

<pre>root@HomePC:~# crictl inspect dc44f43fc3d8c</pre>
<details>
  <summary>Spoiler</summary>
<pre>{
  &quot;status&quot;: {
    &quot;id&quot;: &quot;dc44f43fc3d8c05529da7e6166447ebfed585b08e7047142b3374e05f0004a5b&quot;,
    &quot;metadata&quot;: {
      &quot;attempt&quot;: 0,
      &quot;name&quot;: &quot;nginx-emptydir&quot;
    },
    &quot;state&quot;: &quot;CONTAINER_RUNNING&quot;,
    &quot;createdAt&quot;: &quot;2022-02-06T20:14:31.851815065+05:00&quot;,
    &quot;startedAt&quot;: &quot;2022-02-06T20:14:31.96969944+05:00&quot;,
    &quot;finishedAt&quot;: &quot;0001-01-01T00:00:00Z&quot;,
    &quot;exitCode&quot;: 0,
    &quot;image&quot;: {
      &quot;annotations&quot;: {},
      &quot;image&quot;: &quot;docker.io/library/nginx:latest&quot;
    },
    &quot;imageRef&quot;: &quot;docker.io/library/nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767&quot;,
    &quot;reason&quot;: &quot;&quot;,
    &quot;message&quot;: &quot;&quot;,
    &quot;labels&quot;: {
      &quot;io.kubernetes.container.name&quot;: &quot;nginx-emptydir&quot;,
      &quot;io.kubernetes.pod.name&quot;: &quot;nginx-emptydir-deployment-78d79b5fcc-qk5w8&quot;,
      &quot;io.kubernetes.pod.namespace&quot;: &quot;default&quot;,
      &quot;io.kubernetes.pod.uid&quot;: &quot;249f46eb-bc93-4009-bf98-9443b4545fec&quot;
    },
    &quot;annotations&quot;: {
      &quot;io.kubernetes.container.hash&quot;: &quot;7c5c2111&quot;,
      &quot;io.kubernetes.container.restartCount&quot;: &quot;0&quot;,
      &quot;io.kubernetes.container.terminationMessagePath&quot;: &quot;/dev/termination-log&quot;,
      &quot;io.kubernetes.container.terminationMessagePolicy&quot;: &quot;File&quot;,
      &quot;io.kubernetes.pod.terminationGracePeriod&quot;: &quot;30&quot;
    },
    &quot;mounts&quot;: [
      {
        &quot;containerPath&quot;: &quot;/data&quot;,
        &quot;hostPath&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~empty-dir/data&quot;,
        &quot;propagation&quot;: &quot;PROPAGATION_PRIVATE&quot;,
        &quot;readonly&quot;: false,
        &quot;selinuxRelabel&quot;: false
      },
      {
        &quot;containerPath&quot;: &quot;/var/run/secrets/kubernetes.io/serviceaccount&quot;,
        &quot;hostPath&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~projected/kube-api-access-4l6hk&quot;,
        &quot;propagation&quot;: &quot;PROPAGATION_PRIVATE&quot;,
        &quot;readonly&quot;: true,
        &quot;selinuxRelabel&quot;: false
      },
      {
        &quot;containerPath&quot;: &quot;/etc/hosts&quot;,
        &quot;hostPath&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/etc-hosts&quot;,
        &quot;propagation&quot;: &quot;PROPAGATION_PRIVATE&quot;,
        &quot;readonly&quot;: false,
        &quot;selinuxRelabel&quot;: false
      },
      {
        &quot;containerPath&quot;: &quot;/dev/termination-log&quot;,
        &quot;hostPath&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/containers/nginx-emptydir/1ef99577&quot;,
        &quot;propagation&quot;: &quot;PROPAGATION_PRIVATE&quot;,
        &quot;readonly&quot;: false,
        &quot;selinuxRelabel&quot;: false
      }
    ],
    &quot;logPath&quot;: &quot;/var/log/pods/default_nginx-emptydir-deployment-78d79b5fcc-qk5w8_249f46eb-bc93-4009-bf98-9443b4545fec/nginx-emptydir/0.log&quot;
  },
  &quot;info&quot;: {
    &quot;sandboxID&quot;: &quot;61e9462282eef43750538189368fd70e26a366fb27902747a2968b863ff09ad1&quot;,
    &quot;pid&quot;: 87381,
    &quot;removing&quot;: false,
    &quot;snapshotKey&quot;: &quot;dc44f43fc3d8c05529da7e6166447ebfed585b08e7047142b3374e05f0004a5b&quot;,
    &quot;snapshotter&quot;: &quot;overlayfs&quot;,
    &quot;runtimeType&quot;: &quot;io.containerd.runc.v2&quot;,
    &quot;runtimeOptions&quot;: null,
    &quot;config&quot;: {
      &quot;metadata&quot;: {
        &quot;name&quot;: &quot;nginx-emptydir&quot;
      },
      &quot;image&quot;: {
        &quot;image&quot;: &quot;sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5a&quot;
      },
      &quot;envs&quot;: [
        {
          &quot;key&quot;: &quot;KUBERNETES_SERVICE_PORT&quot;,
          &quot;value&quot;: &quot;443&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_PORT_443_TCP&quot;,
          &quot;value&quot;: &quot;tcp://10.43.0.1:443&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_SERVICE_PORT&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_PORT&quot;,
          &quot;value&quot;: &quot;tcp://10.43.203.132:80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_PORT_80_TCP_PORT&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_SERVICE_PORT_HTTP&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_PORT_80_TCP_ADDR&quot;,
          &quot;value&quot;: &quot;10.43.203.132&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_PORT_443_TCP_PORT&quot;,
          &quot;value&quot;: &quot;443&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_SERVICE_PORT&quot;,
          &quot;value&quot;: &quot;9001&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_PORT_9001_TCP_PROTO&quot;,
          &quot;value&quot;: &quot;tcp&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_PORT_9001_TCP_PORT&quot;,
          &quot;value&quot;: &quot;9001&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_PORT_80_TCP_PORT&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_PORT_80_TCP_ADDR&quot;,
          &quot;value&quot;: &quot;10.43.1.35&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_PORT_443_TCP_ADDR&quot;,
          &quot;value&quot;: &quot;10.43.0.1&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_SERVICE_HOST&quot;,
          &quot;value&quot;: &quot;10.43.154.99&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_PORT&quot;,
          &quot;value&quot;: &quot;tcp://10.43.154.99:9001&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_PORT_9001_TCP_ADDR&quot;,
          &quot;value&quot;: &quot;10.43.154.99&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_SERVICE_PORT_HTTP&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;MINIO_APP_PORT_9001_TCP&quot;,
          &quot;value&quot;: &quot;tcp://10.43.154.99:9001&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_SERVICE_HOST&quot;,
          &quot;value&quot;: &quot;10.43.203.132&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_SERVICE_PORT&quot;,
          &quot;value&quot;: &quot;80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_SERVICE_HOST&quot;,
          &quot;value&quot;: &quot;10.43.1.35&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_PORT&quot;,
          &quot;value&quot;: &quot;tcp://10.43.1.35:80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_PORT_80_TCP&quot;,
          &quot;value&quot;: &quot;tcp://10.43.203.132:80&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_SERVICE_HOST&quot;,
          &quot;value&quot;: &quot;10.43.0.1&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_PORT_80_TCP&quot;,
          &quot;value&quot;: &quot;tcp://10.43.1.35:80&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V2_PORT_80_TCP_PROTO&quot;,
          &quot;value&quot;: &quot;tcp&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_SERVICE_PORT_HTTPS&quot;,
          &quot;value&quot;: &quot;443&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_PORT&quot;,
          &quot;value&quot;: &quot;tcp://10.43.0.1:443&quot;
        },
        {
          &quot;key&quot;: &quot;KUBERNETES_PORT_443_TCP_PROTO&quot;,
          &quot;value&quot;: &quot;tcp&quot;
        },
        {
          &quot;key&quot;: &quot;NGINX_V1_PORT_80_TCP_PROTO&quot;,
          &quot;value&quot;: &quot;tcp&quot;
        }
      ],
      &quot;mounts&quot;: [
        {
          &quot;container_path&quot;: &quot;/data&quot;,
          &quot;host_path&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~empty-dir/data&quot;
        },
        {
          &quot;container_path&quot;: &quot;/var/run/secrets/kubernetes.io/serviceaccount&quot;,
          &quot;host_path&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~projected/kube-api-access-4l6hk&quot;,
          &quot;readonly&quot;: true
        },
        {
          &quot;container_path&quot;: &quot;/etc/hosts&quot;,
          &quot;host_path&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/etc-hosts&quot;
        },
        {
          &quot;container_path&quot;: &quot;/dev/termination-log&quot;,
          &quot;host_path&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/containers/nginx-emptydir/1ef99577&quot;
        }
      ],
      &quot;labels&quot;: {
        &quot;io.kubernetes.container.name&quot;: &quot;nginx-emptydir&quot;,
        &quot;io.kubernetes.pod.name&quot;: &quot;nginx-emptydir-deployment-78d79b5fcc-qk5w8&quot;,
        &quot;io.kubernetes.pod.namespace&quot;: &quot;default&quot;,
        &quot;io.kubernetes.pod.uid&quot;: &quot;249f46eb-bc93-4009-bf98-9443b4545fec&quot;
      },
      &quot;annotations&quot;: {
        &quot;io.kubernetes.container.hash&quot;: &quot;7c5c2111&quot;,
        &quot;io.kubernetes.container.restartCount&quot;: &quot;0&quot;,
        &quot;io.kubernetes.container.terminationMessagePath&quot;: &quot;/dev/termination-log&quot;,
        &quot;io.kubernetes.container.terminationMessagePolicy&quot;: &quot;File&quot;,
        &quot;io.kubernetes.pod.terminationGracePeriod&quot;: &quot;30&quot;
      },
      &quot;log_path&quot;: &quot;nginx-emptydir/0.log&quot;,
      &quot;linux&quot;: {
        &quot;resources&quot;: {
          &quot;cpu_period&quot;: 100000,
          &quot;cpu_shares&quot;: 2,
          &quot;oom_score_adj&quot;: 1000,
          &quot;hugepage_limits&quot;: [
            {
              &quot;page_size&quot;: &quot;2MB&quot;
            }
          ]
        },
        &quot;security_context&quot;: {
          &quot;namespace_options&quot;: {
            &quot;pid&quot;: 1
          },
          &quot;run_as_user&quot;: {},
          &quot;masked_paths&quot;: [
            &quot;/proc/acpi&quot;,
            &quot;/proc/kcore&quot;,
            &quot;/proc/keys&quot;,
            &quot;/proc/latency_stats&quot;,
            &quot;/proc/timer_list&quot;,
            &quot;/proc/timer_stats&quot;,
            &quot;/proc/sched_debug&quot;,
            &quot;/proc/scsi&quot;,
            &quot;/sys/firmware&quot;
          ],
          &quot;readonly_paths&quot;: [
            &quot;/proc/asound&quot;,
            &quot;/proc/bus&quot;,
            &quot;/proc/fs&quot;,
            &quot;/proc/irq&quot;,
            &quot;/proc/sys&quot;,
            &quot;/proc/sysrq-trigger&quot;
          ],
          &quot;seccomp&quot;: {
            &quot;profile_type&quot;: 1
          }
        }
      }
    },
    &quot;runtimeSpec&quot;: {
      &quot;ociVersion&quot;: &quot;1.0.2-dev&quot;,
      &quot;process&quot;: {
        &quot;user&quot;: {
          &quot;uid&quot;: 0,
          &quot;gid&quot;: 0
        },
        &quot;args&quot;: [
          &quot;/docker-entrypoint.sh&quot;,
          &quot;nginx&quot;,
          &quot;-g&quot;,
          &quot;daemon off;&quot;
        ],
        &quot;env&quot;: [
          &quot;PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin&quot;,
          &quot;HOSTNAME=nginx-emptydir-deployment-78d79b5fcc-qk5w8&quot;,
          &quot;NGINX_VERSION=1.21.6&quot;,
          &quot;NJS_VERSION=0.7.2&quot;,
          &quot;PKG_RELEASE=1~bullseye&quot;,
          &quot;KUBERNETES_SERVICE_PORT=443&quot;,
          &quot;KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443&quot;,
          &quot;NGINX_V2_SERVICE_PORT=80&quot;,
          &quot;NGINX_V1_PORT=tcp://10.43.203.132:80&quot;,
          &quot;NGINX_V1_PORT_80_TCP_PORT=80&quot;,
          &quot;NGINX_V1_SERVICE_PORT_HTTP=80&quot;,
          &quot;NGINX_V1_PORT_80_TCP_ADDR=10.43.203.132&quot;,
          &quot;KUBERNETES_PORT_443_TCP_PORT=443&quot;,
          &quot;MINIO_APP_SERVICE_PORT=9001&quot;,
          &quot;MINIO_APP_PORT_9001_TCP_PROTO=tcp&quot;,
          &quot;MINIO_APP_PORT_9001_TCP_PORT=9001&quot;,
          &quot;NGINX_V2_PORT_80_TCP_PORT=80&quot;,
          &quot;NGINX_V2_PORT_80_TCP_ADDR=10.43.1.35&quot;,
          &quot;KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1&quot;,
          &quot;MINIO_APP_SERVICE_HOST=10.43.154.99&quot;,
          &quot;MINIO_APP_PORT=tcp://10.43.154.99:9001&quot;,
          &quot;MINIO_APP_PORT_9001_TCP_ADDR=10.43.154.99&quot;,
          &quot;NGINX_V2_SERVICE_PORT_HTTP=80&quot;,
          &quot;MINIO_APP_PORT_9001_TCP=tcp://10.43.154.99:9001&quot;,
          &quot;NGINX_V1_SERVICE_HOST=10.43.203.132&quot;,
          &quot;NGINX_V1_SERVICE_PORT=80&quot;,
          &quot;NGINX_V2_SERVICE_HOST=10.43.1.35&quot;,
          &quot;NGINX_V2_PORT=tcp://10.43.1.35:80&quot;,
          &quot;NGINX_V1_PORT_80_TCP=tcp://10.43.203.132:80&quot;,
          &quot;KUBERNETES_SERVICE_HOST=10.43.0.1&quot;,
          &quot;NGINX_V2_PORT_80_TCP=tcp://10.43.1.35:80&quot;,
          &quot;NGINX_V2_PORT_80_TCP_PROTO=tcp&quot;,
          &quot;KUBERNETES_SERVICE_PORT_HTTPS=443&quot;,
          &quot;KUBERNETES_PORT=tcp://10.43.0.1:443&quot;,
          &quot;KUBERNETES_PORT_443_TCP_PROTO=tcp&quot;,
          &quot;NGINX_V1_PORT_80_TCP_PROTO=tcp&quot;
        ],
        &quot;cwd&quot;: &quot;/&quot;,
        &quot;capabilities&quot;: {
          &quot;bounding&quot;: [
            &quot;CAP_CHOWN&quot;,
            &quot;CAP_DAC_OVERRIDE&quot;,
            &quot;CAP_FSETID&quot;,
            &quot;CAP_FOWNER&quot;,
            &quot;CAP_MKNOD&quot;,
            &quot;CAP_NET_RAW&quot;,
            &quot;CAP_SETGID&quot;,
            &quot;CAP_SETUID&quot;,
            &quot;CAP_SETFCAP&quot;,
            &quot;CAP_SETPCAP&quot;,
            &quot;CAP_NET_BIND_SERVICE&quot;,
            &quot;CAP_SYS_CHROOT&quot;,
            &quot;CAP_KILL&quot;,
            &quot;CAP_AUDIT_WRITE&quot;
          ],
          &quot;effective&quot;: [
            &quot;CAP_CHOWN&quot;,
            &quot;CAP_DAC_OVERRIDE&quot;,
            &quot;CAP_FSETID&quot;,
            &quot;CAP_FOWNER&quot;,
            &quot;CAP_MKNOD&quot;,
            &quot;CAP_NET_RAW&quot;,
            &quot;CAP_SETGID&quot;,
            &quot;CAP_SETUID&quot;,
            &quot;CAP_SETFCAP&quot;,
            &quot;CAP_SETPCAP&quot;,
            &quot;CAP_NET_BIND_SERVICE&quot;,
            &quot;CAP_SYS_CHROOT&quot;,
            &quot;CAP_KILL&quot;,
            &quot;CAP_AUDIT_WRITE&quot;
          ],
          &quot;inheritable&quot;: [
            &quot;CAP_CHOWN&quot;,
            &quot;CAP_DAC_OVERRIDE&quot;,
            &quot;CAP_FSETID&quot;,
            &quot;CAP_FOWNER&quot;,
            &quot;CAP_MKNOD&quot;,
            &quot;CAP_NET_RAW&quot;,
            &quot;CAP_SETGID&quot;,
            &quot;CAP_SETUID&quot;,
            &quot;CAP_SETFCAP&quot;,
            &quot;CAP_SETPCAP&quot;,
            &quot;CAP_NET_BIND_SERVICE&quot;,
            &quot;CAP_SYS_CHROOT&quot;,
            &quot;CAP_KILL&quot;,
            &quot;CAP_AUDIT_WRITE&quot;
          ],
          &quot;permitted&quot;: [
            &quot;CAP_CHOWN&quot;,
            &quot;CAP_DAC_OVERRIDE&quot;,
            &quot;CAP_FSETID&quot;,
            &quot;CAP_FOWNER&quot;,
            &quot;CAP_MKNOD&quot;,
            &quot;CAP_NET_RAW&quot;,
            &quot;CAP_SETGID&quot;,
            &quot;CAP_SETUID&quot;,
            &quot;CAP_SETFCAP&quot;,
            &quot;CAP_SETPCAP&quot;,
            &quot;CAP_NET_BIND_SERVICE&quot;,
            &quot;CAP_SYS_CHROOT&quot;,
            &quot;CAP_KILL&quot;,
            &quot;CAP_AUDIT_WRITE&quot;
          ]
        },
        &quot;apparmorProfile&quot;: &quot;cri-containerd.apparmor.d&quot;,
        &quot;oomScoreAdj&quot;: 1000
      },
      &quot;root&quot;: {
        &quot;path&quot;: &quot;rootfs&quot;
      },
      &quot;mounts&quot;: [
        {
          &quot;destination&quot;: &quot;/proc&quot;,
          &quot;type&quot;: &quot;proc&quot;,
          &quot;source&quot;: &quot;proc&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;noexec&quot;,
            &quot;nodev&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/dev&quot;,
          &quot;type&quot;: &quot;tmpfs&quot;,
          &quot;source&quot;: &quot;tmpfs&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;strictatime&quot;,
            &quot;mode=755&quot;,
            &quot;size=65536k&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/dev/pts&quot;,
          &quot;type&quot;: &quot;devpts&quot;,
          &quot;source&quot;: &quot;devpts&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;noexec&quot;,
            &quot;newinstance&quot;,
            &quot;ptmxmode=0666&quot;,
            &quot;mode=0620&quot;,
            &quot;gid=5&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/dev/mqueue&quot;,
          &quot;type&quot;: &quot;mqueue&quot;,
          &quot;source&quot;: &quot;mqueue&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;noexec&quot;,
            &quot;nodev&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/sys&quot;,
          &quot;type&quot;: &quot;sysfs&quot;,
          &quot;source&quot;: &quot;sysfs&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;noexec&quot;,
            &quot;nodev&quot;,
            &quot;ro&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/sys/fs/cgroup&quot;,
          &quot;type&quot;: &quot;cgroup&quot;,
          &quot;source&quot;: &quot;cgroup&quot;,
          &quot;options&quot;: [
            &quot;nosuid&quot;,
            &quot;noexec&quot;,
            &quot;nodev&quot;,
            &quot;relatime&quot;,
            &quot;ro&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/data&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~empty-dir/data&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/etc/hosts&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/etc-hosts&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/dev/termination-log&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/containers/nginx-emptydir/1ef99577&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/etc/hostname&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/rancher/k3s/agent/containerd/io.containerd.grpc.v1.cri/sandboxes/61e9462282eef43750538189368fd70e26a366fb27902747a2968b863ff09ad1/hostname&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/etc/resolv.conf&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/rancher/k3s/agent/containerd/io.containerd.grpc.v1.cri/sandboxes/61e9462282eef43750538189368fd70e26a366fb27902747a2968b863ff09ad1/resolv.conf&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/dev/shm&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/run/k3s/containerd/io.containerd.grpc.v1.cri/sandboxes/61e9462282eef43750538189368fd70e26a366fb27902747a2968b863ff09ad1/shm&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
        {
          &quot;destination&quot;: &quot;/var/run/secrets/kubernetes.io/serviceaccount&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~projected/kube-api-access-4l6hk&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;ro&quot;
          ]
        }
      ],
      &quot;annotations&quot;: {
        &quot;io.kubernetes.cri.container-name&quot;: &quot;nginx-emptydir&quot;,
        &quot;io.kubernetes.cri.container-type&quot;: &quot;container&quot;,
        &quot;io.kubernetes.cri.image-name&quot;: &quot;docker.io/library/nginx:latest&quot;,
        &quot;io.kubernetes.cri.sandbox-id&quot;: &quot;61e9462282eef43750538189368fd70e26a366fb27902747a2968b863ff09ad1&quot;,
        &quot;io.kubernetes.cri.sandbox-name&quot;: &quot;nginx-emptydir-deployment-78d79b5fcc-qk5w8&quot;,
        &quot;io.kubernetes.cri.sandbox-namespace&quot;: &quot;default&quot;
      },
      &quot;linux&quot;: {
        &quot;resources&quot;: {
          &quot;devices&quot;: [
            {
              &quot;allow&quot;: false,
              &quot;access&quot;: &quot;rwm&quot;
            }
          ],
          &quot;memory&quot;: {},
          &quot;cpu&quot;: {
            &quot;shares&quot;: 2,
            &quot;period&quot;: 100000
          }
        },
        &quot;cgroupsPath&quot;: &quot;/kubepods/besteffort/pod249f46eb-bc93-4009-bf98-9443b4545fec/dc44f43fc3d8c05529da7e6166447ebfed585b08e7047142b3374e05f0004a5b&quot;,
        &quot;namespaces&quot;: [
          {
            &quot;type&quot;: &quot;pid&quot;
          },
          {
            &quot;type&quot;: &quot;ipc&quot;,
            &quot;path&quot;: &quot;/proc/87296/ns/ipc&quot;
          },
          {
            &quot;type&quot;: &quot;uts&quot;,
            &quot;path&quot;: &quot;/proc/87296/ns/uts&quot;
          },
          {
            &quot;type&quot;: &quot;mount&quot;
          },
          {
            &quot;type&quot;: &quot;network&quot;,
            &quot;path&quot;: &quot;/proc/87296/ns/net&quot;
          }
        ],
        &quot;maskedPaths&quot;: [
          &quot;/proc/acpi&quot;,
          &quot;/proc/kcore&quot;,
          &quot;/proc/keys&quot;,
          &quot;/proc/latency_stats&quot;,
          &quot;/proc/timer_list&quot;,
          &quot;/proc/timer_stats&quot;,
          &quot;/proc/sched_debug&quot;,
          &quot;/proc/scsi&quot;,
          &quot;/sys/firmware&quot;
        ],
        &quot;readonlyPaths&quot;: [
          &quot;/proc/asound&quot;,
          &quot;/proc/bus&quot;,
          &quot;/proc/fs&quot;,
          &quot;/proc/irq&quot;,
          &quot;/proc/sys&quot;,
          &quot;/proc/sysrq-trigger&quot;
        ]
      }
    }
  }
}</pre>
</details>
<pre>
root@HomePC:~# 
</pre>

From the previous output we can get the part of 
<pre>  &quot;mounts&quot;: [
         ...
        {
          &quot;destination&quot;: &quot;/data&quot;,
          &quot;type&quot;: &quot;bind&quot;,
          &quot;source&quot;: &quot;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec/volumes/kubernetes.io~empty-dir/data&quot;,
          &quot;options&quot;: [
            &quot;rbind&quot;,
            &quot;rprivate&quot;,
            &quot;rw&quot;
          ]
        },
</pre>

which shows us a directory on the kubernetes host where volume was created

then let's connect to the pod and create some data in the directory
<pre>root@HomePC:~# crictl exec -ti dc44f43fc3d8c bash
root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# ls /data
root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# touch /data/test-empty-dir-file.txt
root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# echo &quot;test empty dir data 123123123&quot; &gt; /data/test-empty-dir-file.txt 
root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# cat /data/test-empty-dir-file.txt 
test empty dir data 123123123
root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# 
</pre>

Then let's delete the deployment and try to find out if there any information on the host still left.

<pre>┌─[ 8:14][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl delete deployment nginx-emptydir-deployment
deployment.apps &quot;nginx-emptydir-deployment&quot; deleted
</pre>

<pre>root@nginx-emptydir-deployment-78d79b5fcc-qk5w8:/# <font color="#CC0000">FATA</font>[0351] execing command in container: command terminated with exit code 137 
root@HomePC:~# 
</pre>

<pre>root@HomePC:~# ls -la /var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec
ls: cannot access &apos;/var/lib/kubelet/pods/249f46eb-bc93-4009-bf98-9443b4545fec&apos;: No such file or directory
root@HomePC:~# </pre>

So from all this we can get that with the EmptyDir volume type, kubernetes creates a directory locally on a host
and after the pod destroy it deletes the volume directory as well.
  







  
## - Optional. Raise nfs share on a remote machine. Create a pv using this share, create a pvc for it, create a deployment. Save data to the share, delete the deployment, delete the pv/pvc, check that the data is safe.

First of all we shall start from setting up an NFS server on my laptop than and use in on the k3s server which is on the desktop computer.
<pre>
┌─[ 8:56][][<font color="#4E9A06">alexLaptop</font>]
├─[<font color="#06989A">~</font>]
└─▪ sudo -i
[sudo] password for alexander:              
root@alexLaptop:~# apt install nfs-server
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting &apos;nfs-kernel-server&apos; instead of &apos;nfs-server&apos;
nfs-kernel-server is already the newest version (1:1.3.4-2.5ubuntu3.4).
nfs-kernel-server set to manually installed.
0 to upgrade, 0 to newly install, 0 to remove and 0 not to upgrade.
</pre>

Then - add a directory for it at /data catalogue
and add the next line to `/etc/exports` file 

<pre>
root@alexLaptop:~# cat /etc/exports 
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/data/nfs-share 192.168.44.110/24(rw,all_squash,anonuid=0,anongid=0)
</pre>

You also night notice that I added WHOLE permissions to the directory and allowed access under root user.
I don't suggest you to do so on a production environment. But here, when we just want to check the fact of connection and
test usage of NFS share, so I just waned nfs service had no problems with catalogue usage at all.

<pre>
┌─[ 9:04][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:8 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ git clone https://github.com/kubernetes-incubator/external-storage.git
Cloning into 'external-storage'...
remote: Enumerating objects: 64319, done.
remote: Total 64319 (delta 0), reused 0 (delta 0), pack-reused 64319
Receiving objects: 100% (64319/64319), 113.79 MiB | 4.27 MiB/s, done.
Resolving deltas: 100% (29663/29663), done.
</pre>

From the `external-storage` directory you may find a lot of storage types to use. But I already have downloaded it, and customized for my implementation.
So my release version you may find in `./homework` folder.

To connect NFS share to our k3s "cluster" we shall start from addition a ServiceAccount with some particular permissions: 
<pre>
┌─[ 9:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat nfs-rbac.yaml 
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [&quot;&quot;]
    resources: [&quot;persistentvolumes&quot;]
    verbs: [&quot;get&quot;, &quot;list&quot;, &quot;watch&quot;, &quot;create&quot;, &quot;delete&quot;]
  - apiGroups: [&quot;&quot;]
    resources: [&quot;persistentvolumeclaims&quot;]
    verbs: [&quot;get&quot;, &quot;list&quot;, &quot;watch&quot;, &quot;update&quot;]
  - apiGroups: [&quot;storage.k8s.io&quot;]
    resources: [&quot;storageclasses&quot;]
    verbs: [&quot;get&quot;, &quot;list&quot;, &quot;watch&quot;]
  - apiGroups: [&quot;&quot;]
    resources: [&quot;events&quot;]
    verbs: [&quot;create&quot;, &quot;update&quot;, &quot;patch&quot;]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [&quot;&quot;]
    resources: [&quot;endpoints&quot;]
    verbs: [&quot;get&quot;, &quot;list&quot;, &quot;watch&quot;, &quot;create&quot;, &quot;update&quot;, &quot;patch&quot;]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io


┌─[ 9:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ kubectl apply -f ./nfs-rbac.yaml
serviceaccount/nfs-client-provisioner created
clusterrole.rbac.authorization.k8s.io/nfs-client-provisioner-runner created
clusterrolebinding.rbac.authorization.k8s.io/run-nfs-client-provisioner created
role.rbac.authorization.k8s.io/leader-locking-nfs-client-provisioner created
rolebinding.rbac.authorization.k8s.io/leader-locking-nfs-client-provisioner created
</pre>

Then we are about to create a deployment for the NFS-client provisioner pod.
So we shall edit the file we are about to apply, and customize it for our needs.
The lines in ENV and Volumes part shall be brought to our reality.

<pre>┌─[09:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat nfs-deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: fuseim.pri/ifs
            - name: NFS_SERVER
              value: 192.168.44.103
            - name: NFS_PATH
              value: /data/nfs-share
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.44.103
            path: /data/nfs-share

┌─[09:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ kubectl apply -f nfs-deployment.yaml
deployment.apps/nfs-client-provisioner created

┌─[ 9:53][][alexLaptop][±][master U:1 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task3/external-storage/nfs-client/deploy]
└─▪ kubectl get po
NAME                                      READY   STATUS    RESTARTS      AGE
nginx-v2-5f885975d5-wtj6z                 1/1     Running   1 (12h ago)   24h
nginx-v1-79bc94ff97-9c2p4                 1/1     Running   1 (12h ago)   24h
nginx-v1-79bc94ff97-9q66p                 1/1     Running   0             10h
nginx-v1-79bc94ff97-6ng9t                 1/1     Running   0             10h
minio-575d987896-qktkd                    1/1     Running   0             5h56m
minio-state-0                             1/1     Running   0             5h38m
nfs-client-provisioner-5fb9b5f758-tgh5l   1/1     Running   0             36s
</pre>

Then we might have to add a class

<pre>┌─[11:16][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat ./nfs-class.yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
provisioner: nfs-storage
parameters:
  archiveOnDelete: &quot;false&quot;
</pre>

<pre>
┌─[09:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ kubectl apply -f ./nfs-class.yaml
storageclass.storage.k8s.io/managed-nfs-storage created

┌─[09:05][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ kubectl get storageClass
NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  28h
managed-nfs-storage    nfs-storage             Delete          Immediate              false                  23s

</pre>


And now, we can create the main NFS storage - PV
and a PVC or even PVCs which we may use with deployment(s).

<pre>
┌─[10:23][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat nfs-pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
  labels:
    type: networked
spec:
  storageClassName: managed-nfs-storage
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    server: 192.168.44.103
    path: &quot;/data/nfs-share&quot;
    readOnly: false

┌─[ 9:09][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:10 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f ./nfs-pv.yaml 
persistentvolume/nfs-pv created

┌─[10:57][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                            STORAGECLASS          REASON   AGE
minio-deployment-pv                        5Gi        RWO            Retain           Bound    default/minio-deployment-claim                                  7h13m
pvc-f8ba17ed-5f0a-4da3-84f1-02edb6433887   1Gi        RWO            Delete           Bound    default/minio-minio-state-0      local-path                     6h42m
nfs-pv                                     10Gi       RWO            Retain           Bound    default/nfs-pvc                  managed-nfs-storage            6m22s
</pre>

<pre>
┌─[11:18][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat ./nfs-pvc.yaml 
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nfs-pvc
  annotations:
    volume.beta.kubernetes.io/storage-class: &quot;managed-nfs-storage&quot;
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Mi

┌─[ 9:09][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:10 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl apply -f ./nfs-pvc.yaml 
persistentvolumeclaim/nfs-pvc created

┌─[10:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get pvc
NAME                     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS          AGE
minio-deployment-claim   Bound    minio-deployment-pv                        5Gi        RWO                                  7h4m
minio-minio-state-0      Bound    pvc-f8ba17ed-5f0a-4da3-84f1-02edb6433887   1Gi        RWO            local-path            6h42m
nfs-pvc                  Bound    nfs-pv                                     10Gi       RWO            managed-nfs-storage   6m15s
</pre>

Okay. Now we do have a PVC to use in a deployment.
Let's try it.

<pre>
┌─[11:46][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ cat ./nginx-nfs-deployment.yaml 
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-nfs-deployment
spec:
  selector:
    matchLabels:
      app: nginx-nfs
  template:
    metadata:
      labels:
        app: nginx-nfs
    spec:
      containers:
        - name: nginx-nfs
          image: nginx
          volumeMounts:
            - name: data
              mountPath: /data
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: nfs-pvc
      restartPolicy: Always 

┌─[11:46][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework</font>]
└─▪ kubectl apply -f ./nfs-deployment.yaml
deployment.apps/nginx-nfs-deployment created
</pre>

To check it - let's connect to the pod and put some data into it.

<pre>
┌─[10:58][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ kubectl get pod
NAME                                      READY   STATUS    RESTARTS      AGE
nginx-v2-5f885975d5-wtj6z                 1/1     Running   1 (13h ago)   25h
nginx-v1-79bc94ff97-9c2p4                 1/1     Running   1 (13h ago)   25h
nginx-v1-79bc94ff97-9q66p                 1/1     Running   0             11h
nginx-v1-79bc94ff97-6ng9t                 1/1     Running   0             11h
minio-575d987896-qktkd                    1/1     Running   0             7h1m
minio-state-0                             1/1     Running   0             6h43m
nfs-client-provisioner-5fb9b5f758-tgh5l   1/1     Running   0             65m
nginx-nfs-deployment-d4f8c49f9-7plpm      1/1     Running   0             6m4s

┌─[10:53][][alexLaptop][±][main S:1 U:2 ?:6 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework]
└─▪ kubectl exec -ti nginx-nfs-deployment-d4f8c49f9-7plpm -- bash
root@nginx-nfs-deployment-d4f8c49f9-7plpm:/# ls /data
root@nginx-nfs-deployment-d4f8c49f9-7plpm:/# touch /data/test-nfs-pvc.txt
root@nginx-nfs-deployment-d4f8c49f9-7plpm:/# echo "test test test 1234321" > /data/test-nfs-pvc.txt
root@nginx-nfs-deployment-d4f8c49f9-7plpm:/# cat /data/test-nfs-pvc.txt
test test test 1234321
</pre>

Okay. Data is in the mounted NFS folder, and it should be on my laptop as far as I run NFS server on it :) 

<pre>
root@alexLaptop:~# ls /data/
<font color="#729FCF"><b>doker-data</b></font>  <font color="#729FCF"><b>nfs-share</b></font>
root@alexLaptop:~# ls /data/nfs-share/
test-nfs-pvc.txt
root@alexLaptop:~# cat /data/nfs-share/test-nfs-pvc.txt 
test test test 1234321
root@alexLaptop:~# 
</pre>

Tadaaaaaa!!!! Data is here.

Okay then. Let's delete all that NFS stuff and check if the data still in there.

<pre>┌─[10:57][][alexLaptop][±][main S:1 U:2 ?:6 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework]
└─▪ kubectl get deployment
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
nginx-v2                 1/1     1            1           26h
nginx-v1                 3/3     3            3           26h
minio                    1/1     1            1           7h31m
nfs-client-provisioner   1/1     1            1           94m
nginx-nfs-deployment     1/1     1            1           35m

┌─[11:27][][alexLaptop][±][main S:1 U:2 ?:6 ✗]
├─[~/_GIT/github/EPAM/devops-school-kubernetes/Task3/homework]
└─▪ kubectl delete deployment nginx-nfs-deployment
deployment.apps "nginx-nfs-deployment" deleted

┌─[11:29][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ sudo ls -la /data/nfs-share
total 4
drwxr-xr-x 2 root root 30 фев  6 22:54 .
drwx--x--x 4 root root 41 фев  6 20:58 ..
-rw-r--r-- 1 root root 23 фев  6 22:54 test-nfs-pvc.txt
</pre>

<pre>┌─[11:29][][<font color="#4E9A06">alexLaptop</font>][<font color="#8AE234"><b>±</b></font>][main S:1 U:2 ?:6 <font color="#EF2929"><b>✗</b></font>]
├─[<font color="#06989A">~/_GIT/github/EPAM/devops-school-kubernetes/Task3</font>]
└─▪ sudo cat /data/nfs-share/test-nfs-pvc.txt 
test test test 1234321
</pre>

Okay. Data still in the directory of the NFS server on my laptop.
This is it.
We used a PERSISTENT storage because we can reuse this data next time in other deployment.
