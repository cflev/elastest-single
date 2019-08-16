Install microk8s
https://microk8s.io/

1. Get latest files
```
git clone https://github.com/cflev/elastest-single.git
```

2. Create /tmp/domain 

```
mkdir /tmp/domain
```
You need to create it every time you restart the node

3. add privileged permissions (--allow-privileged=true) - if you already run k8s go to step (6)
```
vi /var/snap/microk8s/current/args/kube-apiserver
or
echo "--allow-privileged=true" >> /var/snap/microk8s/current/args/kube-apiserver
```
4. Reboot or restart microk8s services
```
microk8s.stop && microk8s.start
```

5. Enable storage
```
microk8s.enable storage
```

6. Create a new namespace
```
kubectl create namespace elatest-single
```

7. Change kubectl namespace context to it:
```
kubens elatest
```
* install kubens from here: https://github.com/ahmetb/kubectx

8. go to edm directory and run all yaml files:
```
kubectl apply -f edm-volumes.yaml -f edm-mysql.yaml -f edm-elasticsearch.yaml -f edm-hadoop-config.yaml -f edm-hadoop-yarn.yaml -f edm-hadoop-hdfs.yaml -f edm-kibana.yaml -f edm-cerebro.yaml -f edm-alluxio.yaml -f edm.yaml
```
8a. change permissions for elastic search service
```
chown 1000:1000 /tmp/data/es
```
9. go to ebs directory and run all yaml files:
```
kubectl apply -f ebs-sparkmaster.yaml -f ebs-sparkworker.yaml -f restapi.yaml
```

10. get a bash shell to ebs-sparkmaster
```
kubectl exec -ti deployment.apps/ebs-sparkmaster bash
```

11. Run tests!

```
kubectl exec -ti svc/sparkmaster bash
git clone https://github.com/elastest/demo-projects.git
cd demo-projects/ebs-test
mvn -q package
rm -f big.txt
wget -q https://norvig.com/big.txt
hadoop fs -rmr /out.txt 
hadoop fs -rm /big.txt
hadoop fs -copyFromLocal big.txt /big.txt
spark-submit --class org.sparkexample.WordCountTask --master spark://sparkmaster:7077 /demo-projects/ebs-test/target/hadoopWordCount-1.0-SNAPSHOT.jar /big.txt
hadoop fs -getmerge /out.txt ./out.txt
head -20 out.txt
```

12. Cleanup:
```
kubectl delete namespaces elatest-single
```
