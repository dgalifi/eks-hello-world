# eks-hello-world

## Prerequisites
 - eksctl installed
 - aws credentials via .aws/crendentials, env variables or via profile
 - make sure to also set the AWS_REGION


## Create the EKS Cluster using eksctl
```
eksctl create cluster \
    --name eks-test-cluster \
    --version 1.32 \
    --region eu-west-1 \
    --node-type t2.micro \
    --nodes 5

```
leave the number of nodes value set to 5

##  Ingress controller
deploy the ingress controller using helm
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```
test deployment was successful
```
kubectl get service ingress-nginx-controller --namespace=ingress-nginx
```
this will create a classic Load Balancer in your AWS account which will work as an ingress to the cluster

## Deploy applications

deploy config first
```
kubectl apply -f quick-app-config.yaml
```

deploy your pods
```
kubectl apply -f quick-app.yaml
kubectl apply -f client.yaml
```

deploy the ingress 
```
kubectl apply -f ingress.yaml
```


## Test it
grab the loadbalancer DNS name and run the following to hit the client app
```
curl http://<lb-dns_name>/
```

this to directly hit the quick-app
```
curl http://<lb-dns_name>/app
```
stay frosty!

## Delete the cluster when done
```
eksctl delete cluster --region=eu-west-1 --name=test-cluster
```

## Useful commands
```
kubectl get nodes
kubectls get pods
kubectl get all
kubectl get ingress-controller
kubectl get svc
kubectl get deployments
```

set namespace with 

```--namespace <_namespace_>  option```


### if you want to scale up the number of nodes you need to 
- grab the nodegroup name with
```
eksctl get nodegroup --cluster eks-test-cluster --region eu-west-1
```

then run 

```
eksctl scale nodegroup --cluster eks-test-cluster --name <node_group_name> --nodes 8 --nodes-max 10  --region eu-west-1
```