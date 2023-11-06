# WIK-DPS-tp04

## PART 1 - Pod

Create a new pod named `api-pod` with the image `registry.cluster.wik.cloud/public/echo` and expose it on port `8080`.

### Configuration file:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api-pod
spec:
  containers:
    - name: api-container
      image: registry.cluster.wik.cloud/public/echo
```
### Apply the configuration file:
```bash
kubectl create -f api-pod.yaml
```
### Check the pod:
```bash
  main S:1 U:1  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       11:59:14  lexit 
❯ kgpo
NAME                          READY   STATUS    RESTARTS   AGE
api-pod                       1/1     Running   0          2m
hello-node-7579565d66-g7rmt   1/1     Running   0          47m
```
Expose the pod on port `8080`:
```bash
kubectl port-forward api-pod 8080:8080
```
### Test the pod:
```bash
curl localhost:8080
```
Output:
- server-side:
    ```bash
    ^C  main S:2 U:2  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                     12:07:31  lexit
    ❯ kpf api-pod 8080:8080
    Forwarding from 127.0.0.1:8080 -> 8080
    Forwarding from [::1]:8080 -> 8080
    Handling connection for 8080
    ```
- client-side:
    ```bash
     main S:2 U:2  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:06:36  lexit
    130 ❯ curl localhost:8080/ping
    {"host":"localhost:8080","user-agent":"curl/7.81.0","accept":"*/*"}
    ```
  
### Clear
```bash
  main S:3 U:3  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:12:35  lexit 
 1 ❯ krm pod api-pod
pod "api-pod" deleted
```

## PART 2 - Replicaset

Create a new replicaset named `api-replicaset` with the image `registry.cluster.wik.cloud/public/echo` and expose it on port `8080`.

### Configuration file:
```yaml
# Description: ReplicaSet for api pods
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: api-replicaset
spec:
  replicas: 4
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
        - name: api-container
          image: registry.cluster.wik.cloud/public/echo

```

### Apply the configuration file & check the replicaset:

```bash
  main S:3 U:3  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:13:13  lexit 
❯ ka replicaset-api-pod.yaml 
replicaset.apps/api-replicaset created
  main S:3 U:3  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:18:19  lexit 
❯ kgpo
NAME                          READY   STATUS    RESTARTS   AGE
api-replicaset-7vsqd          1/1     Running   0          6s
api-replicaset-cf77f          1/1     Running   0          6s
api-replicaset-csxzb          1/1     Running   0          6s
api-replicaset-dps9d          1/1     Running   0          6s
hello-node-7579565d66-g7rmt   1/1     Running   0          65m
  main S:3 U:3  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:18:25  lexit 
❯ kg replicaset
NAME                    DESIRED   CURRENT   READY   AGE
api-replicaset          4         4         4       68s
hello-node-7579565d66   1         1         1       3h16m
```

## PART 3 - Deployment

Create a new deployment named `api-deployment` with the image `registry.cluster.wik.cloud/public/echo` for update strategy use `RollingUpdate`.

### Configuration file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
        - name: api-container
          image: registry.cluster.wik.cloud/public/echo
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 50%
```

## part 4 - Service

1. Add service to `replicaset-api-pod.yaml`:
```yaml
---
# service for api-replicaset pods forwarding traffic to port 8080
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  selector:
    app: api
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
  type: ClusterIP
```

2. Update pods configuration

```bash
  main S:3 U:3  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:24:44  lexit 
❯ ka replicaset-api-pod.yaml 
replicaset.apps/api-replicaset unchanged
service/api-service created
```

3. check

```bash
  main S:4 U:4  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:30:00  lexit 
❯ kg services
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
api-service   ClusterIP   10.106.241.60   <none>        8080/TCP   62s
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP    17h
  main S:4 U:4  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:30:36  lexit 
❯ kpf api-service 8080:8080
Error from server (NotFound): pods "api-service" not found
  main S:4 U:4  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       12:30:55  lexit 
 1 ❯ kpf service/api-service 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080
```
```bash
❯ curl localhost:8080/ping
{"host":"localhost:8080","user-agent":"curl/7.81.0","accept":"*/*"}
```

## part 5 - Ingress

1. Configuration file `api-ingress.yaml`:
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: echo-ingress
    spec:
      rules:
        - host: rust-api.local
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: api-service
                    port:
                      number: 8080
    ```
2. Create minikube ingress controller:
    ```bash
    minikube addons enable ingress
    ```
3. Open tunnel to minikube:
    ```bash
      main S:6 U:6  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       14:20:25  lexit 
     1 ❯ minikube tunnel
    W1106 14:20:33.749780  214725 main.go:291] Unable to resolve the current Docker CLI context "default": context "default": context not found: open /home/lexit/.docker/contexts/meta/37a8eec1ce19687d132fe29051dca629d164e2c4958ba141d5f4133a33f0688f/meta.json: no such file or directory
    Place your right index finger on the fingerprint reader
    Failed to match fingerprint
    Place your right index finger on the fingerprint reader
    Status:	
        machine: minikube
        pid: 214725
        route: 10.96.0.0/12 -> 192.168.49.2
        minikube: Running
        services: []
        errors: 
            minikube: no errors
            router: no errors
            loadbalancer emulator: no errors
    ^CPlace your right index finger on the fingerprint reader
    ```

4. update `/etc/hosts`:
    ```bash
    sudo echo "192.168.49.2 rust-api.local" >> /etc/hosts
    ```

5. Apply the configuration file:
    ```bash
      main S:6 U:6  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       14:39:01  lexit 
    ❯ ka api-ingress.yaml 
    ingress.networking.k8s.io/echo-ingress created
    ```
6. Test:
    ```bash
      main S:6 U:6  ~/Ynov/DevOps/wik-dps-tp04                                                                                                                                                       14:24:56  lexit 
    ❯ curl rust-api.local/ping
    {"host":"rust-api.local","x-request-id":"9b80fcda241aa20145458227daaeb522","x-real-ip":"192.168.49.1","x-forwarded-for":"192.168.49.1","x-forwarded-host":"rust-api.local","x-forwarded-port":"80","x-forwarded-proto":"http","x-forwarded-scheme":"http","x-scheme":"http","user-agent":"curl/7.81.0","accept":"*/*"}  main S:6 U:6  ~/Ynov/DevOps/wik-dps-tp04                                                       14:25:00  lexit 
    ❯ curl rust-api.local/ping -v
    *   Trying 192.168.49.2:80...
    * Connected to rust-api.local (192.168.49.2) port 80 (#0)
    > GET /ping HTTP/1.1
    > Host: rust-api.local
    > User-Agent: curl/7.81.0
    > Accept: */*
    > 
    * Mark bundle as not supporting multiuse
    < HTTP/1.1 200 OK
    < Date: Mon, 06 Nov 2023 13:29:41 GMT
    < Content-Type: application/json
    < Transfer-Encoding: chunked
    < Connection: keep-alive
    < 
    * Connection #0 to host rust-api.local left intact
    {"host":"rust-api.local","x-request-id":"295584ceced35982d70fd608326b5730","x-real-ip":"192.168.49.1","x-forwarded-for":"192.168.49.1","x-forwarded-host":"rust-api.local","x-forwarded-port":"80","x-forwarded-proto":"http","x-forwarded-scheme":"http","x-scheme":"http","user-agent":"curl/7.81.0","accept":"*/*"}
    ```


