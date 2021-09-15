# Download Istio for your SO
https://github.com/istio/istio/releases/

### Move to the Istio package directory. For example, if the package is istio-1.11.2:
```
cd istio-1.11.2
```
### The installation directory contains:
```
Sample applications in samples/
The istioctl client binary in the bin/ directory.
```
## Install Istio

```
istioctl install --set profile=demo -y
```

## Install Kiali, Prometheus, Jaeger and Grafana
```
cd samples/addons
kubectl apply -f kiali.yaml
kubectl apply -f prometheus.yaml
kubectl apply -f jaeger.yaml
kubectl apply -f grafana.yaml
```

### Open Kiali Dashboard
```
istioctl dashboard kiali
```
## Create namespace and add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later
```
kubectl create namespace grpc
kubectl label namespace grpc istio-injection=enabled
```

## Deploy application
```
kubectl apply -f .\src\deployment.yml
kubectl apply -f .\src\gateway.yml
kubectl apply -f .\src\service.yml
kubectl apply -f .\src\virtual-service.yml
```

### Interact with your app through ```grpcurl```
```
# Curling against the istio-ingressgateway
grpcurl -plaintext $(kubctl get svc -n istio-system istio-ingressgateway -ojsonpath="{.status.loadBalancer.ingress[0].ip}"):80 list

# Describe proto
grpcurl -plaintext $(kubctl get svc -n istio-system istio-ingressgateway -ojsonpath="{.status.loadBalancer.ingress[0].ip}"):80 describe br.com.me.proto.HelloService

# Use endpoint
grpcurl -plaintext -d '{"name":"chrome"}'  $(kubctl get svc -n istio-system istio-ingressgateway -ojsonpath="{.status.loadBalancer.ingress[0].ip}"):80 br.com.me.proto.HelloService/SayHello

```