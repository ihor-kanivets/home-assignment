# Httpbin HELM Chart
This repository contains Kubernetes manifests to deploy the `kennethreitz/httpbin` latest image.

## Helm Chart installation/uninstallation:
1. Clone the repository with the next command: `git clone https://github.com/ihor-kanivets/home-assignment`;
2. Go to the `helm/httpbin` folder;
3. To deploy the mentioned Helm chart, please use the next command: `helm install httpbin ./httpbin --namespace httpbin --create-namespace';
4. To upgrade the existing Helm with your settings, please use the next command: `helm upgrade httpbin ./httpbin --namespace httpbin`;
5. To destroy the mentioned Helm chart, the next command should be run: `helm uninstall httpbin  --namespace httpbin`.

## Components
 - **Namespace**: Required to isolate resources related to the current helm;
 - **Deployment**: Manages resources, limits, security, and settings;
 - **Service**: Exposes application within the cluster;
 - **HPA**: Horizontal Pod Autoscaler for scaling the number of pods based on CPU load (for now);
    
 ## Verification
1. After the required resources installation, let's check that pods are in the 'Running' status with the command 
 'kubectl get pods -A -n httpbin', which should print the next results: 
```
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
httpbin       httpbin-67f8d7d455-2xg97           1/1     Running   0             8m48s
httpbin       httpbin-67f8d7d455-jfbgx           1/1     Running   0             8m48s
httpbin       httpbin-67f8d7d455-nd59r           1/1     Running   0             8m19s
httpbin       httpbin-67f8d7d455-t8gnk           1/1     Running   0             8m48s
```

2. To check that the service works as expected - commands below should be run:
If service works as expected, the command 'kubectl --namespace httpbin port-forward $POD_NAME 8080:$CONTAINER_PORT' will 
start listening on the 8080 port and the next output should be printed to the console:
```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
Handling connection for 8080
```
3. To test the service itself, the next command should be run from a different terminal window: 'curl -s http://localhost:8080/get' , which should return the output below: 
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
4. At the same time with each run of the `curl -s http://localhost:8080/get` command, you should see an additional line added at the terminal with each 
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