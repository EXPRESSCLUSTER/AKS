# Deploy Application
## clpwitnessd
1. Create the following file (e.g. deployment.yaml).
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: clpwitnessd
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: clpwitnessd
     template:
       metadata:
         labels:
           app: clpwitnessd
       spec:
         containers:
         - name: clpwitnessd
           image: docker.io/expresscluster/clpwitnessd:latest
           ports:
           - containerPort: 80
             name: clpwitness
   ```
1. Apply the yaml file and check the pod is running.
   ```sh
   $ kubectl apply -f deployment.yaml
   $ kubectl get deployment,pod
   NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
   deployment.apps/clpwitnessd   1/1     1            1           26m
   
   NAME                              READY   STATUS    RESTARTS   AGE
   pod/clpwitnessd-fcf8796f7-svckh   1/1     Running   0          26m
   ```
1. Login the container and check the process is running.
   ```sh
   $ kubectl exec -it pod/clpwitnessd-fcf8796f7-svckh -- bash
   root@clpwitnessd-fcf8796f7-svckh:/# ps ax
     PID TTY      STAT   TIME COMMAND
       1 ?        Ssl    0:08 node /usr/local/bin/clpwitnessd
      23 pts/0    Ss     0:00 bash
      29 pts/0    R+     0:00 ps ax
   root@clpwitnessd-fcf8796f7-svckh:/# curl 127.0.0.1 -v
   * Rebuilt URL to: 127.0.0.1/
   *   Trying 127.0.0.1...
   * TCP_NODELAY set
   * Connected to 127.0.0.1 (127.0.0.1) port 80 (#0)
   > GET / HTTP/1.1
   > Host: 127.0.0.1
   > User-Agent: curl/7.52.1
   > Accept: */*
   >
   < HTTP/1.1 200 OK
   :
   ```
1. Create the following file (e.g. service.yaml).
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: clpwitnessd
   spec:
     type: LoadBalancer
     ports:
     - protocol: "TCP"
       port: 80
       targetPort: 80
     selector:
       app: clpwitnessd
   ```
1. On EXPRESSCLUSTER WebUI, add a witness heartbeat resource to the cluster.
   - For more detail, refer to Reference Guide.
      - Windows: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/504/W42_RG_EN/W_RG_02.html#interconnect-tab
      - Linux: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/505/L42_RG_EN/L_RG_02.html#interconnect-tab
