## Install
```bash
# Install all helm / manifest
kubectl kustomize --enable-helm . | kubectl apply -f -

# Delete all helm / manifest
kubectl kustomize --enable-helm . | kubectl delete -f -
```

## Admin kafka
You can find kafka management script directly inside the pod
```bash
# replace kafka-0 with your kafka pod
kubectl exec -it kafka-0 -- /bin/bash -c "cd /opt/bitnami/kafka/bin && ls && /bin/bash" 
```

## Access Kowl
```bash
minikube service -n kafkanalytics kowl
# a bit messy, can't delete topic due to default values
```

## Use kcat
Find KafkaCat documentation [here](https://docs.confluent.io/platform/current/app-development/kafkacat-usage.html)
```bash
# replace 'kafka-0' with your kafka broken pod
# replace 'kafka-headless' with your hs svc
kubectl exec -it kcat -- kcat -b kafka-0.kafka-headless.kafkanalytics.svc.cluster.local [kcat cmd] 

# ProTip
alias kcat="kubectl exec -it kcat -- kcat -b kafka-0.kafka-headless.kafkanalytics.svc.cluster.local"
```

## ksqlDB
[ksqlDB documentation](https://ksqldb.io/examples.html#create-a-stream-over-an-existing-kafka-topic)
```bash 
kubectl exec -it ksql-cli -- ksql http://ksql-server.kafkanalytics.svc.cluster.local:8088
```
