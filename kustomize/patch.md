## Patches

### patchesStrategicMerge

The names inside the patches `must match Resource names` that are already loaded.<br/> 
Small patches that do one thing are recommended.
```yaml
# increase_replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3

# kustomization.yaml
resources:
- deployment.yaml
patchesStrategicMerge:
- increase_replicas.yaml
```

### patchesJson6902

Not all Resources or fields support strategic merge patches through `patchesStrategicMerge`.<br/>
To support modifying arbitrary fields in arbitrary Resources use JSON patch through `patchesJson6902`

```yaml
# patch.yaml
- op: replace
  path: /spec/replicas # jsonPath
  value: 3

# kustomization.yaml
resources:
- deployment.yaml
patchesJson6902:
- target: # Target the resource to apply the patch.yaml
    group: apps
    version: v1
    kind: Deployment
    name: my-nginx
  path: patch.yaml
```

### Overriding image
```yaml 
# kustomization.yaml
resources:
- deployment.yaml
images:
- name: nginx
  newName: my.image.registry/nginx
  newTag: 1.4.0
```