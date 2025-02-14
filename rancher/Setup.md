# Rancher Setup instructions
## Cert Manager
Add the helm repo for Cert Manager to it:
```
helm repo add jetstack https://charts.jetstack.io
```
Create a New Namespace for Cert-Manager:
```
kubectl create namespace cert-manager
```
Install the CustomResourceDefinitions of cert-manage:
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.16.3/cert-manager.crds.yaml
````
### Installing Cert Manager
As of now the version is v1.16.3 so we use the latest in stable:
```
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.16.3
```
You can check the deployment:
```
kubectl get pods -n cert-manager
```
* Setup Ngnix Loadbalancer
We need to make adjustents to our ngnix to be able to be used by rancher.
The ngnix File is located at `/etc/nginx/nginx.conf`
```
upstream rancher_server_http {
    least_conn;
    server 192.168.100.41:80 max_fails=3 fail_timeout=5s;
    server 192.168.100.42:80 max_fails=3 fail_timeout=5s;
}
upstream rancher_server_https {
    least_conn;
    server 192.168.100.41:443 max_fails=3 fail_timeout=5s;
    server 192.168.100.42:443 max_fails=3 fail_timeout=5s;
}
```

# Installing Rancher
## Preconfigure
Create a namespace:  
```
kubectl create namespace cattle-system
```
Add the rancher repo:  
```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```  
Now Install Rancher with the helm chart:
```
helm upgrade --install rancher rancher-stable/rancher \
   --namespace cattle-system \
   --set hostname=rancher.denniscloud.io 
```
Check the rollout status:
```
kubectl -n cattle-system rollout status deploy/rancher
```
