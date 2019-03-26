## Persistent Volume ##

ElasticSearch will be making a PersistentVolumeClaim for its persistence. A PersistentVolume will be needed. Since this is all temporary in Katacoda, a [hostPath based PersistentVolume](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume) is created.

`mkdir -p /mnt/data/efk && kubectl create -f pv.yaml`{{execute}}

## Install ElasticSearch ##

Deploy the public Helm chart for ElasticSearch. Many of the default parameters are downsized to fit in this KataCoda cluster. 

`helm install stable/elasticsearch --name=elasticsearch --namespace=logs --set client.replicas=1 --set data.replicas=1 --set data.heapSize=300m --set data.persistence.size=300Gi`{{execute}}

It will start in a few moments and you can observe its progress.

`watch kubectl get deployments,pods,services --namespace=logs`{{execute}}

Once complete, the Pods will move to the _running_ state. It will be a few moments and the Deployments will eventually move to the _available (1)_ state. Use this ```clear```{{execute interrupt}} to ctrl-c and clear the shell or press ctrl-c to break out of the watch.