So far the mount types we have been exploring have been within the containers, Pods, and Nodes. The next mount type is at the cluster level.

## HostPath

Like the last example we still with use _hostPath_. _hostPath_ is a connection to a path on a Worker Node in the cluster. Where the Pod is hosted is where the path is accessed. This is helpful for testing in single Node clusters. Because Nodes are ephemeral, _hostPath_ should not be used in production deployments. In this scenario you have a two node cluster, the worker node is called `node01`.

`kubectl get nodes`{{execute}}

In the previous step the _hostPath_ was defined right in the Pod spec section. For this step we introduce two important Kubernetes resources; a PersistentVolume and a PersistentVolumeClaim. Pods can connect their mounts to these PersistentVolumeClaims and the claims are bound to PersistentVolumes. The two constructs provide layers of indirection to keep your application decoupled from the numerous and changeable choices of file mounts.

## PersistentVolume

First, define a PersistentVolume.

`ccat local-storage-pv.yaml`{{execute}}

Within the specification a declaration is made to allocate a volume at /mnt/data on the host as _local-storage_ on the _Filesystem_. A 3Gi volume will be reserved. This is simply making the declaration that a new volume is available. This declaration uses the HostPath. For production systems this PersistentVolume can be connected many different types of persistent file stores.

Submit this PersistentVolume (PV) to the cluster.

`kubectl apply -f local-storage-pv.yaml`{{execute}}

Inspect this newly created PV:

`kubectl get pv`{{execute}}

The columns should look familiar as they follow the declarations submitted in the manifest. Notice the _status_ column is `Available` and the _claim_ column is blank. Next, bind a claim to this volume to see the status change.

## PersistentVolumeClaim

Second, define a PersistentVolumeClaim against the PersistentVolume.

`ccat local-storage-pvc.yaml`{{execute}}

Submit this PersistentVolumeClaim (PVC) to the cluster.

`kubectl apply -f local-storage-pvc.yaml`{{execute}}

Inspect this newly created PVC:

`kubectl get pvc`{{execute}}

`kubectl get pv`{{execute}}

Now the _status_ column reads `bound`, This tells you the claim found the volume. Once a claim is bound to a volume, no other claims can be made to the volume while this claim exists. The `local-storage` matching label between the PV and PVC declarations is was allow the pairing to occur. Other declarations are part of hte pairing considering such as the claim has to be a size the same or smaller than the volume size allocation. In this case the sizes matched to allow the binding to occur.

## Mount Pod to PersistentVolumeClaim

We define a simple NGINX Pod.

`ccat local-storage-pod.yaml`{{execute}}

The Pod's mount is it to the same label found in the PVC as `name: nginx-pvc`. Submit this Pod declaration.

`kubectl apply -f local-storage-pod.yaml`{{execute}}

The NGINX Pod is on port 80, so port forward that to 8888 so we can call the web server's main index page.

`kubectl port-forward nginx-hostpath 8001:80  > /dev/null &`{{execute}}

Dump the default index page:

`lynx http://localhost:8001 --dump`{{execute}}

The page is blank. Remember, the PV declared a mount to `hostPath.path: /mnt/data`. Your bash terminal is on the master node, so switch over to the worker node, `node01`.

`ssh node01`{{execute}}

Inspect the directory where the PV is declared for a the hostPath.

`ls /mnt/data`{{execute}}

Nothing there. Create some content.

`echo "Live to learn, learn to live, then teach others. <p>--Douglas Horton" > /mnt/data/index.html`{{execute}}

Return to the master node, by typing `exit` just ***ONCE***. **Do not type exit again as it will end this scenario.**

See if you have updated the website content.

`lynx http://localhost:8001 --dump`{{execute}}

There are hundreds of others volume types you can declare and bind to and we'll get to that next.