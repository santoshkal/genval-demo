Sure, here is a Cuelang definition that creates a Kubernetes Deployment and Service based on your requirements:

```cuelang
package k8s

import (
apps "k8s.io/api/apps/v1"
core "k8s.io/api/core/v1"
)

#Deployment: apps.#Deployment & {
apiVersion: "apps/v1"
kind: "Deployment"

metadata: {
    name: "text-deployment"
}

spec: apps.#DeploymentSpec & {
    replicas: 3

    template: {
        spec: core.#PodSpec & {
            containers: [{
                image: =~"^.*[^:latest]$"
                name: "nginx"

                securityContext: {
                    allowPrivilegeEscalation: false
                    runAsNonRoot: true
                    runAsUser: 1000
                }
            }]
        }
    }
}
}

#Service: core.#Service & {
apiVersion: "v1"
kind: "Service"

metadata: {
    name: "text-deployment-service"
}

spec: {
    type: "NodePort"
    ports: [{
        port: 80
        targetPort: 80
        nodePort: 31100
        protocol: "TCP"
    }]

    selector: {
        app: "text-deployment"
    }
}
}
```

This Cuelang definition creates a Kubernetes Deployment with the name `text-deployment`, running the `nginx` image (not using the `latest` tag). The Deployment has 3 replicas, and each pod runs as a non-root user with UID 1000. Privilege escalation is not allowed.

The Deployment is exposed by a Service of type NodePort, which listens on port 31100. The Service routes traffic to the `text-deployment` pods on port 80.