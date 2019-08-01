Install microk8s
https://microk8s.io/

Get latest files
```
git clone https://github.com/cflev/elastest-single.git
```

Create /tmp/domain 

```
mkdir /tmp/domain
```
You need to create it every time you restart the node

add privileged permissions (--allow-privileged=true)
```
vi /var/snap/microk8s/current/args/kube-apiserver
or
echo "--allow-privileged=true" >> /var/snap/microk8s/current/args/kube-apiserver
```
Reboot or restart microk8s services

Create a new namespace
```
kubectl create namespace elatest-single
```

Change kubectl namespace context to it:
```
kubectx elatest
```
* install kubectx from here: https://github.com/ahmetb/kubectx

go to edm directory and run all yaml files:
```
kubectl apply -f edm-volumes.yaml -f edm-mysql.yaml -f edm-elasticsearch.yaml -f edm-hadoop-config.yaml -f edm-hadoop-yarn.yaml -f edm-hadoop-hdfs.yaml -f edm-kibana.yaml -f edm-cerebro.yaml -f edm-alluxio.yaml -f edm.yaml
```
go to ebs directory and run all yaml files:
```
kubectl apply -f ebs-sparkmaster.yaml -f ebs-sparkworker.yaml -f restapi.yaml
```

get a bash shell to ebs-sparkmaster
```
kubectl exec -ti deployment.apps/ebs-sparkmaster bash
```

Run tests!

Cleanup:
```
kubectl delete namespaces elatest-single
```
