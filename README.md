## Goldilocks

Goldilocks is a utility that can help you identify a starting point for resource requests and limits.

**To deploy the Metrics Server**

Deploy the Metrics Server with the following command:  
```ruby
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
 Since the metric server will not open properly, the following parameters must be added.
```ruby
kubectl edit -n kube-system deployments.apps metrics-server
```
```ruby
- args:
- --cert-dir=/tmp
- --secure-port=4443
- --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
- --kubelet-use-node-status-port
- --authorization-always-allow-paths=/livez,/readyz
- --kubelet-insecure-tls
image: k8s.gcr.io/metrics-server/metrics-server:v0.4.1
```
 Make sure metric-server pod is running
```ruby
kubectl get po -n kube-system
```
Check if metric server is running
```ruby
kubectl top nodes
```
### Vertical Pod Autoscaler

To install VPA, please download the source code of VPA and run the following command inside the `vertical-pod-autoscaler` directory:([URL](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler))
```ruby
git clone [https://github.com/kubernetes/autoscaler.git](https://github.com/kubernetes/autoscaler.git)
```
```ruby
cd autoscaler/vertical-pod-autoscaler/
```
```ruby
./hack/vpa-up.sh
```
Check if all system components are running:
```ruby
kubectl --namespace=kube-system get pods|grep vpa
```
Test your installation
```ruby
kubectl create -f examples/hamster.yaml
```
```ruby
kubectl describe vpa
```
### Goldilocks

The [hack/manifests (opens new window)](https://github.com/FairwindsOps/goldilocks/tree/master/hack/manifests)directory contains collections of Kubernetes YAML definitions for installing the controller and dashboard components in cluster.

```ruby
git clone https://github.com/FairwindsOps/goldilocks.git
cd goldilocks
kubectl create namespace goldilocks
kubectl -n goldilocks apply -f hack/manifests/controller
kubectl -n goldilocks apply -f hack/manifests/dashboard
```
**Enable Namespace**

Pick an application namespace and label it like so in order to see some data:
```ruby
kubectl label ns goldilocks goldilocks.fairwinds.com/enabled=true
```
After that you should start to see VPA objects in that namespace.

**Viewing the Dashboard**

The default installation creates a ClusterIP service for the dashboard. You can access via port forward:

```ruby
kubectl -n goldilocks port-forward svc/goldilocks-dashboard 8080:80
```
or

change service type to nodeport

Result

![image](https://user-images.githubusercontent.com/3519706/106140905-70bbdc00-6180-11eb-976e-1e892781e8e9.png)
