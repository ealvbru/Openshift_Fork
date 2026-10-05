## Prepare the lab.
```
oc adm taint  nodes worker01 dedicated=app1:NoSchedule
oc adm taint  nodes worker02 dedicated=app1:NoSchedule
oc adm taint  nodes worker03 dedicated=app1:NoSchedule

oc adm taint node master01 node-role.kubernetes.io/master=:NoSchedule
oc adm taint node master02 node-role.kubernetes.io/master=:NoSchedule
oc adm taint node master03 node-role.kubernetes.io/master=:NoSchedule

oc patch scheduler cluster --type='json' -p='[{"op": "replace", "path": "/spec/mastersSchedulable", "value": false}]'

oc create deployment test-pod-taints --image=registry.ocp4.example.com:8443/redhattraining/hello-world-nginx:v1.0 --replicas=3

```

### Just for your information: You can also use `kubeclt` command, `kubectl taint node worker03 dedicated=app1:NoSchedule`

# Qestion: You need to check why new pods are not being created on the OCP cluster. 

## Solution:

```
oc get nodes

oc get pods
NAME                        READY   STATUS    RESTARTS   AGE
test-pod-79d89c74b7-ct5mz   0/1     Pending   0          2s
test-pod-79d89c74b7-xlhqn   0/1     Pending   0          2s
test-pod-79d89c74b7-z69v8   0/1     Pending   0          2s


oc get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
NAME       TAINTS
master01   [map[effect:NoSchedule key:node-role.kubernetes.io/master]]
master02   [map[effect:NoSchedule key:node-role.kubernetes.io/master]]
master03   [map[effect:NoSchedule key:node-role.kubernetes.io/master]]
worker01   [map[effect:NoSchedule key:dedicated value:app1]]
worker02   [map[effect:NoSchedule key:dedicated value:app1]]
worker03   [map[effect:NoSchedule key:dedicated value:app1]]


oc adm taint node worker01 dedicated-
oc adm taint node worker02 dedicated-
oc adm taint node worker03 dedicated-
```

### With the help of FORLOOP command.


```
for i in {01..03} ; do echo $i ; done
```


```
for i in {01..03} ; do oc get nodes worker$i ; done
```

```
for i in {01..03} ; do oc get nodes worker$i -o yaml ; done
```

```
for i in {01..03} ; do oc get nodes worker$i -o yaml | grep -i taint ; done
```

```
for i in {01..03} ; do oc get nodes worker$i -o yaml | grep -i taint -A 4; done
```


### Below is the references.

```
[student@workstation test]$ oc get nodes worker01 -o yaml| grep -i taint -A 4
  taints:
  - effect: NoSchedule
    key: dedicated
    value: app1
status:
[student@workstation test]$ oc adm taint node worker01 dedicated-
node/worker01 untainted
[student@workstation test]$
[student@workstation test]$ oc adm taint node worker02 dedicated-
node/worker02 untainted
[student@workstation test]$ oc adm taint node worker03 dedicated-
node/worker03 untainted
[student@workstation test]$ 
```
