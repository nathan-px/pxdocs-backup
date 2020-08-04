---
title: Configure access to the PX-Backup UI
description: Configure access to the PX-Backup UI
keywords: upgrade,
weight: 10
hidesections: true
disableprevnext: true
scrollspy-container: false
type: common-landing
---

By default, the PX-Backup UI is configured <!-- need description of the current default config here. -->. If the standard PX-Backup UI configuration doesn't work with your cluster architecture, you can alter it so that it does. 

<!-- Revisit this whole description -->

## Expose the PX-Backup UI on ingress and access using HTTPS

You can configure PX-Backup to use HTTPS ... 

1. Create the following spec, entering your own secret and hosts based on your cluster's configuration:

    <!-- What do they need to enter here? I assume `test`, but what else? -->

    ```text
    cat <<< ' 
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    annotations:
        ingress.bluemix.net/redirect-to-https: "True"
        kubernetes.io/ingress.class: nginx
        nginx.ingress.kubernetes.io/x-forwarded-port: "443"
    name: px-backup-ui-ingress
    namespace: px-backup
    spec:
    rules:
    - host: px-backup-ui.test-1.us-east.containers.appdomain.cloud
        http:
        paths:
        - backend:
            serviceName: px-backup-ui
            servicePort: 80
            path: /
        - backend:
            serviceName: pxcentral-keycloak-http
            servicePort: 80
            path: /auth
    tls:
    - hosts:
        - px-backup-ui.test-1.us-east.containers.appdomain.cloud
        secretName: <your-secret>
    ' > /tmp/px-backup-ui-ingress.yaml
    ```

    {{<info>}}
**NOTE:** The `secretName` field -> `kubernetes TLS certificates secret` is only required when you want to terminate TLS on the host/domain.

Some examples:

* AKS: https://docs.microsoft.com/en-us/azure/aks/ingress-own-tls
* EKS: https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/
    {{<info>}}

2. Apply the spec:

    ```console
    kubectl apply -f /tmp/px-backup-ui-ingress.yaml
    ```

3. Retrieve the `INGRESS_ENDPOINT` using the `kubectl get ingress` command:

    ```console
    $ kubectl get ingress px-backup-ui-ingress --namespace px-backup -o jsonpath="{.status.loadBalancer.ingress[0].hostname}"
    ```

Once you've retrieved the `INGRESS_ENDPOINT`, you can use it to access the PX-Backup UI with the HTTPS scheme: `https://INGRESS_ENDPOINT`. Use the default credentials (admin/admin) to log in.

You can access the Keycloak UI at the `/auth` path: `https://INGRESS_ENDPOINT/auth`.

### Access the PX-Backup UI and Keycloak using node IP:

You can access PX-Backup from one of your node's IP addresses ...

1. Find the public/external IP (NODE_IP) of  any node in your current Kubernetes cluster.

    <!-- any instructions we can provide here? -->

2. Find the node port (NODE_PORT) of the `px-backup-ui` service: 

    <!-- any commands to find this? -->

Once you've found the node IP and port, you can combine them to access the PX-Backup UI: `http://NODE_IP:NODE_PORT`.

You can access the Keycloak UI at the `/auth` path: `http://NODE_IP:NODE_PORT/auth`.


### Access PX-Backup UI using Loadbalancer Endpoint:

You can also the access PX-Backup UI by navigating to the load balancer directly using either its host name or IP address.

1. Get the loadbalancer endpoint (LB_ENDPOINT) using one of the following commands:

   - Host:

    ```console
    $ kubectl get ingress --namespace {{ .Release.Namespace }} px-backup-ui -o jsonpath="{.status.loadBalancer.ingress[0].hostname}"`
    ```

   - IP:

    ```console
    $ kubectl get ingress --namespace {{ .Release.Namespace }} px-backup-ui -o jsonpath="{.status.loadBalancer.ingress[0].ip}"`
    ```
  
Once you've retrieved the load balancer endpoint, you can use it to access the PX-Backup UI: `http://LB_ENDPOINT`.

You can access the Keycloak UI at the `/auth` path: `http://LB_ENDPOINT/auth`.
