# Kubernetes Jenkins example with minimal RBAC role

## Requitments
* Kubernetes cluster with  enabled RBAC role
* `kubectl` or webui access to the kubernetes API
* Permissions to create service account role and bind privileges to this role
* `kubernetes-deployer` util
* `create-kubeconfig.sh` bash script or another method to create `kubeconfig` file


## Setup

* create namespace for `kubernetes-deployer`
```yml
---
apiVersion: v1
kind: Namespace
metadata:
  name: jenkins
```
* create service account role in this namespace
```yml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: jenkins
  name: jenkins
```
* create ClusterRole with minimal permissions (you should pay attention on the namespace in this part)
```yml
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: project-dev
  name: jenkins-dev
rules:
  - apiGroups: [""]
    resources:
    - namespaces
    verbs:
    - get
  - apiGroups: [""]
    resources:
    - services
    - secrets
    - configmaps
    - resourcequotas
    verbs:
    - list
  - apiGroups: ["apiextensions.k8s.io"]
    resources:
    - customresourcedefinitions
    verbs:
    - list
  - apiGroups: ["apps"]
    resources:
    - daemonsets
    - statefulsets
    verbs:
    - list
  - apiGroups: ["autoscaling" ]
    resources:
    - horizontalpodautoscalers
    verbs:
    - list
  - apiGroups: ["batch"]
    resources:
    - cronjobs
    - jobs
    verbs:
    - list
  - apiGroups: ["extensions"]
    resources:
    - daemonsets
    - ingresses
    - replicasets
    verbs:
    - list
  - apiGroups: ["policy"]
    resources:
    - poddisruptionbudgets
    verbs:
    - list
  - apiGroups: ["networking.k8s.io"]
    resources:
    - networkpolicies
    verbs:
    - list
```
* bind this role(roles) to the created service account role
```yml
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: jenkins-sa-dev
  namespace: project-dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: jenkins-dev
subjects:
- kind: ServiceAccount
  name: jenkins
  namespace: jenkins
```
* get kubeconfig  file via bash script to use with your CI\CD or just on your PC\laptop
`create-kubeconfig.sh jenkins -n jenkins`

* deploy your application through `kubernetes-deployer`

### PS
btw you can skip some part of this tutorial or add additional permissions to this role.

