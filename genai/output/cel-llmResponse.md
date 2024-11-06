# Output:

Here are the CEL policies for your requirements:

```yaml
policies:
  - apiVersion: v1alpha1
    kind: CELPolicy
    metadata:
      name: Check image with latest tag
      description: Deny Images with latest tag
      severity: Critical
      benchmark: XYZ
    rule: |
      !input.spec.template.spec.containers[0].image.endsWith('latest')
  - apiVersion: v1alpha1
    kind: CELPolicy
    metadata:
      name: Check replicas in a Deployment
      description: Ensure that Deployment has at least 3 replicas
      severity: High
      benchmark: XYZ
    rule: |
      input.kind == 'Deployment' ? input.spec.replicas >= 3 : true
```

The first policy `Check image with latest tag` checks if the image used in the container of a Kubernetes Deployment does not have the `latest` tag. The CEL expression `!input.spec.template.spec.containers[0].image.endsWith('latest')` checks if the image name does not end with `latest`. If it does, the policy will deny the deployment.

The second policy `Check replicas in a Deployment` ensures that the number of replicas in a Kubernetes Deployment is at least 3. The CEL expression `input.kind == 'Deployment' ? input.spec.replicas >= 3 : true` checks if the kind of the Kubernetes object is a Deployment and if so, it checks if the number of replicas is greater than or equal to 3. If the number of replicas is less than 3, the policy will deny the deployment. If the Kubernetes object is not a Deployment, the policy will allow it.