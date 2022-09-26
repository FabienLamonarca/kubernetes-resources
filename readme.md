## bash inside a pod
```bash
1 - kubectl exec --stdin --tty pods/{POD_NAME} -- /bin/bash
2 - kubectl exec -i -t pods/{POD_NAME} -- /bin/bash
3 - kubectl exec -it pods/{POD_NAME} -- bash
```