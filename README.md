# jenkins-kubernetes-maven-pvc

A persistence volume claim to use for caching maven repository when using jenkins pipelines over k8s.
The following is based on https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client

```bash
# RBAC
kubectl apply -f serviceaccount.yaml -f clusterrole.yaml -f clusterrolebinding.yaml

# Provisioner and storage class
kubectl apply -f deployment.yaml -f class.yaml

# Claim
kubectl apply -f maven-repo-claim.yaml
```

The files in this repo have values specific to our environment.
Our cluster supports RBAC v1beta1, so the RBAC yaml use that instead of v1.

A pod using this claim would look like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
spec:
  volumes:
    - name: maven-repo-storage
      persistentVolumeClaim:
       claimName: maven-repo
  containers:
  - name: my-container
    image: maven:3.5-jdk-8
    volumeMounts:
        - mountPath: "/root/.m2/repository"
          name: maven-repo-storage
```
