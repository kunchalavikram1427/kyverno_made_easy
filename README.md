# Kyverno


## Install
```
kubectx docker-desktop
```
```
kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.13.0/install.yaml
```
```
kubectl get mutatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations kyverno-resource-mutating-webhook-cfg -o yaml > mutatingwebhookconfiguration.yaml
kubectl get validatingwebhookconfigurations
kubectl get validatingwebhookconfigurations kyverno-resource-validating-webhook-cfg -o yaml 
```
ClusterPolicy and Policy
```
kubectl get cpol,pol -A  
```

### Examples

#### Enforce labels
```
kubectl apply -f examples/enforce_require_labels.yaml 
kubectl run nginx --image nginx --labels team=backend
```

```
kubectl get policyreport -o wide   
NAME                                   KIND   NAME    PASS   FAIL   WARN   ERROR   SKIP   AGE
89f7d4bd-525c-4b57-a79d-70b25971e0c2   Pod    nginx   1      0      0      0       0      32s
```

```
kubectl delete po nginx
kubectl delete clusterpolicy require-labels
```

#### Mutate labels
```
kubectl apply -f examples/mutate_add_labels.yaml
kubectl run nginx --image nginx
```
```
kubectl get po nginx --show-labels
NAME    READY   STATUS    RESTARTS   AGE   LABELS
nginx   1/1     Running   0          18s   run=nginx,team=devops
```

```
kubectl delete po nginx
kubectl delete clusterpolicy add-labels
```              
