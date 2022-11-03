# Staging an update

You can stage an update to a **StatefulSet** by using the partition parameter of the `RollingUpdate` update strategy.

A staged update will keep all of the Pods in the **StatefulSet** at the current version while allowing mutations to the **StatefulSet's** `.spec.template.`

All pod with ordinal number lesser than the `rollingUpdate.partition` will be stuck a the current version

```yaml
...
spec:
updateStrategy:
type: RollingUpdate
rollingUpdate:
partition: 2
...
```