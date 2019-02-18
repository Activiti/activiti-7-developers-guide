# Activiti Cloud FAQ

## postgreSQL configuration
our activiti cloud full example rely on postgres for persistent data storage. But when I deploy activiti cloud on kubernetes( using kubeadm, single node clust) I found our activiti cloud full example stucked. I tried several methods to find the reason, finally I found it nothing dealing with activiti cloud.
The steps to reproduce the situation:
* helm install --name my-release stable/postgresql
* open kubernetes dashboard, default namespace.
* you will see error: unbound immediate persistentvolumeclaims

It is because my kubernetes cluster lack of persistent Volume. 
Some article said, when you create persistent volume claims, kubernetes will automatically bind match pvs to your pvc. 
But my kubernetes cluster is just a single node on CentOS7. It didn't have any cloud stoarge or nfs etc.
So I have to create my own persistent volume, using this script:
(```)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-directory-pv
  labels:
    type: local
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  hostPath:
    path: /data/postgres_data
(```)

Then open postgres yml config file, remove one line:   "subPath":"postgresql-db".

I think if you deploy activiti cloud on "Cloud", you may not face such error. Good Luck! Hope help some guys.