# Persistant Storage

## Why do we need storage ?

By nature pod are ephemerals and can be replaced at any time. 

When container write data on his filesystem, this data is lost when recreating an instance of this pod. 

Kubernetes doesn't provide storage out the box for pods, so we need to configure it by ourselves.

## Storage requirements

1. We need storage that doesn't depends on the pod lifecycle
2. We need storage that must be available on all nodes because a pod can be scheduled on any node of the cluster
3. We need storage that survive cluster crashes


## 1 - Persistant volume (PV)

PV is a cluster resource created via a YAML file
- kind : **PersistentVolume**
- spec: Define the type and volume specs

It need actual physical storage
- Local disk
- NFS server
- cloud storage
- etc.

Persistent volumes **are not namespaced** and are available accross the cluster


### Where does this storage come from ? 

We have to decide what kind of storage we need, then **Create and manage them by ourself**

Managing implies: 
- Backups
- protect from data corrupt
- etc.

Storage in kubernetes is an external thing of the cluster, no matter if this is local or cloud store

We can use different type of storage at the same time


### Local vs Remote volumes types

Each volume type has it's own use case

Local volumes are not valid regarding points #2 and #3 of Storage requirements above
- They are tied to one specific node
- They do not survive a cluster crash

**For database persistence, always use remote storage**


### Who creates the PV and when ?

PV are resources that **needs to be there BEFORE** the pod that depends on it is created

We define two user roles in kubernetes
- K8S administrator : 
    - setup the cluster and maintain it
    - make sur the cluster have enough ressources
    - these are usually system administrator or devops engineer in a company
- K8S users :
    - deploys applications inside the cluster

**Storage is provisioned by administrator**
- create the storage itself
- create it as PV in kubernetes


## 2 - Persistant Volume Claim (PVC)

In order to have storage for applications, users have to claim the persistent volume

- PVC are also created by YAML configuration manifest

- 1 : Pod requete a volume through the PV claim
- 2 : Claim tries to find a volume in cluster
- 3 : Volume has the actual storage backend

Persistent Volumes Claims **are namespaced**


## 3 - Storage classes

In a pod we can have different volumes types
- Secret (Password, token, certificates... sensible stuff)
- ConfigMap (application configuration files, KV pairs, etc.)
- PVC for data store (awsElasticBlockStore, etc.)

Storages process
- 1: Admin configure storage backend
- 2: Admin create persistent storage in kubernetes
- 3: Users claim PV using PVC

Basically, user ask admin to create PV when they need it, and admin have to manually create PV.
This can be painful, time-consumming and messy very quickly

To adress this issue, we have the 3rd k8s component: **Storage Class**

- SC provisions PV **dynamically** when PVC claims it
- SC are also create by YAML manifests
- To use SC, we have to mention it into the PVC resource