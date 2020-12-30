# Deploy Application
## Index
- [clpwitnessd](#clpwitnessd)
- [Cluster WebUI](#cluster-webui)

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
1. Check the IP address and port number.
   ```sh
   $ kubectl get service
   NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)           AGE
   clpwitnessd   LoadBalancer   10.0.202.168   20.63.172.134   80:32067/TCP      171m
   kubernetes    ClusterIP      10.0.0.1       <none>          443/TCP           29h
   ```
1. On EXPRESSCLUSTER WebUI, add a witness heartbeat resource to the cluster.
   - For more detail, refer to Reference Guide.
      - Windows: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/504/W42_RG_EN/W_RG_02.html#interconnect-tab
      - Linux: https://www.manuals.nec.co.jp/contents/system/files/nec_manuals/node/505/L42_RG_EN/L_RG_02.html#interconnect-tab

## Cluster WebUI
1. Create the following file (e.g. deployment.yaml).
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: cwo-win
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: cwo-win
     template:
       metadata:
         labels:
           app: cwo-win
       spec:
         containers:
         - name: cwo-win
           image: docker.io/expresscluster/cwo:4.2.2-200515-1
           command: ["/bin/bash", "-c", " cd /opt/cwo && npx cluster-webui-win --locale=en"]
           ports:
           - containerPort: 29003
             name: cwo-win
   ```
   - The above example is to run Cluster WebUI for Windows cluster. If you want to create Linux cluster, set the following command.
      ```yaml
      command: ["/bin/bash", "-c", " cd /opt/cwo && npx cluster-webui-lin --locale=en"]
      ```
1. Apply the yaml file and check the pod is running.
   ```sh
   $ kubectl apply -f deployment.yaml
   $ kubectl get deployment,pod
   NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
   deployment.apps/cwo-win       1/1     1            1           7m36s
   
   NAME                              READY   STATUS    RESTARTS   AGE
   pod/cwo-win-8667667b4b-776gv      1/1     Running   0          7m36s
   ```
1. Login the container and check the process is running.
   ```sh
   kubectl exec -it cwo-win-8667667b4b-776gv --bash
   root@cwo-win-8667667b4b-776gv:/opt/cwo# ps ax
     PID TTY      STAT   TIME COMMAND
       1 ?        Ssl    0:00 node /usr/local/bin/npx cluster-webui-win --locale=en
      26 pts/0    Ss     0:00 bash
      33 pts/0    R+     0:00 ps ax
   ```
1. Create the following file (e.g. service.yaml).
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: cwo-win
   spec:
     type: LoadBalancer
     ports:
     - protocol: "TCP"
       port: 30003
       targetPort: 29003
     selector:
       app: cwo-win
   ```
1. Check the IP address and port number.
   ```sh
   $ kubectl get service
   NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)           AGE
   cwo-win       LoadBalancer   10.0.54.33     20.48.83.176    30003:31745/TCP   10m
   kubernetes    ClusterIP      10.0.0.1       <none>          443/TCP           29h
   ```
1. Open a web browser and access to Cluster WebUI. 
   - E.g. http://20.48.83.176:30003
