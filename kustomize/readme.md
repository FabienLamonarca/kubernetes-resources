# Kustomize

Kustomize is a tool for customizing Kubernetes configurations:
- generating resources from other sources
- setting cross-cutting fields for resources
- composing and customizing collections of resources

It's based on a `kustomization.yaml` file.

More official Kustomize feature list can be found [here](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#kustomize-feature-list)

### Usage
```bash
kubectl kustomize . # Will find kustomization.yaml then render the associated template

kubectl apply -k kustomize.yaml # will build then apply the resources from the kustomize file

kubectl delete -k kustomize.yaml # same here but delete

kubectl diff -k ./ # Run the following command to compare the manifest object against the state that the cluster would be in if it was applied
```

## Features

### Composing

```yaml
# kustomization.yaml
resources:
- deployment.yaml
- service.yaml
```

### configMapGenerator 

```yaml
# application.properties
FOO=Bar

# kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
---
OR
---
configMapGenerator:
- name: example-configmap-2
  literals:
  - FOO=Bar
```

### secretGenerator 
```yaml
# password.txt
username=admin
password=secret

# kustomization.yaml
secretGenerator:
- name: example-secret-1
  files:
  - password.txt
---
OR
---
secretGenerator:
- name: example-secret-2
  literals:
  - username=admin
  - password=secret
```

### generatorOptions 
```yaml
# kustomization.yaml
generatorOptions:
  disableNameSuffixHash: true # Will disable suffix hash for generated resources
  labels:
    type: generated # All resources created from this kustomization file will have those labels 
  annotations:
    note: generated # All resources created from this kustomization file will have those annotations 
```

### Setting cross-cutting fields 
```yaml
# kustomization.yaml
namespace: my-namespace # set namespace for resources
namePrefix: dev- # prefix all resources name
nameSuffix: "-001" # suffix all resources name
commonLabels: # set labels for all resources
  app: bingo
commonAnnotations: # set anotation for all resources
  oncallPager: 800-555-1212
resources: # List of kubernetes manifest to deploy for this kustomization file
- deployment.yaml
```