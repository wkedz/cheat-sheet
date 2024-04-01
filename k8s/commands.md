# create new definition yaml.
kubectl create -f [PATH-TO-FILE.yml]

# apply changes
kubectl apply -f [PATH-TO-FILE.yml]

# Get

## pods
kubectl get pods
kubectl get pods --watch # window will watch/monitor changes on fly

## nodes
kubectl get nodes

## namespaces
kubectl get ns

## replicationcontroller
kubectl get replicationcontroller

## replicaset
kubectl get replicaset

## service 
kubectl get svc

# get version
kubectl version --client=true -o yaml

# see config
kubectl config view

# show current context
kubectl config current-context 

# use given context
kubectl config use -context [NAME] 

# explain pods - but can be anything else
kubectl explain pods --recursive
kubectl explain pod.spec.restartPolicy 

# show logs
kubectl logs logtest [NAME] 

# show logs of target container in pod
kubectl logs logtest --container [CONTAINER-NAME]

# see more details abut given pod
kubectl describe pod [NAME-OF-POD]

# Create namespace imperative
kubectl create ns [NAME]

# deleting

## namespace
kubectl delete ns [NAME-NS]

## pod
kubectl delete pod [NAME-OF-POD]

# context

## set default context to [NAME-NS] -> commands no longer need to have -n/--namespace flag
kubectl config set-context --current --namespace [NAME-NS]

