# Base and overlays

Kustomize has the concepts of bases and overlays.

- **`A base`** is a directory with a `kustomization.yaml`, which `contains a set of resources and associated customization`. It could be either a local directory or a directory from a remote repo, as long as a `kustomization.yaml` is present inside.

- **`An overlay`** is a directory with a `kustomization.yaml` that **`refers to other kustomization directories as its bases`**. 

A base has no knowledge of an overlay and can be used in multiple overlays. 

An overlay may have multiple bases and it composes all resources from bases and may also have customization on top of them.

<br/>

# Example

Make these directories : base, dev, prod (dev and prod will be our overlays)

## Base

### **base**/deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
```

### **base**/service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
```

### **base**/kustomization.yaml
```yaml
resources:
- deployment.yaml
- service.yaml
```

<br/>

---

<br/>

## Overlays

### **dev**/kustomization.yaml
```yaml
bases:
- ../base
namePrefix: dev-
```

### **prod**/kustomization.yaml
```yaml
bases:
- ../base
namePrefix: prod-
```