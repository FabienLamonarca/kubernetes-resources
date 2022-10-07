## bash inside a pod
```bash
1 - kubectl exec --stdin --tty pods/{POD_NAME} -- /bin/bash
2 - kubectl exec -i -t pods/{POD_NAME} -- /bin/bash
3 - kubectl exec -it pods/{POD_NAME} -- bash
```

### examine dns

Use kubectl run to execute a container that provides the `nslookup` command from the dnsutils package. 
Using nslookup on the Pods' hostnames, you can examine their in-cluster DNS addresses:

```bash
kubectl run -i --tty --image busybox:1.28 dns-test --restart=Never --rm
```

This starts a new shell. where you can run: `nslookup web-0.nginx`