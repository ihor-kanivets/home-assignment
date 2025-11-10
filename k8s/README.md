# Httpbin Kubernetes Deployment
This repository contains Kubernetes manifests to deploy the `kennethreitz/httpbin` latest image.

## Kubernetes resources installation/uninstallation:
    1. Clone the repository with the next command: `git clone https://github.com/ihor-kanivets/home-assignment`
    2. Go to the `k8s/httpbin` folder;
    3. Create dedicated namespace: `kubectl apply -f namespace.yaml';
    4. Create a deployment: `kubectl apply -f deployment.yaml';
    5. Create a service: `kubectl apply -f service.yaml';
    6. Create a HorizontalPodAutoscaler: `kubectl apply -f hpa.yaml'.

    To remove all the `httpbin` related resources, run the commands below:
    Delete the namespace: `kubectl delete -f namespace.yaml`;

## Components
 - **Namespace**: Required to isolate `httpbin` resources;
 - **Deployment**: Manages resources, limits, security, and settings;
 - **Service**: Exposes application within the cluster;
 - **HPA**: Horizontal Pod Autoscaler for scaling the number of pods based on CPU load (for now);
 - **NetworkPolicy**: Pod communication restriction;
 - **ResourceQuota**: Resources consumption limits;

 ## Verification
 To verify that the application works as expected the following actions are required:

 1. After the required resources installation, let's check that pods are in the 'Running' status with the command 
 'kubectl get pods -A -n httpbin', which should print the next results: 
    ```
    NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
    httpbin       httpbin-67f8d7d455-2xg97           1/1     Running   0             8m48s
    httpbin       httpbin-67f8d7d455-jfbgx           1/1     Running   0             8m48s
    httpbin       httpbin-67f8d7d455-nd59r           1/1     Running   0             8m19s
    httpbin       httpbin-67f8d7d455-t8gnk           1/1     Running   0             8m48s
    ```
 2. Next - let's verify that the application may handle requests properly: 
    - Run the next command to allow the application to listen on the 8080 port:
    `kubectl port-forward -n httpbin svc/httpbin 8080:80`, output should be the next:
    ```
    kubectl port-forward -n httpbin svc/httpbin 8080:80
    Forwarding from 127.0.0.1:8080 -> 80
    Forwarding from [::1]:8080 -> 80
    ```
    - Open a new terminal and try to reach the service with the command below:
    `curl -s http://localhost:8080/get`, it should return the next result:
    ```
    {
    "args": {}, 
    "headers": {
        "Accept": "*/*", 
        "Host": "localhost:8080", 
        "User-Agent": "curl/8.7.1"
    }, 
    "origin": "127.0.0.1", 
    "url": "http://localhost:8080/get"
    }
    ```
    - At the same time with each run of the `curl -s http://localhost:8080/get` command, you should see an additional line added at the terminal with each 
    request to the service, where you started port forwarding for the service (`kubectl port-forward -n httpbin svc/httpbin 8080:80`), like the output below.
    ```
    Forwarding from 127.0.0.1:8080 -> 80
    Forwarding from [::1]:8080 -> 80
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    Handling connection for 8080
    ```

## Security
The security setting below have been applied additionally to follow the best practices:
```
- Pod Security Context:
    fsGroup: 1000 - Sets ownership of volume files to group 1000;
    runAsNonRoot: true - Prevents containers from running as root user;
    runAsUser/runAsGroup: 1000 -  Runs processes with specific non-root identity;
    seccompProfile: RuntimeDefault - Restricts available system calls (reboot, shutdown, etc...);
- Container Security Context:
    allowPrivilegeEscalation: false - Prevents privilege escalation;
    capabilities.drop: [ALL] - Removes all Linux capabilities;
    privileged: false - Prevents privileged container execution;
    readOnlyRootFilesystem - Commented out due to application requirements;
- Network Security:
    Network Policy - Controls pod-to-pod communication;
    Ingress rules - Control inbound traffic to pods;
    Egress rules - Restrict outbound traffic (DNS and essential services only);
- Resource Controls:
    Resource Limits - Restrict individual container resource usage;
    Resource Quota: Prevents namespace resource break the limit;
```