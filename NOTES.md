# kind cluster with calico

```shell
# install KIND - https://github.com/alexellis/arkade?tab=readme-ov-file#getting-arkade
ark get kind

# create cluster
kind create cluster --config kind-calico.yaml

# our new cluster
kubectl get no -o wide
kubectl get pods -n kube-system
kubectl config view 

# install Calico
kubectl delete -f https://docs.projectcalico.org/v3.12/manifests/calico.yaml
watch kubectl get pods --all-namespaces

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/custom-resources.yaml

# watch progress
kubectl get pods -A --watch

kubectl create deploy web --image nginx --port 80
WEBPOD=$(kubectl get po -l app=web -o name | head -1)
kubectl exec -it $WEBPOD -- curl ip.iol.cz/ip/

while true; do kubectl exec -it $WEBPOD -- curl -s -m1 ip.iol.cz/ip/; echo ; sleep 3; done

# deny egress from NS default
#kubectl delete -f allow-all-egress.yaml
kubectl apply -f default-deny-all-egress.yaml

# look at curl loop

kubectl apply -f allow-dns-and-https-egress.yaml
# and remove again
kubectl delete -f allow-dns-and-https-egress.yaml

# look at curl loop

# cleanup
kind delete cluster
```