There are many workflows around Kubernetes that need access to one more container registries. While public registries are commonly referenced there is privacy concerns and no need to shuttle private images over the internet. Instead, private registries keep all this pushing and pulling in a local registry.

There are many options for standing up a container registry. We prefer a pure Kubernetes solution and install a registry through the [stable Helm chart](https://github.com/helm/charts/tree/master/stable/docker-registry#docker-registry-helm-chart).

Add the Harbor chart report to Helm.

`helm repo add harbor https://helm.goharbor.io`{{execute}}

Install Harbor from the Helm chart.

`helm install --name harbor harbor/harbor --namespace harbor \
--set persistence.enabled=false \
--set expose.tls.enabled=false \
--set expose.type=nodePort \
--set expose.nodePort.ports.http.nodePort=31500`{{execute}}

The registry is now available as a service. It can be listed.

`kubectl get service --namespace harbor`{{execute}}

The Docker tag, push and pull commands must all have the same host name for the image. Docker also requires SSL access, so its necessary to consistently refer to the registry from the command line and from within the cluster. In this scenario case we can simply use 127.0.0.0. Use port-forward to expose the registry.

`kubectl port-forward --namespace harbor \
$(kubectl get po -n harbor | grep harbor-registry | \
awk '{print $1;}') 5000:5000 &`{{execute}}

Assign an environment variable to the common registry location.

`export REGISTRY=127.0.0.1:31500`{{execute}}


This chart bootstraps a Harbor instance consisting of several deployed components. To get a complete status of the deployment availability run this inspection.

`watch kubectl get deployments,pods,services --namespace harbor`{{execute}}

Once complete, the Pods will move to the _running_ state. The Harbor server takes about 2 minutes to start. The Deployments will eventually move to the _Available (1)_ state. Use this ```clear```{{execute interrupt}} to ctrl-c and clear the shell or press ctrl-c to break out of the watch.

Once the registry is serving, inspect the contents of the empty registry.

`curl $REGISTRY/v2/_catalog`{{execute}}