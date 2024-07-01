## Usefull flags 

`--record` - record the cause of change used in rollout
`--dry-run=client` - tells if resource can be created and if command  is right
`-o [yaml,json]` - output resource definition in yaml formal on the screen

## Apply

`kubectl apply -f [PATH-TO-FILE.yml]`

## Get

`kubectl get deployments` - get deployments from default namespace
`kubectl get nodes` - get nodes from edfault namespace 
`kubectl get ns` - get namespaces 
`kubectl get pods` - get pods
`kubectl get pods --watch` - window will watch/monitor changes on fly
`kubectl get replicaset` - get replicaset
`kubectl get replicationcontroller` - get replicatorcontroller
`kubectl get svc` - get services
`kubectl get secrets` - get secrets   
`kubectl get serviceaccount` - get service account
`kubectl get rs new-replica-set -o yaml` - dump manifest of existing resource `new-replica-set`

## Version

`kubectl version --client=true -o yaml` - get version in yaml

## Config

`kubectl config current-context`
`kubectl config use -context [NAME]`
`kubectl config view`
`kubectl config set-context --current --namespace [NAME-NS]` - set default context to [NAME-NS] -> commands no longer need to have -n/--namespace flag

## Create

`kubectl create -f [PATH-TO-FILE.yml]`
`kubectl create ns [NAME]` - create namespace imperative
`kubectl create deployment --image=nginx nginx --dry-run -o yaml` - generate deployment yaml file, dont create resources
`kubectl create deployment nginx --image=nginx --replicas=4` - generate deployment with 4 replicas
`kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml` - create nginx deployment and dump it into yaml
`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml` - this will not use the pods' labels as selectors; instead it will assume selectors as app=redis.
`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml` - This will not use the pods' labels as selectors
`kubectl create configmap my-config --from-file=key1=/path/to/bar/file1.txt --from-file=key2=/path/to/bar/file2.txt` - create a new config map named my-config with specified keys instead of file basenames on disk
`kubectl create configmap my-config --from-literal=key1=config1 --from-literal=key2=config2` - create a new config map named my-config with key1=config1 and key2=config2
`kubectl create deployment redis-deploy --image=redis --replicas=2 -n dev-ns` = Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas.
`kubectl run httpd --image=httpd:alpine --port=80 --expose` - Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80.



## Explain

`kubectl explain pods --recursive` - pods - but can be anything else
`kubectl explain pod.spec.restartPolicy` 

## Expose

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml` - create a Service named redis-service of type ClusterIP to expose pod redis on port 6379 This will automatically use the pod's labels as selectors)
`kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml` - This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.

## Logs

`kubectl logs logtest [NAME]` 
`kubectl logs logtest --container [CONTAINER-NAME]`

## Describe

`kubectl describe pod [NAME-OF-POD]` - see more details abut given pod

## Delete

`kubectl delete ns [NAME-NS]` - namespace
`kubectl delete pod [NAME-OF-POD]` - pod

## Set 

`kubectl set image deployment [DEPLOYMENT-NAME] [CONTAINER-NAME]=[IMAGE-NAME]` - set image of deployment

## Scale

`kubectl scale deployment nginx --replicas=4` - scale deployment to 4 replicas

## Rollout 

`kubectl rollout status deployment [DEPLOYMENT-NAME]` - status
`kubectl rollout history deployment [DEPLOYMENT-NAME]` - history of revision  
`kubectl rollout undo deployment [DEPLOYMENT-NAME]` - undo 

## Run

`kubectl run nginx --image nginx`
`kubectl run nginx --image=nginx --dry-run=client -o yaml` - generate pod manifest yaml file done create resource
