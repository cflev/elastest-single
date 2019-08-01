# Cerebro
1. install with kubectl

```
kubectl apply -f edm-cerebro-volumes.yaml
kubectl apply -f edm-cerebro-deployment.yaml
```
2. install with helm:
```
cd helm
helm install --name edm-cerebro .
```

if you get error:
```
Error: release edm-cerebro failed: namespaces "default" is forbidden: User "system:serviceaccount:kube-system:default" cannot get resource "namespaces" in API group "" in the namespace "default"
```

Run the following:

```
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
```
