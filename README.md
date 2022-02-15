mygps app deployed on Google GKE<br>
<br>
using gcloud cloud shell<br>
<br>
mygps app at https://www.github.com/fkam18/mygps<br>
the mygps docker image is at https://hub.docker.com/u/fkam18<br>
<br>
sales@cloudshell:~ (mygps-341412)$ history<br>
gcloud config set project mygps-341412<br>
gcloud config set compute/zone europe-west6-b<br>
gcloud services enable container.googleapis.com<br>
gcloud container clusters create mygps-cluster<br>
<br>
Creating cluster mygps-cluster in europe-west6-b... Cluster is being health-checked (master is healthy)...done.     <br>
Created [https://container.googleapis.com/v1/projects/mygps-341412/zones/europe-west6-b/clusters/mygps-cluster].<br>
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/europe-west6-b/mygps-cluster?project=mygps-341412<br>
kubeconfig entry generated for mygps-cluster.<br>
NAME: mygps-cluster<br>
LOCATION: europe-west6-b<br>
MASTER_VERSION: 1.21.6-gke.1500<br>
MASTER_IP: 34.65.136.213<br>
MACHINE_TYPE: e2-medium<br>
NODE_VERSION: 1.21.6-gke.1500<br>
NUM_NODES: 3<br>
STATUS: RUNNING<br>
<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get nodes<br>
NAME                                           STATUS   ROLES    AGE    VERSION<br>
gke-mygps-cluster-default-pool-b975fa1a-fc36   Ready    <none>   3m5s   v1.21.6-gke.1500<br>
gke-mygps-cluster-default-pool-b975fa1a-l4x4   Ready    <none>   3m4s   v1.21.6-gke.1500<br>
gke-mygps-cluster-default-pool-b975fa1a-x9h4   Ready    <none>   3m5s   v1.21.6-gke.1500<br>
sales@cloudshell:~ (mygps-341412)$<br>
<br>
sales@cloudshell:~ (mygps-341412)$ gcloud container clusters get-credentials mygps-cluster --zone europe-west6-b<br>
Fetching cluster endpoint and auth data.<br>
kubeconfig entry generated for mygps-cluster.<br>
<br>
sales@cloudshell:~ (mygps-341412)$ kubectl create deployment mygps --image=docker.io/fkam18/mygps<br>
deployment.apps/mygps created<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get deployment mygps<br>
NAME    READY   UP-TO-DATE   AVAILABLE   AGE<br>
mygps   1/1     1            1           40s<br>
sales@cloudshell:~ (mygps-341412)$ kubectl scale deployment mygps --replicas=3<br>
deployment.apps/mygps scaled<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get deployment mygps<br>
NAME    READY   UP-TO-DATE   AVAILABLE   AGE<br>
mygps   3/3     3            3           3m14s<br>
<br>
sales@cloudshell:~ (mygps-341412)$ kubectl autoscale deployment mygps --cpu-percent=80 --min=1 --max=5<br>
horizontalpodautoscaler.autoscaling/mygps autoscaled<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get deployment mygps<br>
NAME    READY   UP-TO-DATE   AVAILABLE   AGE<br>
mygps   3/3     3            3           4m24s<br>
sales@cloudshell:~ (mygps-341412)$<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get pods<br>
NAME                     READY   STATUS    RESTARTS   AGE<br>
mygps-66dddf4b5b-lfdlh   1/1     Running   0          3m56s<br>
mygps-66dddf4b5b-nkntv   1/1     Running   0          3m56s<br>
mygps-66dddf4b5b-qbnjj   1/1     Running   0          4m51s<br>
sales@cloudshell:~ (mygps-341412)$<br>
sales@cloudshell:~ (mygps-341412)$ kubectl expose deployment mygps --name=mygps-service --type=LoadBalancer --port 80 --target-port 80<br>
service/mygps-service exposed<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get services<br>
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE<br>
kubernetes      ClusterIP      10.116.0.1     <none>        443/TCP        16m<br>
mygps-service   LoadBalancer   10.116.1.232   <pending>     80:30786/TCP   9s<br>
sales@cloudshell:~ (mygps-341412)$<br>
<br>
sales@cloudshell:~ (mygps-341412)$ kubectl get services<br>
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE<br>
kubernetes      ClusterIP      10.116.0.1     <none>          443/TCP        17m<br>
mygps-service   LoadBalancer   10.116.1.232   34.65.205.110   80:30786/TCP   69s<br>
sales@cloudshell:~ (mygps-341412)$<br>
<br>
Now http://34.65.205.110 will get to myGPS app.<br>
<br>
=== <br>
