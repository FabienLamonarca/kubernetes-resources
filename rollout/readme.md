# Rollout

## Basic rollout strategy

### Recreating deployment

Recreating deployment **terminates all the pods** and **replaces them** with the new version

- 🟢 **Easy** to understand
- 🟢 Useful in situations where an old and new version of the application **cannot run at the same time**
- 🔴 The amount of **downtime** incurred using this strategy will **depend on how long the application takes to restart**

```yaml
spec:
    replicas: 10
    strategy:
        type: Recreate
```

### Rolling deployment

**Rolling deployments are the default K8S** offering designed to **reduce downtime** to the cluster

To achieve this, **Readiness probes** are used to monitor when the application becomes available. If the probes fail, no traffic will be sent to the pod

- 🟢 No downtime
- 🟢 Drive to implement **Readiness probes**
- 🟠 **Can be time consuming** : each pod is replaced one by one, deployments can take time for larger clusters

```yaml
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 3
```

- **MaxSurge** : specifies the maximum number of pods the Deployment **is allowed to create at one time**
- **MaxUnavailable** : specifies the maximum number of pods that are **allowed to be unavailable** during the rollout

## Advanced rollout strategy

### Blue/Green deployments

A Blue/Green deployment involves deploying the **new application version (green) alongside the old one (blue)** <br/>

This deployment implies the presence of a **load balancer** to switch between applications

- 🟢 An application **can be fully tested and verified** before receiving traffic
- 🟢 **No downtime**
- 🔴 **Costly** : need to have the two application fully running during the deployment process

```yaml
# Service selector for blue deployment for an app called web-app with v1.0.0
kind: Service
metadata:
name: web-app-01
labels:
    app: web-app
    selector:
        app: web-app
        version: v1.0.0

# And the deployment for the blue web app:
kind: Deployment
metadata:
name: web-app-01
spec:
    template:
        metadata:
            labels:
                app: web-app
                version: "v1.0.0"

# When we want to direct traffic to the new (green) version of the app,
# we update the manifest to point to the new version, v2.0.0.
kind: Service
metadata:
name: web-app-02
labels:
    app: web-app
    selector:
        app: web-app
        version: v2.0.0

#The deployment for the green app:
kind: Deployment
metadata:
name: web-app-02
spec:
    template:
        metadata:
            labels:
                app: web-app
                version: "v2.0.0"
```

### Canary deployment

A Canary deployment can be **used to let a subset of the users test a new version** of the application or **when you are not fully confident** about the new version’s functionality <br/>
This involves deploying a new version of the application **alongside the old one**, with the old version of the application serving **most users** and the newer version serving a **small pool of test users**
The new deployment is rolled out to more users if it is successful

- 🟢 Safe deploy : Real application with real traffic to monitor the behavior of the new one without impacting all users
- 🟢 User centric : testable
- 🟠 Manual operation : You have to do the deployment by yourself

```yaml
# The spec section of the old application manifest would look like the following:
spec:
    replicas: 95

# And the new application manifest:
spec:
    replicas: 5
```

### A/B Deployments

Similar to a Canary deployment, you can **target a given subsection of users** based on **target parameters**, usually the HTTP headers, GET params or a cookie <br/>
This technique is widely used to **test the conversion of a given feature**, and then the **version that converts the most is rolled out**

This approach is usually taken based on data collected on **user behavior** and can be used to make better **business decisions** <br/>
Users are usually **uninformed** of the new features during the A/B testing period, so **true testing** can be done <br/>

- 🟢 **No downtime**
- 🟢 **Business centric** : compare feature conversion between multiples version
- 🟠 Rollouts **can be slower** due to the additional testing period and analysis of the user experience
