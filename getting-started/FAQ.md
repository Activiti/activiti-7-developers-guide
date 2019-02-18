# Activiti Cloud FAQ

## postgreSQL configuration
our activiti cloud full example rely on postgres for persistent data storage. But when I deploy activiti cloud on kubernetes( using kubeadm, single node cluster) I found our activiti cloud full example stuck. I tried several methods to find out the reason, finally I found it was nothing dealing with activiti cloud.
The steps to reproduce this situation:
* helm install --name my-release stable/postgresql
* open kubernetes dashboard, default namespace.
* you will find error: unbound immediate persistentvolumeclaims

It is because my kubernetes cluster lack of persistent Volume. 
Some articles said that when you create persistent volume claims, kubernetes will automatically bind matched pv to your pvc. 
But my kubernetes cluster is just a single node cluster on CentOS7. It didn't have any cloud stoarge or nfs etc.
So I have to create my own persistent volume, using this script:
```
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
```

Then open postgres yml config file(using dashboard), remove one line:   "subPath":"postgresql-db", then click update button.
After that the error disappeared.

Hope this faq may help some guys. Good Luck! 
